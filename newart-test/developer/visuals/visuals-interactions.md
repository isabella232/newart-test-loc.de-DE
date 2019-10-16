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
# <a name="visual-interactions-in-power-bi-visuals"></a><span data-ttu-id="2845a-103">Visuelle Interaktionen in Power BI-Visuals</span><span class="sxs-lookup"><span data-stu-id="2845a-103">Visual interactions in Power BI visuals</span></span>

<span data-ttu-id="2845a-104">Visuals können den Wert des `allowInteractions`-Flags abfragen, das angibt, ob das Visual visuelle Interaktionen zulassen soll.</span><span class="sxs-lookup"><span data-stu-id="2845a-104">Visuals can query the value of the `allowInteractions` flag, which indicates whether the visual should allow visual interactions.</span></span> <span data-ttu-id="2845a-105">Beispielsweise sind Visuals während der Anzeige oder Bearbeitung von Berichten interaktiv, bei der Anzeige in einem Dashboard aber nicht.</span><span class="sxs-lookup"><span data-stu-id="2845a-105">For example, visuals are interactive during report viewing or editing, but not interactive when they're viewed in a dashboard.</span></span> <span data-ttu-id="2845a-106">Interaktionen können beispielsweise *Klick*-, *Schwenk*-, *Zoom*- und *Auswahlaktionen* umfassen.</span><span class="sxs-lookup"><span data-stu-id="2845a-106">These interactions are *click*, *pan*, *zoom*, *selection*, and others.</span></span> 

> [!NOTE]
> <span data-ttu-id="2845a-107">Sie sollten QuickInfos in allen Szenarios aktivieren, unabhängig davon, welches Flag angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="2845a-107">You should enable tooltips in all scenarios, regardless of which flag is indicated.</span></span>

<span data-ttu-id="2845a-108">Das `allowInteractions`-Flag wird während der Initialisierung des Visuals in Form eines booleschen Werts als Member der IVisualHost-Schnittstelle übergeben.</span><span class="sxs-lookup"><span data-stu-id="2845a-108">The `allowInteractions` flag is passed as a Boolean during the initialization of the visual, as a member of the IVisualHost interface.</span></span>

<span data-ttu-id="2845a-109">In allen Power BI-Szenarios, in denen Visuals nicht interaktiv sein dürfen (beispielsweise in Dashboardkacheln), ist das `allowInteractions`-Flag auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="2845a-109">In any Power BI scenario that requires that the visuals not be interactive (for example, dashboard tiles), the `allowInteractions` flag is set to `false`.</span></span> <span data-ttu-id="2845a-110">Ansonsten (beispielsweise in einem Bericht) ist das `allowInteractions`-Flag auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="2845a-110">Otherwise (for example, Report), `allowInteractions` is set to `true`.</span></span>

<span data-ttu-id="2845a-111">Weitere Informationen finden Sie im [SampleBarChart-Visualrepository](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/commit/59a47935d8f5272ce145fe804193599ddb7e2001).</span><span class="sxs-lookup"><span data-stu-id="2845a-111">For more information, see the [SampleBarChart visual repository](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/commit/59a47935d8f5272ce145fe804193599ddb7e2001).</span></span>

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
