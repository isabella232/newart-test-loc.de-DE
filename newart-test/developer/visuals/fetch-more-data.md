---
title: Abrufen größerer Datenmengen von Power BI
description: In diesem Artikel wird erläutert, wie Sie einen segmentierten Abruf großer Datasets für Power BI-Visuals aktivieren.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b67977abd93b3aa605430dd2d7fb516ca33bd51c
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424175"
---
# <a name="fetch-more-data-from-power-bi"></a><span data-ttu-id="cb33e-103">Abrufen größerer Datenmengen von Power BI</span><span class="sxs-lookup"><span data-stu-id="cb33e-103">Fetch more data from Power BI</span></span>

<span data-ttu-id="cb33e-104">In diesem Artikel wird erläutert, wie Sie größere Datenmengen laden können, um das harte Limit eines 30 KB großen Datenpunkts zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="cb33e-104">This article discusses how to load more data to bypass the hard limit of a 30-KB data point.</span></span> <span data-ttu-id="cb33e-105">Bei diesem Ansatz werden Daten in Blöcken bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="cb33e-105">This approach provides data in chunks.</span></span> <span data-ttu-id="cb33e-106">Sie können die Blockgröße so konfigurieren, dass sie Ihrem Anwendungsfall entspricht, um die Leistung zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="cb33e-106">To improve performance, you can configure the chunk size to accommodate your use case.</span></span>  

## <a name="enable-a-segmented-fetch-of-large-datasets"></a><span data-ttu-id="cb33e-107">Aktivieren eines segmentierten Abrufs großer Datasets</span><span class="sxs-lookup"><span data-stu-id="cb33e-107">Enable a segmented fetch of large datasets</span></span>

<span data-ttu-id="cb33e-108">Für den Segmentmodus `dataview` definieren Sie für dataReductionAlgorithm in der *capabilities.json*-Datei des Visuals eine Fenstergröße für das erforderliche dataViewMapping-Objekt.</span><span class="sxs-lookup"><span data-stu-id="cb33e-108">For the `dataview` segment mode, you define a window size for dataReductionAlgorithm in the visual's *capabilities.json* file for the required dataViewMapping.</span></span> <span data-ttu-id="cb33e-109">Die Fenstergröße wird durch `count` bestimmt. Dadurch wird die Anzahl neuer Datenzeilen begrenzt, die bei jeder Aktualisierung an `dataview` angefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="cb33e-109">The `count` determines the window size, which limits the number of new data rows that can be appended to the `dataview` in each update.</span></span>

<span data-ttu-id="cb33e-110">Fügen Sie den folgenden Code in die *capabilities.json*-Datei ein:</span><span class="sxs-lookup"><span data-stu-id="cb33e-110">Add the following code in the *capabilities.json* file:</span></span>

```typescript
"dataViewMappings": [
    {
        "table": {
            "rows": {
                "for": {
                    "in": "values"
                },
                "dataReductionAlgorithm": {
                    "window": {
                        "count": 100
                    }
                }
            }
    }
]
```

<span data-ttu-id="cb33e-111">Neue Segmente werden an die vorhandene `dataview` angefügt und dem Visual als `update`-Aufruf zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="cb33e-111">New segments are appended to the existing `dataview` and provided to the visual as an `update` call.</span></span>

## <a name="usage-in-the-power-bi-visual"></a><span data-ttu-id="cb33e-112">Verwendung im Power BI-Visual</span><span class="sxs-lookup"><span data-stu-id="cb33e-112">Usage in the Power BI visual</span></span>

<span data-ttu-id="cb33e-113">Sie können ermitteln, ob Daten vorhanden sind, indem Sie die Existenz von `dataView.metadata.segment` überprüfen:</span><span class="sxs-lookup"><span data-stu-id="cb33e-113">You can determine whether data exists by checking the existence of `dataView.metadata.segment`:</span></span>

```typescript
    public update(options: VisualUpdateOptions) {
        const dataView = options.dataViews[0];
        console.log(dataView.metadata.segment);
        // output: __proto__: Object
    }
```

<span data-ttu-id="cb33e-114">Anhand von `options.operationKind` können Sie auch überprüfen, ob es sich um das erste Update oder eine nachfolgende Aktualisierung handelt.</span><span class="sxs-lookup"><span data-stu-id="cb33e-114">You can also check to see whether it's the first update or a subsequent update by checking `options.operationKind`.</span></span> <span data-ttu-id="cb33e-115">Im folgenden Code bezieht sich `VisualDataChangeOperationKind.Create` auf das erste Segment und `VisualDataChangeOperationKind.Append` auf die nachfolgenden Segmente.</span><span class="sxs-lookup"><span data-stu-id="cb33e-115">In the following code, `VisualDataChangeOperationKind.Create` refers to the first segment, and `VisualDataChangeOperationKind.Append` refers to subsequent segments.</span></span>

<span data-ttu-id="cb33e-116">Eine Beispielimplementierung finden Sie im folgenden Codeausschnitt:</span><span class="sxs-lookup"><span data-stu-id="cb33e-116">For a sample implementation, see the following code snippet:</span></span>

```typescript
// CV update implementation
public update(options: VisualUpdateOptions) {
    // indicates this is the first segment of new data.
    if (options.operationKind == VisualDataChangeOperationKind.Create) {

    }

    // on second or subsequent segments:
    if (options.operationKind == VisualDataChangeOperationKind.Append) {

    }

    // complete update implementation
}
```

<span data-ttu-id="cb33e-117">Wie im Folgenden gezeigt können Sie die `fetchMoreData`-Methode auch über einen UI-Ereignishandler aufrufen:</span><span class="sxs-lookup"><span data-stu-id="cb33e-117">You can also invoke the `fetchMoreData` method from a UI event handler, as shown here:</span></span>

```typescript
btn_click(){
{
    // check if more data is expected for the current data view
    if (dataView.metadata.segment) {
        //request for more data if available; as a response, Power BI will call update method
        let request_accepted: bool = this.host.fetchMoreData();
        // handle rejection
        if (!request_accepted) {
            // for example, when the 100 MB limit has been reached
        }
    }
}
```

<span data-ttu-id="cb33e-118">Als Antwort auf das Aufrufen der `this.host.fetchMoreData`-Methode ruft Power BI die `update`-Methode des Visuals mit einem neuen Datensegment auf.</span><span class="sxs-lookup"><span data-stu-id="cb33e-118">As a response to calling the `this.host.fetchMoreData` method, Power BI calls the `update` method of the visual with a new segment of data.</span></span>

> [!NOTE]
> <span data-ttu-id="cb33e-119">In Power BI ist die insgesamt abrufbare Datenmenge derzeit auf 100 MB beschränkt, um Speichereinschränkungen auf dem Client zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="cb33e-119">To avoid client memory constraints, Power BI currently limits the fetched data total to 100 MB.</span></span> <span data-ttu-id="cb33e-120">Sie können sehen, dass der Grenzwert erreicht wurde, wenn fetchMoreData() `false` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="cb33e-120">You can see that the limit has been reached when fetchMoreData() returns `false`.</span></span>
