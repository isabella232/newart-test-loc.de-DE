---
title: Hervorhebungen
description: Hervorheben ausgewählter Datenpunkte in Power BI-Visuals
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 4ff2187dc99d4e790b08c11f55a37e31e85693ad
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424280"
---
# <a name="highlight-data-points-in-power-bi-visuals"></a><span data-ttu-id="b8a68-103">Hervorheben von Datenpunkten in Power BI-Visuals</span><span class="sxs-lookup"><span data-stu-id="b8a68-103">Highlight data points in Power BI Visuals</span></span>

<span data-ttu-id="b8a68-104">Wenn ein Element ausgewählt ist, wird das `values`-Array im `dataView`-Objekt standardmäßig nach den ausgewählten Werten gefiltert.</span><span class="sxs-lookup"><span data-stu-id="b8a68-104">By default whenever an element is selected the `values` array in the `dataView` object will be filtered to just the selected values.</span></span> <span data-ttu-id="b8a68-105">Dadurch werden für alle anderen Visuals auf der Seite nur die ausgewählten Daten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b8a68-105">It will cause all other visuals on the page to display just the selected data.</span></span>

![Standardverhalten beim Hervorheben von 'DataView'](./media/highlight-dataview.png)

<span data-ttu-id="b8a68-107">Wenn Sie die `supportsHighlight`-Eigenschaft in `capabilities.json` auf `true` festlegen, erhalten Sie ein gänzlich ungefiltertes `values`-Array zusammen mit einem `highlights`-Array.</span><span class="sxs-lookup"><span data-stu-id="b8a68-107">If you set the `supportsHighlight` property in your `capabilities.json` to `true`, you'll receive the full unfiltered `values` array along with a `highlights` array.</span></span> <span data-ttu-id="b8a68-108">Das `highlights`-Array hat die gleiche Länge wie das Wertarray, und alle nicht ausgewählten Werte werden auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b8a68-108">The `highlights` array will be the same length as the values array and any non-selected values will be set to `null`.</span></span> <span data-ttu-id="b8a68-109">Wenn diese Eigenschaft aktiviert ist, ist das Visual dafür zuständig, die entsprechenden Daten hervorzuheben. Dazu wird das `values`-Array mit dem `highlights`-Array verglichen.</span><span class="sxs-lookup"><span data-stu-id="b8a68-109">With this property enabled it's the visual's responsibility to highlight the appropriate data by comparing the `values` array to the `highlights` array.</span></span>

![Unterstützung für das Hervorheben von 'DataView'](./media/highlight-dataview-supports.png)

<span data-ttu-id="b8a68-111">In diesem Beispiel sehen Sie, dass ein Balken ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="b8a68-111">In the example, you'll notice the 1 bar that is selected.</span></span> <span data-ttu-id="b8a68-112">Dies ist auch der einzige Wert im highlights-Array.</span><span class="sxs-lookup"><span data-stu-id="b8a68-112">And it's the only value in the highlights array.</span></span> <span data-ttu-id="b8a68-113">Beachten Sie außerdem, dass es mehrere Auswahlen sowie teilweise Hervorhebungen geben kann.</span><span class="sxs-lookup"><span data-stu-id="b8a68-113">It's also important to note there could be multiple selections and partial highlight.</span></span> <span data-ttu-id="b8a68-114">Der entsprechende numerische Wert ist in den Werten enthalten. Highlights-Arrays sind vorhanden, unterscheiden sich jedoch voneinander.</span><span class="sxs-lookup"><span data-stu-id="b8a68-114">There's the corresponding numeric value in the values and highlights arrays will be present but different.</span></span>
