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
# <a name="highlight-data-points-in-power-bi-visuals"></a>Hervorheben von Datenpunkten in Power BI-Visuals

Wenn ein Element ausgewählt ist, wird das `values`-Array im `dataView`-Objekt standardmäßig nach den ausgewählten Werten gefiltert. Dadurch werden für alle anderen Visuals auf der Seite nur die ausgewählten Daten angezeigt.

![Standardverhalten beim Hervorheben von 'DataView'](./media/highlight-dataview.png)

Wenn Sie die `supportsHighlight`-Eigenschaft in `capabilities.json` auf `true` festlegen, erhalten Sie ein gänzlich ungefiltertes `values`-Array zusammen mit einem `highlights`-Array. Das `highlights`-Array hat die gleiche Länge wie das Wertarray, und alle nicht ausgewählten Werte werden auf `null` festgelegt. Wenn diese Eigenschaft aktiviert ist, ist das Visual dafür zuständig, die entsprechenden Daten hervorzuheben. Dazu wird das `values`-Array mit dem `highlights`-Array verglichen.

![Unterstützung für das Hervorheben von 'DataView'](./media/highlight-dataview-supports.png)

In diesem Beispiel sehen Sie, dass ein Balken ausgewählt ist. Dies ist auch der einzige Wert im highlights-Array. Beachten Sie außerdem, dass es mehrere Auswahlen sowie teilweise Hervorhebungen geben kann. Der entsprechende numerische Wert ist in den Werten enthalten. Highlights-Arrays sind vorhanden, unterscheiden sich jedoch voneinander.
