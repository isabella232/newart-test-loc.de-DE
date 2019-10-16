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
# <a name="add-bookmark-support-for-power-bi-visuals"></a><span data-ttu-id="7a559-103">Hinzufügen der Unterstützung von Lesezeichen für Power BI-Visuals</span><span class="sxs-lookup"><span data-stu-id="7a559-103">Add bookmark support for Power BI visuals</span></span>

<span data-ttu-id="7a559-104">Mithilfe von Lesezeichen in Power BI-Berichten lassen sich konfigurierte Ansichten einer Berichtsseite sowie der Auswahl- und Filterzustand des Visuals erfassen.</span><span class="sxs-lookup"><span data-stu-id="7a559-104">With Power BI report bookmarks, you can capture a configured view of a report page, the selection state, and the filtering state of the visual.</span></span> <span data-ttu-id="7a559-105">Allerdings sind zusätzliche Aktionen von Seiten der Power BI-Visuals nötig, damit Lesezeichen unterstützt werden und angemessen auf Änderungen reagiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="7a559-105">But it requires additional action from the Power BI visuals side to support the bookmark and react correctly to changes.</span></span>

<span data-ttu-id="7a559-106">Weitere Informationen zu Lesezeichen finden Sie unter [Verwenden von Lesezeichen zum Teilen von Erkenntnissen und Erstellen von Präsentationen in Power BI](https://docs.microsoft.com/power-bi/desktop-bookmarks).</span><span class="sxs-lookup"><span data-stu-id="7a559-106">For more information about bookmarks, see [Use bookmarks to share insights and build stories in Power BI](https://docs.microsoft.com/power-bi/desktop-bookmarks).</span></span>

## <a name="report-bookmarks-support-in-your-visual"></a><span data-ttu-id="7a559-107">Unterstützung von Berichtslesezeichen in Visuals</span><span class="sxs-lookup"><span data-stu-id="7a559-107">Report bookmarks support in your visual</span></span>

<span data-ttu-id="7a559-108">Wenn Ihr Visual mit anderen Visuals interagiert, Datenpunkte auswählt oder andere Visuals filtert, müssen Sie den Zustand mithilfe von Eigenschaften wiederherstellen.</span><span class="sxs-lookup"><span data-stu-id="7a559-108">If your visual interacts with other visuals, selects data points, or filters other visuals, you need to restore the state from properties.</span></span>

## <a name="add-report-bookmarks-support"></a><span data-ttu-id="7a559-109">Hinzufügen von Unterstützung für Berichtslesezeichen</span><span class="sxs-lookup"><span data-stu-id="7a559-109">Add report bookmarks support</span></span>

1. <span data-ttu-id="7a559-110">Installieren (oder aktualisieren) Sie das erforderliche Hilfsprogramm [powerbi-visuals-utils-interactivityutils](https://github.com/Microsoft/PowerBI-visuals-utils-interactivityutils/), Version 3.0.0 oder höher.</span><span class="sxs-lookup"><span data-stu-id="7a559-110">Install (or update) the required utility, [powerbi-visuals-utils-interactivityutils](https://github.com/Microsoft/PowerBI-visuals-utils-interactivityutils/) version 3.0.0 or later.</span></span> <span data-ttu-id="7a559-111">Es enthält zusätzliche Klassen für die Bearbeitung der Zustands- oder Filterauswahl.</span><span class="sxs-lookup"><span data-stu-id="7a559-111">It contains additional classes to manipulate with the state selection or filter.</span></span> <span data-ttu-id="7a559-112">Das Hilfsprogramm wird zudem für filterbezogene Visuals und alle Visuals vorausgesetzt, die `InteractivityService` verwenden.</span><span class="sxs-lookup"><span data-stu-id="7a559-112">It's required for filter visuals and any visual that uses `InteractivityService`.</span></span>

2. <span data-ttu-id="7a559-113">Aktualisieren Sie die Visual-API auf Version 1.11.0, damit Sie `registerOnSelectCallback` in einer Instanz von `SelectionManager` verwenden können.</span><span class="sxs-lookup"><span data-stu-id="7a559-113">Update the visual API to version 1.11.0 to use `registerOnSelectCallback` in an instance of `SelectionManager`.</span></span> <span data-ttu-id="7a559-114">Das Hilfsprogramm wird für nicht filterbezogene Visuals vorausgesetzt, die den normalen `SelectionManager` anstelle des `InteractivityService` verwenden.</span><span class="sxs-lookup"><span data-stu-id="7a559-114">It's required for non-filter visuals that use the plain `SelectionManager` rather than `InteractivityService`.</span></span>

### <a name="how-power-bi-visuals-interact-with-power-bi-in-report-bookmarks"></a><span data-ttu-id="7a559-115">So interagieren Power BI-Visuals mit Power BI im Berichtslesezeichen</span><span class="sxs-lookup"><span data-stu-id="7a559-115">How Power BI visuals interact with Power BI in report bookmarks</span></span>

<span data-ttu-id="7a559-116">Betrachten wir das folgende Szenario: Sie möchten mehrere Lesezeichen auf der Berichtsseite mit einem anderen Auswahlzustand in jedem Lesezeichen erstellen.</span><span class="sxs-lookup"><span data-stu-id="7a559-116">Let's consider the following scenario: you want to create several bookmarks on the report page, with a different selection state in each bookmark.</span></span>

<span data-ttu-id="7a559-117">Wählen Sie zunächst einen Datenpunkt in Ihrem Visual aus.</span><span class="sxs-lookup"><span data-stu-id="7a559-117">First, you select a data point in your visual.</span></span> <span data-ttu-id="7a559-118">Das Visual interagiert mit Power BI und anderen Visuals, indem es die Auswahl an den Host übergibt.</span><span class="sxs-lookup"><span data-stu-id="7a559-118">The visual interacts with Power BI and other visuals by passing selections to the host.</span></span> <span data-ttu-id="7a559-119">Klicken Sie anschließend im Bereich **Lesezeichen** auf **Hinzufügen**, woraufhin Power BI die aktuelle Auswahl für das neue Lesezeichen speichert.</span><span class="sxs-lookup"><span data-stu-id="7a559-119">You then select **Add** in the **Bookmark** pane, and Power BI saves the current selections for the new bookmark.</span></span>

<span data-ttu-id="7a559-120">Dieser Vorgang wird wiederholt, wenn Sie die Auswahl ändern und neue Lesezeichen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="7a559-120">It happens repeatedly as you change the selection and add new bookmarks.</span></span> <span data-ttu-id="7a559-121">Nachdem Sie die Lesezeichen erstellt haben, können Sie zwischen diesen wechseln.</span><span class="sxs-lookup"><span data-stu-id="7a559-121">After you create the bookmarks, you can switch between them.</span></span>

<span data-ttu-id="7a559-122">Wenn Sie ein Lesezeichen auswählen, stellt Power BI den gespeicherten Filter- oder Auswahlzustand wieder her und übergibt ihn an die Visuals.</span><span class="sxs-lookup"><span data-stu-id="7a559-122">When you select a bookmark, Power BI restores the saved filter or selection state and passes it to the visuals.</span></span> <span data-ttu-id="7a559-123">Andere Visuals werden hervorgehoben oder gemäß dem Zustand gefiltert, der im Lesezeichen gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="7a559-123">Other visuals are highlighted or filtered according to the state that's stored in the bookmark.</span></span> <span data-ttu-id="7a559-124">Der Power BI-Host ist für die Aktionen verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="7a559-124">The Power BI host is responsible for the actions.</span></span> <span data-ttu-id="7a559-125">Ihr Visual hat die Aufgabe, den neuen Auswahlzustand (z. B. Änderungen der Farben der gerenderten Datenpunkte) korrekt wiederzugeben.</span><span class="sxs-lookup"><span data-stu-id="7a559-125">Your visual is responsible for correctly reflecting the new selection state (for example, for changing the colors of rendered data points).</span></span>

<span data-ttu-id="7a559-126">Der neue Auswahlzustand wird dem Visual über die `update`-Methode mitgeteilt.</span><span class="sxs-lookup"><span data-stu-id="7a559-126">The new selection state is communicated to the visual through the `update` method.</span></span> <span data-ttu-id="7a559-127">Das Argument `options` enthält die besondere Eigenschaft `options.jsonFilters`.</span><span class="sxs-lookup"><span data-stu-id="7a559-127">The `options` argument contains a special property, `options.jsonFilters`.</span></span> <span data-ttu-id="7a559-128">Die JSONFilter-Eigenschaft kann `Advanced Filter` und `Tuple Filter` enthalten.</span><span class="sxs-lookup"><span data-stu-id="7a559-128">Its JSONFilter property can contain `Advanced Filter` and `Tuple Filter`.</span></span>

<span data-ttu-id="7a559-129">Das Visual muss die Filterwerte wiederherstellen, um den entsprechenden Zustand des Visuals für das ausgewählte Lesezeichen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="7a559-129">The visual should restore the filter values to display the corresponding state of the visual for the selected bookmark.</span></span> <span data-ttu-id="7a559-130">Wenn das Visual nur die Auswahl verwendet, können Sie alternativ auch die vom besonderen Aufruf der Rückruffunktion registrierte Methode `registerOnSelectCallback` des ISelectionManager verwenden.</span><span class="sxs-lookup"><span data-stu-id="7a559-130">Or, if the visual uses selections only, you can use the special callback function call that's registered as the `registerOnSelectCallback` method of ISelectionManager.</span></span>

### <a name="visuals-with-selection"></a><span data-ttu-id="7a559-131">Visuals mit einer Auswahl</span><span class="sxs-lookup"><span data-stu-id="7a559-131">Visuals with Selection</span></span>

<span data-ttu-id="7a559-132">Wenn das Visual durch eine [Auswahl](https://github.com/Microsoft/PowerBI-visuals/blob/master/Tutorial/Selection.md) mit anderen Visuals interagiert, haben Sie zwei Möglichkeiten, Lesezeichen hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="7a559-132">If your visual interacts with other visuals by using [Selection](https://github.com/Microsoft/PowerBI-visuals/blob/master/Tutorial/Selection.md), you can add bookmarks in either of two ways:</span></span>

* <span data-ttu-id="7a559-133">Wenn das Visual nicht bereits [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) verwendet hat, können Sie die Methode `FilterManager.restoreSelectionIds` verwenden.</span><span class="sxs-lookup"><span data-stu-id="7a559-133">If the visual hasn't already used [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md), you can use the `FilterManager.restoreSelectionIds` method.</span></span>

* <span data-ttu-id="7a559-134">Wenn das Visual bereits [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) verwendet, um die Auswahl zu verwalten, sollten Sie die Methode `applySelectionFromFilter` in der Instanz von `InteractivityService` verwenden.</span><span class="sxs-lookup"><span data-stu-id="7a559-134">If the visual already uses [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) to manage selections, you should use the `applySelectionFromFilter` method in the instance of `InteractivityService`.</span></span>

#### <a name="use-iselectionmanagerregisteronselectcallback"></a><span data-ttu-id="7a559-135">Verwenden von ISelectionManager.registerOnSelectCallback</span><span class="sxs-lookup"><span data-stu-id="7a559-135">Use ISelectionManager.registerOnSelectCallback</span></span>

<span data-ttu-id="7a559-136">Wenn Sie auf ein Lesezeichen klicken, ruft Power BI die Methode `callback` des Visuals mit der entsprechenden Auswahl auf.</span><span class="sxs-lookup"><span data-stu-id="7a559-136">When you select a bookmark, Power BI calls the `callback` method of the visual with the corresponding selections.</span></span> 

```typescript
this.selectionManager.registerOnSelectCallback(
    (ids: ISelectionId[]) => {
        //called when a selection was set by Power BI
    });
);
```

<span data-ttu-id="7a559-137">Angenommen, Sie verfügen über einen Datenpunkt in Ihrem Visual, der in der Methode [visualTransform](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/blob/master/src/barChart.ts#L74) erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="7a559-137">Let's assume that you have a data point in your visual that was created in the [visualTransform](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/blob/master/src/barChart.ts#L74) method.</span></span>

<span data-ttu-id="7a559-138">Dabei sieht `datapoints` wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="7a559-138">And `datapoints` looks like:</span></span>

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

<span data-ttu-id="7a559-139">Sie verfügen jetzt über `visualDataPoints` als Datenpunkte, und das Array `ids` wurde an die Funktion `callback` übergeben.</span><span class="sxs-lookup"><span data-stu-id="7a559-139">You now have `visualDataPoints` as your data points and the `ids` array passed to the `callback` function.</span></span>

<span data-ttu-id="7a559-140">An diesem Punkt sollte das Visual das Array `ISelectionId[]` mit der Auswahl im Array `visualDataPoints` vergleichen und die entsprechenden Datenpunkte als ausgewählt markieren.</span><span class="sxs-lookup"><span data-stu-id="7a559-140">At this point, the visual should compare the `ISelectionId[]` array with the selections in your `visualDataPoints` array and mark the corresponding data points as selected.</span></span>

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

<span data-ttu-id="7a559-141">Nach dem Aktualisieren der Datenpunkte wird der aktuelle Auswahlzustand angezeigt, der im Objekt `filter` gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="7a559-141">After you update the data points, they'll reflect the current selection state that's stored in the `filter` object.</span></span> <span data-ttu-id="7a559-142">Wenn die Datenpunkte anschließend gerendert werden, entspricht der Auswahlzustand des benutzerdefinierten Visuals dem Zustand des Lesezeichens.</span><span class="sxs-lookup"><span data-stu-id="7a559-142">Then, when the data points are rendered, the custom visual's selection state will match the state of the bookmark.</span></span>

### <a name="use-interactivityservice-for-control-selections-in-the-visual"></a><span data-ttu-id="7a559-143">Verwenden von InteractivityService zur Steuerelementauswahl im Visual</span><span class="sxs-lookup"><span data-stu-id="7a559-143">Use InteractivityService for control selections in the visual</span></span>

<span data-ttu-id="7a559-144">Wenn Ihr Visual `InteractivityService` verwendet, sind keine weiteren Aktionen erforderlich, damit Lesezeichen in Ihren Visuals unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="7a559-144">If your visual uses `InteractivityService`, you don't need any additional actions to support the bookmarks in your visual.</span></span>

<span data-ttu-id="7a559-145">Wenn Sie Lesezeichen auswählen, verarbeitet das Hilfsprogramm den Auswahlzustand des Visuals.</span><span class="sxs-lookup"><span data-stu-id="7a559-145">When you select bookmarks, the utility handles the visual's selection state.</span></span>

### <a name="visuals-with-a-filter"></a><span data-ttu-id="7a559-146">Visuals mit einem Filter</span><span class="sxs-lookup"><span data-stu-id="7a559-146">Visuals with a filter</span></span>

<span data-ttu-id="7a559-147">Nehmen wir an, dass vom Visual ein Datenfilter nach Datumsbereich erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="7a559-147">Let's assume that the visual creates a filter of data by date range.</span></span> <span data-ttu-id="7a559-148">`startDate` und `endDate` dienen jeweils als Anfangs- und Enddatum.</span><span class="sxs-lookup"><span data-stu-id="7a559-148">You have `startDate` and `endDate` as the start and end dates of the range.</span></span>

<span data-ttu-id="7a559-149">Durch das Visual wird ein erweiterter Filter erstellt, und die Hostmethode `applyJsonFilter` wird aufgerufen, um Daten nach relevanten Bedingungen zu filtern.</span><span class="sxs-lookup"><span data-stu-id="7a559-149">The visual creates an advanced filter and calls the host method `applyJsonFilter` to filter data by the relevant conditions.</span></span>

<span data-ttu-id="7a559-150">Das Ziel ist die Tabelle, die zum Filtern verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7a559-150">The target is the table that's used for filtering.</span></span>

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

<span data-ttu-id="7a559-151">Sobald ein Benutzer auf ein Lesezeichen klickt, empfängt das benutzerdefinierte Visual einen `update`-Aufruf.</span><span class="sxs-lookup"><span data-stu-id="7a559-151">Each time you select a bookmark, the custom visual gets an `update` call.</span></span>

<span data-ttu-id="7a559-152">Der Objektfilter muss vom benutzerdefinierten Visual überprüft werden:</span><span class="sxs-lookup"><span data-stu-id="7a559-152">The custom visual should check the filter in the object:</span></span>

```typescript
const filter: IAdvancedFilter = FilterManager.restoreFilter(
    && options.jsonFilters
    && options.jsonFilters[0] as any
) as IAdvancedFilter;
```

<span data-ttu-id="7a559-153">Wenn das Objekt `filter` nicht NULL ist, muss das Visual Filterbedingungen des Objekts wiederherstellen:</span><span class="sxs-lookup"><span data-stu-id="7a559-153">If the `filter` object isn't null, the visual should restore the filter conditions from the object:</span></span>

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

<span data-ttu-id="7a559-154">Danach sollte das Visual seinen internen Zustand ändern, um die aktuellen Bedingungen widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="7a559-154">After that, the visual should change its internal state to reflect the current conditions.</span></span> <span data-ttu-id="7a559-155">Der interne Zustand umfasst die Datenpunkte und Visualisierungsobjekte (Linien, Rechtecke usw.).</span><span class="sxs-lookup"><span data-stu-id="7a559-155">The internal state includes the data points and visualization objects (lines, rectangles, and so on).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7a559-156">Im Szenario mit den Berichtslesezeichen sollte das Visual nicht `applyJsonFilter` aufrufen, um die anderen Visuals zu filtern.</span><span class="sxs-lookup"><span data-stu-id="7a559-156">In the report bookmarks scenario, the visual shouldn't call `applyJsonFilter` to filter the other visuals.</span></span> <span data-ttu-id="7a559-157">Diese werden bereits von Power BI gefiltert.</span><span class="sxs-lookup"><span data-stu-id="7a559-157">They will already be filtered by Power BI.</span></span>

<span data-ttu-id="7a559-158">Das Visual für den Zeitachsenslicer ändert die Bereichsauswahl gemäß den jeweiligen Datenbereichen.</span><span class="sxs-lookup"><span data-stu-id="7a559-158">The Timeline Slicer visual changes the range selector to the corresponding data ranges.</span></span>

<span data-ttu-id="7a559-159">Weitere Informationen finden Sie im [Zeitachsenslicer-Repository](https://github.com/Microsoft/powerbi-visuals-timeline/commit/606f1152f59f82b5b5a367ff3b117372d129e597?diff=unified#diff-b6ef9a9ac3a3225f8bd0de84bee0a0df).</span><span class="sxs-lookup"><span data-stu-id="7a559-159">For more information, see the [Timeline Slicer repository](https://github.com/Microsoft/powerbi-visuals-timeline/commit/606f1152f59f82b5b5a367ff3b117372d129e597?diff=unified#diff-b6ef9a9ac3a3225f8bd0de84bee0a0df).</span></span>

### <a name="filter-the-state-to-save-visual-properties-in-bookmarks"></a><span data-ttu-id="7a559-160">Filtern des Zustands, um Visualeigenschaften in Lesezeichen zu speichern</span><span class="sxs-lookup"><span data-stu-id="7a559-160">Filter the state to save visual properties in bookmarks</span></span>

<span data-ttu-id="7a559-161">Durch die `filterState`-Eigenschaft wird eine Eigenschaft zum Bestandteil der Filterung.</span><span class="sxs-lookup"><span data-stu-id="7a559-161">The `filterState` property makes a property of a part of filtering.</span></span> <span data-ttu-id="7a559-162">Das Visual ist in der Lage, verschiedene Werte in Lesezeichen zu speichern.</span><span class="sxs-lookup"><span data-stu-id="7a559-162">The visual can store a variety of values in bookmarks.</span></span>

<span data-ttu-id="7a559-163">Wenn Sie den Eigenschaftswert als Filterzustand speichern möchten, markieren Sie die Objekteigenschaft in der Datei *capabilities.json* als `"filterState": true`.</span><span class="sxs-lookup"><span data-stu-id="7a559-163">To save the property value as a filter state, mark the object property as `"filterState": true` in the *capabilities.json* file.</span></span>

<span data-ttu-id="7a559-164">Beispielsweise speichert der Zeitachsenslicer die Eigenschaftswerte für `Granularity` in einem Filter.</span><span class="sxs-lookup"><span data-stu-id="7a559-164">For example, the Timeline Slicer stores the `Granularity` property values in a filter.</span></span> <span data-ttu-id="7a559-165">Dadurch kann sich die aktuelle Granularität ändern, während Sie die Lesezeichen ändern.</span><span class="sxs-lookup"><span data-stu-id="7a559-165">It allows the current granularity to change as you change the bookmarks.</span></span>

<span data-ttu-id="7a559-166">Weitere Informationen finden Sie im [Zeitachsenslicer-Repository](https://github.com/microsoft/powerbi-visuals-timeline/commit/8b7d82dd23cd2bd71817f1bc5d1e1732347a185e#diff-290828b604cfa62f1cb310f2e90c52fdR334).</span><span class="sxs-lookup"><span data-stu-id="7a559-166">For more information, see the [Timeline Slicer repository](https://github.com/microsoft/powerbi-visuals-timeline/commit/8b7d82dd23cd2bd71817f1bc5d1e1732347a185e#diff-290828b604cfa62f1cb310f2e90c52fdR334).</span></span>
