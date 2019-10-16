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
# <a name="fetch-more-data-from-power-bi"></a>Abrufen größerer Datenmengen von Power BI

In diesem Artikel wird erläutert, wie Sie größere Datenmengen laden können, um das harte Limit eines 30 KB großen Datenpunkts zu umgehen. Bei diesem Ansatz werden Daten in Blöcken bereitgestellt. Sie können die Blockgröße so konfigurieren, dass sie Ihrem Anwendungsfall entspricht, um die Leistung zu verbessern.  

## <a name="enable-a-segmented-fetch-of-large-datasets"></a>Aktivieren eines segmentierten Abrufs großer Datasets

Für den Segmentmodus `dataview` definieren Sie für dataReductionAlgorithm in der *capabilities.json*-Datei des Visuals eine Fenstergröße für das erforderliche dataViewMapping-Objekt. Die Fenstergröße wird durch `count` bestimmt. Dadurch wird die Anzahl neuer Datenzeilen begrenzt, die bei jeder Aktualisierung an `dataview` angefügt werden können.

Fügen Sie den folgenden Code in die *capabilities.json*-Datei ein:

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

Neue Segmente werden an die vorhandene `dataview` angefügt und dem Visual als `update`-Aufruf zur Verfügung gestellt.

## <a name="usage-in-the-power-bi-visual"></a>Verwendung im Power BI-Visual

Sie können ermitteln, ob Daten vorhanden sind, indem Sie die Existenz von `dataView.metadata.segment` überprüfen:

```typescript
    public update(options: VisualUpdateOptions) {
        const dataView = options.dataViews[0];
        console.log(dataView.metadata.segment);
        // output: __proto__: Object
    }
```

Anhand von `options.operationKind` können Sie auch überprüfen, ob es sich um das erste Update oder eine nachfolgende Aktualisierung handelt. Im folgenden Code bezieht sich `VisualDataChangeOperationKind.Create` auf das erste Segment und `VisualDataChangeOperationKind.Append` auf die nachfolgenden Segmente.

Eine Beispielimplementierung finden Sie im folgenden Codeausschnitt:

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

Wie im Folgenden gezeigt können Sie die `fetchMoreData`-Methode auch über einen UI-Ereignishandler aufrufen:

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

Als Antwort auf das Aufrufen der `this.host.fetchMoreData`-Methode ruft Power BI die `update`-Methode des Visuals mit einem neuen Datensegment auf.

> [!NOTE]
> In Power BI ist die insgesamt abrufbare Datenmenge derzeit auf 100 MB beschränkt, um Speichereinschränkungen auf dem Client zu vermeiden. Sie können sehen, dass der Grenzwert erreicht wurde, wenn fetchMoreData() `false` zurückgibt.
