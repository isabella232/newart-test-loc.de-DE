---
title: Hinzufügen einer Landing Page zu Ihren Power BI-Visuals
description: In diesem Artikel wird beschrieben, wie eine Landing Page zu Power BI-Visuals hinzugefügt werden kann.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 79e362796e0694022bceb38f111a62bb62ddbabd
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424415"
---
# <a name="add-a-landing-page-to-your-power-bi-visuals"></a><span data-ttu-id="7b64c-103">Hinzufügen einer Landing Page zu Ihren Power BI-Visuals</span><span class="sxs-lookup"><span data-stu-id="7b64c-103">Add a landing page to your Power BI visuals</span></span>

<span data-ttu-id="7b64c-104">Mit API 2.3.0 können Sie eine Landing Page zu Ihren Power BI-Visuals hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="7b64c-104">With API 2.3.0, you can add a landing page to your Power BI visuals.</span></span> <span data-ttu-id="7b64c-105">Fügen Sie dazu `supportsLandingPage` den Funktionen hinzu, und legen Sie sie auf „true“ fest.</span><span class="sxs-lookup"><span data-stu-id="7b64c-105">To do so, add `supportsLandingPage` to the capabilities, and set it to true.</span></span> <span data-ttu-id="7b64c-106">Durch diese Aktion wird Ihr Visual initialisiert und aktualisiert, bevor Sie Daten hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="7b64c-106">This action initializes and updates your visual before you add data to it.</span></span> <span data-ttu-id="7b64c-107">Da das Visual kein Wasserzeichen mehr anzeigt, können Sie Ihre eigene Landing Page so gestalten, dass Sie im Visual angezeigt wird, solange Sie keine Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="7b64c-107">Because the visual no longer shows a watermark, you can design your own landing page to be displayed in the visual as long as it has no data.</span></span>

```typescript
export class BarChart implements IVisual {
    //...
    private element: HTMLElement;
    private isLandingPageOn: boolean;
    private LandingPageRemoved: boolean;
    private LandingPage: d3.Selection<any>;

    constructor(options: VisualConstructorOptions) {
            //...
            this.element = options.element;
            //...
    }

    public update(options: VisualUpdateOptions) {
    //...
        this.HandleLandingPage(options);
    }

    private HandleLandingPage(options: VisualUpdateOptions) {
        if(!options.dataViews || !options.dataViews.length) {
            if(!this.isLandingPageOn) {
                this.isLandingPageOn = true;
                const SampleLandingPage: Element = this.createSampleLandingPage(); //create a landing page
                this.element.appendChild(SampleLandingPage);
                this.LandingPage = d3.select(SampleLandingPage);
            }

        } else {
                if(this.isLandingPageOn && !this.LandingPageRemoved){
                    this.LandingPageRemoved = true;
                    this.LandingPage.remove();
                }
        }
    }
```

<span data-ttu-id="7b64c-108">Ein Beispiel für eine Landing Page ist in der folgenden Abbildung dargestellt:</span><span class="sxs-lookup"><span data-stu-id="7b64c-108">An example landing page is shown in the following image:</span></span>

![Screenshot einer Landing Page](./media/landing-page.png)
