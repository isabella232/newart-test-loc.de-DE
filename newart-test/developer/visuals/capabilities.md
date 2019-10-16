---
title: Funktionen und Eigenschaften von Power BI-Visuals
description: In diesem Artikel werden die Funktionen und Eigenschaften von Power BI-Visuals beschrieben.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 8b14568eb3a9ebc75b8d94935aedc2ae07122ef4
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424205"
---
# <a name="capabilities-and-properties-of-power-bi-visuals"></a>Funktionen und Eigenschaften von Power BI-Visuals 

Sie verwenden Funktionen, um dem Host Informationen zum Visual zur Verfügung zu stellen. Alle Eigenschaften im Funktionsmodell sind `optional`.

Die Stammobjekte einer Visualfunktion sind `dataRoles`, `dataViewMappings` usw.

```json
{
    "dataRoles": [ ... ],
    "dataViewMappings": [ ... ],
    "objects":  { ... },
    "supportsHighlight": true|false,
    "advancedEditModeSupport": 0|1|2,
    "sorting": { ... }
}

```

## <a name="define-the-data-fields-that-your-visual-expects-dataroles"></a>Definieren der vom Visual erwarteten Datenfelder: dataRoles

Verwenden Sie `dataRoles` zum Definieren von Feldern, die an Daten gebunden werden können. `dataRoles` nimmt ein Array von `DataViewRole`-Objekten an, das alle erforderlichen Eigenschaften definiert.

### <a name="properties"></a>Eigenschaften

* **name**: Der interne Name dieses Datenfelds (muss eindeutig sein)
* **kind**: Die Art des Felds:
    * `Grouping`: Diskrete Werte, die zum Gruppieren von Measurefeldern verwendet werden
    * `Measure`: Numerische Datenwerte
    * `GroupingOrMeasure`: Werte, die als Gruppierung oder Measure verwendet werden können
* **displayName**: Der Name, den der Benutzer im Bereich **Eigenschaften** sieht.
* **description**: Eine kurze Beschreibung des Felds (optional)
* **requiredTypes**: Der für diese Datenrolle erforderliche Datentyp. Werte, die nicht übereinstimmen, werden auf NULL festgelegt (optional)
* **preferredTypes**: Der bevorzugte Datentyp für diese Datenrolle (optional)

### <a name="valid-data-types-in-requiredtypes-and-preferredtypes"></a>Gültige Datentypen in requiredTypes und preferredTypes

* **bool**: Boolescher Wert
* **integer**: Ein Wert in Form einer ganzen Zahl
* **numeric**: Ein numerischer Wert
* **text**: Ein Textwert
* **geography**: Geografische Daten

### <a name="example"></a>Beispiel

```json
"dataRoles": [
    {
        "displayName": "My Category Data",
        "name": "myCategory",
        "kind": "Grouping",
        "requiredTypes": [
            {
                "text": true
            },
            {
                "numeric": true
            },
            {
                "integer": true
            }
        ],
        "preferredTypes": [
            {
                "text": true
            }
        ]
    },
    {
        "displayName": "My Measure Data",
        "name": "myMeasure",
        "kind": "Measure",
        "requiredTypes": [
            {
                "integer": true
            },
            {
                "numeric": true
            }
        ],
        "preferredTypes": [
            {
                "integer": true
            }
        ]
    },
    {
        "displayNameKey": "Visual_Location",
        "name": "Locations",
        "kind": "Measure",
        "displayName": "Locations",
        "requiredTypes": [
            {
                "geography": {
                    "address": true
                }
            },
            {
                "geography": {
                    "city": true
                }
            },
            {
                "geography": {
                    "continent": true
                }
            },
            {
                "geography": {
                    "country": true
                }
            },
            {
                "geography": {
                    "county": true
                }
            },
            {
                "geography": {
                    "place": true
                }
            },
            {
                "geography": {
                    "postalCode": true
                }
            },
            {
                "geography": {
                    "region": true
                }
            },
            {
                "geography": {
                    "stateOrProvince": true
                }
            }
        ]
    }
]
```

Die vorangehenden Datenrollen erstellen die Felder, die in der folgenden Abbildung angezeigt werden:

![Datenrollenfelder](./media/data-role-display.png)

## <a name="define-how-you-want-the-data-mapped-dataviewmappings"></a>Definieren, wie die Daten zugeordnet werden sollen: dataViewMappings

Die Eigenschaft „DataViewMapping“ beschreibt die Beziehung zwischen den Datenrollen und ermöglicht es Ihnen, bedingte Anforderungen dafür festzulegen.

Die meisten Visuals verfügen über eine einzelne Zuordnung. Sie können aber auch mehrere dataViewMappings angeben. Jede gültige Zuordnung erzeugt eine Datenansicht. 

```json
"dataViewMappings": [
    {
        "conditions": [ ... ],
        "categorical": { ... },
        "table": { ... },
        "single": { ... },
        "matrix": { ... }
    }
]
```

Weitere Informationen finden Sie unter [Grundlegendes zur Zuordnung von Datenansichten in Power BI-Visuals](dataview-mappings.md).

## <a name="define-property-pane-options-objects"></a>Definieren von Optionen für den Eigenschaftenbereich: Objekte

Objekte beschreiben anpassbare Eigenschaften, die dem Visual zugeordnet sind. Jedes Objekt kann über mehrere Eigenschaften verfügen, und jeder Eigenschaft ist ein Typ zugeordnet. Mit Typen wird die Eigenschaft näher bestimmt. 

```json
"objects": {
    "myCustomObject": {
        "displayName": "My Object Name",
        "properties": { ... }
    }
}
```

Weitere Informationen finden Sie unter [Objekte und Eigenschaften von Power BI-Visuals](objects-properties.md).

## <a name="handle-partial-highlighting-supportshighlight"></a>Behandeln teilweiser Hervorhebungen: supportsHighlight

Standardmäßig ist dieser Wert auf `false` festgelegt. Dies bedeutet, dass Ihre Werte automatisch gefiltert werden, wenn etwas auf der Seite ausgewählt ist. Diese automatische Filterung aktualisiert wiederum Ihr Visual, sodass nur der ausgewählte Wert angezeigt wird. Wenn Sie die vollständigen Daten anzeigen möchten, aber nur die ausgewählten Elemente hervorheben, müssen Sie `supportsHighlight` in der *capabilities.json*-Datei auf `true` festlegen.

Weitere Informationen finden Sie unter [Hervorheben von Datenpunkten in Power BI-Visuals](highlight.md).

## <a name="handle-advanced-edit-mode-advancededitmodesupport"></a>Behandeln des erweiterten Bearbeitungsmodus: advancedEditModeSupport

Für ein Visual kann deklariert werden, dass es den Modus „Erweiterte Bearbeitung“ unterstützt. Ein Visual bietet standardmäßig keine Unterstützung für den Modus „Erweiterte Bearbeitung“, es sei denn, dies wurde explizit in der *capabilities.json*-Datei angegeben.

Weitere Informationen erhalten Sie unter [Modus „Erweiterte Bearbeitung“ in Power BI-Visuals](advanced-edit-mode.md).

## <a name="data-sorting-options-for-visual-sorting"></a>Datensortieroptionen für Visuals: Sortierung

Das Sortierverhalten eines Visuals kann in dessen Funktionen definiert werden. Die Änderung der Sortierreihenfolge wird von einem Visual standardmäßig nicht unterstützt, es sei denn, dies wurde explizit in der *capabilities.json*-Datei angegeben.

Weitere Informationen finden Sie unter [Sortieroptionen für Power BI-Visuals](sort-options.md).
