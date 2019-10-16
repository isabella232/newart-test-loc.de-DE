---
title: Visuelle Interaktionen in Power BI-Visuals
description: In diesem Artikel wird erläutert, wie Sie überprüfen können, ob Power BI-Visuals visuelle Interaktionen zulassen sollten.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 0155f0fce1bc0fec5c96aef1c7e1dc9cf64b122f
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424640"
---
# <a name="visual-interactions-in-power-bi-visuals"></a>Visuelle Interaktionen in Power BI-Visuals

Visuals können den Wert des `allowInteractions`-Flags abfragen, das angibt, ob das Visual visuelle Interaktionen zulassen soll. Beispielsweise sind Visuals während der Anzeige oder Bearbeitung von Berichten interaktiv, bei der Anzeige in einem Dashboard aber nicht. Interaktionen können beispielsweise *Klick*-, *Schwenk*-, *Zoom*- und *Auswahlaktionen* umfassen. 

> [!NOTE]
> Sie sollten QuickInfos in allen Szenarios aktivieren, unabhängig davon, welches Flag angegeben wird.

Das `allowInteractions`-Flag wird während der Initialisierung des Visuals in Form eines booleschen Werts als Member der IVisualHost-Schnittstelle übergeben.

In allen Power BI-Szenarios, in denen Visuals nicht interaktiv sein dürfen (beispielsweise in Dashboardkacheln), ist das `allowInteractions`-Flag auf `false` festgelegt. Ansonsten (beispielsweise in einem Bericht) ist das `allowInteractions`-Flag auf `true` festgelegt.

Weitere Informationen finden Sie im [SampleBarChart-Visualrepository](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/commit/59a47935d8f5272ce145fe804193599ddb7e2001).

```typescript
   ...
   let allowInteractions = options.host.allowInteractions;
   bars.on('click', function(d) {
       if (allowInteractions) {
           selectionManager.select(d.selectionId);
           ...
       }
   });
```
