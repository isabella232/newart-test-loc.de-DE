---
title: Die API für visuelle Filter für Power BI-Visuals
description: In diesem Artikel wird erläutert, wie Power BI-Visuals andere Visuals filtern können.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 98ebc87cf5a6b7bf8f0b8b88d4ff498edfd5bf9a
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424545"
---
# <a name="the-visual-filters-api-in-power-bi-visuals"></a>Die API für visuelle Filter für Power BI-Visuals

Mit der API für visuelle Filter können Sie Daten in Power BI-Visuals filtern. Der Hauptunterschied zu anderen Auswahlen besteht darin, dass andere Visuals in beliebiger Weise gefiltert werden, obwohl die Hervorhebung von einem anderen Visual unterstützt wird.

Ein Visual sollte ein `filter`-Objekt im `general`-Abschnitt des Codes in *capabilities.json* enthalten, um die Filterung für das Visual zu ermöglichen.

```json
"objects": {
        "general": {
            "displayName": "General",
            "displayNameKey": "formattingGeneral",
            "properties": {
                "filter": {
                    "type": {
                        "filter": true
                    }
                }
            }
        }
    }
```

Die Schnittstellen der API für visuelle Filter stehen im Paket [powerbi-models](https://www.npmjs.com/package/powerbi-models) zur Verfügung. Das Paket enthält auch Klassen zum Erstellen von Filterinstanzen.

```cmd
npm install powerbi-models --save
```

Wenn Sie eine ältere Version der Tools verwenden (früher als 3.x.x), sollten Sie `powerbi-models` in das Visualpaket einschließen. Weitere Informationen finden Sie in der Kurzanleitung [Hinzufügen der API für erweiterte Filter zum benutzerdefinierten Visual](https://github.com/Microsoft/powerbi-visuals-sampleslicer/blob/master/doc/AddingAdvancedFilterAPI.md).

Alle Filter erweitern wie im folgenden Code gezeigt die `IFilter`-Schnittstelle:

```typescript
export interface IFilter {
    $schema: string;
    target: IFilterTarget;
}
```
Hier gilt:
* `target` ist die Tabellenspalte in der Datenquelle.

## <a name="the-basic-filter-api"></a>API für einfache Filter

Die einfache Filterschnittstelle wird im folgenden Code gezeigt:

```typescript
export interface IBasicFilter extends IFilter {
    operator: BasicFilterOperators;
    values: (string | number | boolean)[];
}
```

Hier gilt:
* `operator` ist eine Enumeration mit den Werten *In*, *NotIn* und *All*.
* `values` sind Werte für die Bedingung.

Beispiel für einen einfachen Filter:

```typescript
let basicFilter = {
    target: {
        column: "Col1"
    },
    operator: "In",
    values: [1,2,3]
}
```

Dieser Filter bedeutet: „Gib alle Zeilen zurück, in denen `col1` den Werten „1“, „2“ oder „3“ entspricht“.

Im Folgenden die Entsprechung in SQL:

```sql
SELECT * FROM table WHERE col1 IN ( 1 , 2 , 3 )
```

Sie können die BasicFilter-Klasse in `powerbi-models` verwenden, um einen Filter zu erstellen.

Wenn Sie eine ältere Version des Tools verwenden, sollten Sie wie im folgenden Code gezeigt eine models-Instanz im window-Objekt mithilfe von `window['powerbi-models']` abrufen:

```javascript
let categories: DataViewCategoricalColumn = this.dataView.categorical.categories[0];

let target: IFilterColumnTarget = {
    table: categories.source.queryName.substr(0, categories.source.queryName.indexOf('.')),
    column: categories.source.displayName
};

let values = [ 1, 2, 3 ];

let filter: IBasicFilter = new window['powerbi-models'].BasicFilter(target, "In", values);
```

Das Visual ruft den Filter mithilfe der applyJsonFilter()-Methode für die Hostschnittstelle „IVisualHost“ auf, die im Konstruktor für das Visual bereitgestellt wird.

```typescript
visualHost.applyJsonFilter(filter, "general", "filter", FilterAction.merge);
```

## <a name="the-advanced-filter-api"></a>API für erweiterte Filter

Die [erweiterte Filter-API](https://github.com/Microsoft/powerbi-models) unterstützt die komplexe Datenpunktauswahl über mehrere Visuals hinweg und die Filterung von Abfragen, die auf mehreren Kriterien basieren (z. B. *LessThan*, *Contains*, *Is*, *IsBlank* usw.).

Der Filter wurde in der Visuals-API 1.7.0 eingeführt.

Die erweiterte Filter-API erfordert ebenfalls `target` mit Namen für `table` und `column`. Jedoch lauten die Operatoren der erweiterten Filter-API *And* und *Or* („Und“ und „Oder“). 

Außerdem nutzt der Filter Bedingungen anstelle von Werten für die Schnittstelle:

```typescript
interface IAdvancedFilterCondition {
    value: (string | number | boolean);
    operator: AdvancedFilterConditionOperators;
}
```

Der `operator`-Parameter kann die folgenden Bedingungsoperatoren aufweisen: *None*, *LessThan*, *LessThanOrEqual*, *GreaterThan*, *GreaterThanOrEqual*, *Contains*, *DoesNotContain*, *StartsWith*, *DoesNotStartWith*, *Is*, *IsNot*, *IsBlank* und „IsNotBlank“.

```javascript
let categories: DataViewCategoricalColumn = this.dataView.categorical.categories[0];

let target: IFilterColumnTarget = {
    table: categories.source.queryName.substr(0, categories.source.queryName.indexOf('.')), // table
    column: categories.source.displayName // col1
};

let conditions: IAdvancedFilterCondition[] = [];

conditions.push({
    operator: "LessThan",
    value: 0
});

let filter: IAdvancedFilter = new window['powerbi-models'].AdvancedFilter(target, "And", conditions);

// invoke the filter
visualHost.applyJsonFilter(filter, "general", "filter", FilterAction.merge);
```

Im Folgenden die Entsprechung in SQL:

```sql
SELECT * FROM table WHERE col1 < 0;
```

Den vollständigen Beispielcode für die Verwendung der erweiterten Filter-API finden Sie im [Repository des Beispieldatenschnittvisuals](https://github.com/Microsoft/powerbi-visuals-sampleslicer).

## <a name="the-tuple-filter-api-multi-column-filter"></a>Tupelfilter-API (Filter für mehrere Spalten)

Die Tupelfilter-API wurde in der Visuals-API 2.3.0 eingeführt. Sie ähnelt der API für einfache Filter, ermöglicht Ihnen aber das Definieren von Bedingungen für mehrere Spalten und Tabellen.

Die Filterschnittstelle wird im folgenden Code gezeigt: 

```typescript
interface ITupleFilter extends IFilter {
    $schema: string;
    filterType: FilterType;
    operator: TupleFilterOperators;
    target: ITupleFilterTarget;
    values: TupleValueType[];
}
```

Hier gilt:
* `target` ist ein Array von Spalten mit Tabellennamen:

    ```typescript
    declare type ITupleFilterTarget = IFilterTarget[];
    ```

  Der Filter kann auf Spalten aus verschiedenen Tabellen angewendet werden.

* `$schema` ist http://powerbi.com/product/schema#tuple.

* `filterType` ist *FilterType.Tuple*.

* `operator` erlaubt die Verwendung nur im Operator *In*.

* `values` ist ein Array von Werttupeln, und jedes Tupel stellt eine zulässige Kombination der Zielspaltenwerte dar. 

```typescript
declare type TupleValueType = ITupleElementValue[];

interface ITupleElementValue {
    value: PrimitiveValueType
}
```

Vollständiges Beispiel:

```typescript
let target: ITupleFilterTarget = [
    {
        table: "DataTable",
        column: "Team"
    },
    {
        table: "DataTable",
        column: "Value"
    }
];

let values = [
    [
        // the first column combination value (or the column tuple/vector value) that the filter will pass through
        {
            value: "Team1" // the value for the `Team` column of the `DataTable` table
        },
        {
            value: 5 // the value for the `Value` column of the `DataTable` table
        }
    ],
    [
        // the second column combination value (or the column tuple/vector value) that the filter will pass through
        {
            value: "Team2" // the value for `Team` column of `DataTable` table
        },
        {
            value: 6 // the value for `Value` column of `DataTable` table
        }
    ]
];

let filter: ITupleFilter = {
    $schema: "http://powerbi.com/product/schema#tuple",
    filterType: FilterType.Tuple,
    operator: "In",
    target: target,
    values: values
}

// invoke the filter
visualHost.applyJsonFilter(filter, "general", "filter", FilterAction.merge);
```

> [!IMPORTANT]
> Die Reihenfolge der Spaltennamen und Bedingungswerte ist sensibel.

Im Folgenden die Entsprechung in SQL:

```sql
SELECT * FROM DataTable WHERE ( Team = "Team1" AND Value = 5 ) OR ( Team = "Team2" AND Value = 6 );
```  

## <a name="restore-the-json-filter-from-the-data-view"></a>Wiederherstellen der JSON-Filter aus der Datenansicht

Ab API-Version 2.2 können Sie die JSON-Filter von *VisualUpdateOptions* wie im folgenden Code gezeigt wiederherstellen:

```typescript
export interface VisualUpdateOptions extends extensibility.VisualUpdateOptions {
    viewport: IViewport;
    dataViews: DataView[];
    type: VisualUpdateType;
    viewMode?: ViewMode;
    editMode?: EditMode;
    operationKind?: VisualDataChangeOperationKind;
    jsonFilters?: IFilter[];
}
```

Wenn Sie zwischen Lesezeichen wechseln, ruft Power BI die `update`-Methode des Visuals auf, und das Visual ruft ein entsprechendes `filter`-Objekt ab. Weitere Informationen finden Sie unter [Hinzufügen von Lesezeichenunterstützung für Power BI-Visuals](bookmarks-support.md).

### <a name="sample-json-filter"></a>Beispiel für JSON-Filter

In der folgenden Abbildung wird ein Beispiel für JSON-Filtercode veranschaulicht:

![JSON-Filtercode](./media/json-filter.png)

### <a name="clear-the-json-filter"></a>Bereinigen des JSON-Filters

Die Filter-API interpretiert den Filterwert `null` als Befehl zum *Zurücksetzen* oder *Löschen*.

```typescript
// invoke the filter
visualHost.applyJsonFilter(null, "general", "filter", FilterAction.merge);
```
