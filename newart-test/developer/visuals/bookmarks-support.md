---
title: Hinzufügen der Unterstützung von Lesezeichen für Power BI-Visuals
description: Power BI-Visuals unterstützen das Wechseln zwischen Lesezeichen.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: c19b67a59d0ecb4cbfbcf5ad8dd18886f440e164
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424225"
---
# <a name="add-bookmark-support-for-power-bi-visuals"></a>Hinzufügen der Unterstützung von Lesezeichen für Power BI-Visuals

Mithilfe von Lesezeichen in Power BI-Berichten lassen sich konfigurierte Ansichten einer Berichtsseite sowie der Auswahl- und Filterzustand des Visuals erfassen. Allerdings sind zusätzliche Aktionen von Seiten der Power BI-Visuals nötig, damit Lesezeichen unterstützt werden und angemessen auf Änderungen reagiert werden kann.

Weitere Informationen zu Lesezeichen finden Sie unter [Verwenden von Lesezeichen zum Teilen von Erkenntnissen und Erstellen von Präsentationen in Power BI](https://docs.microsoft.com/power-bi/desktop-bookmarks).

## <a name="report-bookmarks-support-in-your-visual"></a>Unterstützung von Berichtslesezeichen in Visuals

Wenn Ihr Visual mit anderen Visuals interagiert, Datenpunkte auswählt oder andere Visuals filtert, müssen Sie den Zustand mithilfe von Eigenschaften wiederherstellen.

## <a name="add-report-bookmarks-support"></a>Hinzufügen von Unterstützung für Berichtslesezeichen

1. Installieren (oder aktualisieren) Sie das erforderliche Hilfsprogramm [powerbi-visuals-utils-interactivityutils](https://github.com/Microsoft/PowerBI-visuals-utils-interactivityutils/), Version 3.0.0 oder höher. Es enthält zusätzliche Klassen für die Bearbeitung der Zustands- oder Filterauswahl. Das Hilfsprogramm wird zudem für filterbezogene Visuals und alle Visuals vorausgesetzt, die `InteractivityService` verwenden.

2. Aktualisieren Sie die Visual-API auf Version 1.11.0, damit Sie `registerOnSelectCallback` in einer Instanz von `SelectionManager` verwenden können. Das Hilfsprogramm wird für nicht filterbezogene Visuals vorausgesetzt, die den normalen `SelectionManager` anstelle des `InteractivityService` verwenden.

### <a name="how-power-bi-visuals-interact-with-power-bi-in-report-bookmarks"></a>So interagieren Power BI-Visuals mit Power BI im Berichtslesezeichen

Betrachten wir das folgende Szenario: Sie möchten mehrere Lesezeichen auf der Berichtsseite mit einem anderen Auswahlzustand in jedem Lesezeichen erstellen.

Wählen Sie zunächst einen Datenpunkt in Ihrem Visual aus. Das Visual interagiert mit Power BI und anderen Visuals, indem es die Auswahl an den Host übergibt. Klicken Sie anschließend im Bereich **Lesezeichen** auf **Hinzufügen**, woraufhin Power BI die aktuelle Auswahl für das neue Lesezeichen speichert.

Dieser Vorgang wird wiederholt, wenn Sie die Auswahl ändern und neue Lesezeichen hinzufügen. Nachdem Sie die Lesezeichen erstellt haben, können Sie zwischen diesen wechseln.

Wenn Sie ein Lesezeichen auswählen, stellt Power BI den gespeicherten Filter- oder Auswahlzustand wieder her und übergibt ihn an die Visuals. Andere Visuals werden hervorgehoben oder gemäß dem Zustand gefiltert, der im Lesezeichen gespeichert ist. Der Power BI-Host ist für die Aktionen verantwortlich. Ihr Visual hat die Aufgabe, den neuen Auswahlzustand (z. B. Änderungen der Farben der gerenderten Datenpunkte) korrekt wiederzugeben.

Der neue Auswahlzustand wird dem Visual über die `update`-Methode mitgeteilt. Das Argument `options` enthält die besondere Eigenschaft `options.jsonFilters`. Die JSONFilter-Eigenschaft kann `Advanced Filter` und `Tuple Filter` enthalten.

Das Visual muss die Filterwerte wiederherstellen, um den entsprechenden Zustand des Visuals für das ausgewählte Lesezeichen anzuzeigen. Wenn das Visual nur die Auswahl verwendet, können Sie alternativ auch die vom besonderen Aufruf der Rückruffunktion registrierte Methode `registerOnSelectCallback` des ISelectionManager verwenden.

### <a name="visuals-with-selection"></a>Visuals mit einer Auswahl

Wenn das Visual durch eine [Auswahl](https://github.com/Microsoft/PowerBI-visuals/blob/master/Tutorial/Selection.md) mit anderen Visuals interagiert, haben Sie zwei Möglichkeiten, Lesezeichen hinzuzufügen:

* Wenn das Visual nicht bereits [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) verwendet hat, können Sie die Methode `FilterManager.restoreSelectionIds` verwenden.

* Wenn das Visual bereits [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) verwendet, um die Auswahl zu verwalten, sollten Sie die Methode `applySelectionFromFilter` in der Instanz von `InteractivityService` verwenden.

#### <a name="use-iselectionmanagerregisteronselectcallback"></a>Verwenden von ISelectionManager.registerOnSelectCallback

Wenn Sie auf ein Lesezeichen klicken, ruft Power BI die Methode `callback` des Visuals mit der entsprechenden Auswahl auf. 

```typescript
this.selectionManager.registerOnSelectCallback(
    (ids: ISelectionId[]) => {
        //called when a selection was set by Power BI
    });
);
```

Angenommen, Sie verfügen über einen Datenpunkt in Ihrem Visual, der in der Methode [visualTransform](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/blob/master/src/barChart.ts#L74) erstellt wurde.

Dabei sieht `datapoints` wie folgt aus:

```typescript
visualDataPoints.push({
    category: categorical.categories[0].values[i],
    color: getCategoricalObjectValue<Fill>(categorical.categories[0], i, 'colorSelector', 'fill', defaultColor).solid.color,
    selectionId: host.createSelectionIdBuilder()
        .withCategory(categorical.categories[0], i)
        .createSelectionId(),
    selected: false
});
```

Sie verfügen jetzt über `visualDataPoints` als Datenpunkte, und das Array `ids` wurde an die Funktion `callback` übergeben.

An diesem Punkt sollte das Visual das Array `ISelectionId[]` mit der Auswahl im Array `visualDataPoints` vergleichen und die entsprechenden Datenpunkte als ausgewählt markieren.

```typescript
this.selectionManager.registerOnSelectCallback(
    (ids: ISelectionId[]) => {
        visualDataPoints.forEach(dataPoint => {
            ids.forEach(bookmarkSelection => {
                if (bookmarkSelection.equals(dataPoint.selectionId)) {
                    dataPoint.selected = true;
                }
            });
        });
    });
);
```

Nach dem Aktualisieren der Datenpunkte wird der aktuelle Auswahlzustand angezeigt, der im Objekt `filter` gespeichert ist. Wenn die Datenpunkte anschließend gerendert werden, entspricht der Auswahlzustand des benutzerdefinierten Visuals dem Zustand des Lesezeichens.

### <a name="use-interactivityservice-for-control-selections-in-the-visual"></a>Verwenden von InteractivityService zur Steuerelementauswahl im Visual

Wenn Ihr Visual `InteractivityService` verwendet, sind keine weiteren Aktionen erforderlich, damit Lesezeichen in Ihren Visuals unterstützt werden.

Wenn Sie Lesezeichen auswählen, verarbeitet das Hilfsprogramm den Auswahlzustand des Visuals.

### <a name="visuals-with-a-filter"></a>Visuals mit einem Filter

Nehmen wir an, dass vom Visual ein Datenfilter nach Datumsbereich erstellt wird. `startDate` und `endDate` dienen jeweils als Anfangs- und Enddatum.

Durch das Visual wird ein erweiterter Filter erstellt, und die Hostmethode `applyJsonFilter` wird aufgerufen, um Daten nach relevanten Bedingungen zu filtern.

Das Ziel ist die Tabelle, die zum Filtern verwendet wird.

```typescript
import { AdvancedFilter } from "powerbi-models";

const filter: IAdvancedFilter = new AdvancedFilter(
    target,
    "And",
    {
        operator: "GreaterThanOrEqual",
        value: startDate
            ? startDate.toJSON()
            : null
    },
    {
        operator: "LessThanOrEqual",
        value: endDate
            ? endDate.toJSON()
            : null
    });

this.host.applyJsonFilter(
    filter,
    "general",
    "filter",
    (startDate && endDate)
        ? FilterAction.merge
        : FilterAction.remove
);
```

Sobald ein Benutzer auf ein Lesezeichen klickt, empfängt das benutzerdefinierte Visual einen `update`-Aufruf.

Der Objektfilter muss vom benutzerdefinierten Visual überprüft werden:

```typescript
const filter: IAdvancedFilter = FilterManager.restoreFilter(
    && options.jsonFilters
    && options.jsonFilters[0] as any
) as IAdvancedFilter;
```

Wenn das Objekt `filter` nicht NULL ist, muss das Visual Filterbedingungen des Objekts wiederherstellen:

```typescript
const jsonFilters: AdvancedFilter = this.options.jsonFilters as AdvancedFilter[];

if (jsonFilters
    && jsonFilters[0]
    && jsonFilters[0].conditions
    && jsonFilters[0].conditions[0]
    && jsonFilters[0].conditions[1]
) {
    const startDate: Date = new Date(`${jsonFilters[0].conditions[0].value}`);
    const endDate: Date = new Date(`${jsonFilters[0].conditions[1].value}`);

    // apply restored conditions
} else {
    // apply default settings
}
```

Danach sollte das Visual seinen internen Zustand ändern, um die aktuellen Bedingungen widerzuspiegeln. Der interne Zustand umfasst die Datenpunkte und Visualisierungsobjekte (Linien, Rechtecke usw.).

> [!IMPORTANT]
> Im Szenario mit den Berichtslesezeichen sollte das Visual nicht `applyJsonFilter` aufrufen, um die anderen Visuals zu filtern. Diese werden bereits von Power BI gefiltert.

Das Visual für den Zeitachsenslicer ändert die Bereichsauswahl gemäß den jeweiligen Datenbereichen.

Weitere Informationen finden Sie im [Zeitachsenslicer-Repository](https://github.com/Microsoft/powerbi-visuals-timeline/commit/606f1152f59f82b5b5a367ff3b117372d129e597?diff=unified#diff-b6ef9a9ac3a3225f8bd0de84bee0a0df).

### <a name="filter-the-state-to-save-visual-properties-in-bookmarks"></a>Filtern des Zustands, um Visualeigenschaften in Lesezeichen zu speichern

Durch die `filterState`-Eigenschaft wird eine Eigenschaft zum Bestandteil der Filterung. Das Visual ist in der Lage, verschiedene Werte in Lesezeichen zu speichern.

Wenn Sie den Eigenschaftswert als Filterzustand speichern möchten, markieren Sie die Objekteigenschaft in der Datei *capabilities.json* als `"filterState": true`.

Beispielsweise speichert der Zeitachsenslicer die Eigenschaftswerte für `Granularity` in einem Filter. Dadurch kann sich die aktuelle Granularität ändern, während Sie die Lesezeichen ändern.

Weitere Informationen finden Sie im [Zeitachsenslicer-Repository](https://github.com/microsoft/powerbi-visuals-timeline/commit/8b7d82dd23cd2bd71817f1bc5d1e1732347a185e#diff-290828b604cfa62f1cb310f2e90c52fdR334).
