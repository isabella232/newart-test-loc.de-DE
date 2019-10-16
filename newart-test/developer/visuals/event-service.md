---
title: Rendern von Ereignissen in Power BI-Visuals
description: Power BI kann von Power BI-Visuals benachrichtigt werden, dass sie für den Export in PowerPoint oder eine PDF-Datei bereit sind.
author: Yarovinsky
ms.author: alexyar
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b481ce94e5025045466a05d71e30a00f02be7ead
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424300"
---
# <a name="render-events-in-power-bi-visuals"></a><span data-ttu-id="9fb53-103">Rendern von Ereignissen in Power BI-Visuals</span><span class="sxs-lookup"><span data-stu-id="9fb53-103">Render events in Power BI visuals</span></span>

<span data-ttu-id="9fb53-104">Die neue API umfasst drei Methoden (`started`, `finished` oder `failed`), die während des Renderings aufgerufen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="9fb53-104">The new API consists of three methods (`started`, `finished`, or `failed`) that should be called during rendering.</span></span>

<span data-ttu-id="9fb53-105">Beim Start des Renderings wird vom Code des Power BI-Visuals die `renderingStarted`-Methode aufgerufen, um anzugeben, dass der Renderingvorgang gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="9fb53-105">When rendering starts, the Power BI visual code calls the `renderingStarted` method to indicate that the rendering process has started.</span></span>

<span data-ttu-id="9fb53-106">Wenn das Rendering erfolgreich abgeschlossen ist, wird im Code des Power BI-Visuals sofort die `renderingFinished`-Methode aufgerufen, um die Listener (hauptsächlich für *In PDF exportieren* und *Nach PowerPoint exportieren*) zu benachrichtigen, dass das Image des Visuals bereit ist für den Export.</span><span class="sxs-lookup"><span data-stu-id="9fb53-106">If rendering is completed successfully, the Power BI visual code immediately calls the `renderingFinished` method, notifying the listeners (primarily, *export to PDF* and *export to PowerPoint*) that the visual's image is ready for export.</span></span>

<span data-ttu-id="9fb53-107">Wenn während des Prozesses ein Problem auftritt, kann das Power BI-Visual nicht erfolgreich gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="9fb53-107">If a problem occurs during the process, the Power BI visual is prevented from being rendered successfully.</span></span> <span data-ttu-id="9fb53-108">Im Code des Power BI-Visuals muss die `renderingFailed`-Methode aufgerufen werden, durch die der Listener benachrichtigt wird, dass der Renderingvorgang noch nicht abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="9fb53-108">To notify the listeners that the rendering process hasn't been completed, the Power BI visual code should call the `renderingFailed` method.</span></span> <span data-ttu-id="9fb53-109">Diese Methode stellt zusätzlich eine optionale Zeichenfolge zur Erläuterung der Fehlerursache bereit.</span><span class="sxs-lookup"><span data-stu-id="9fb53-109">This method also provides an optional string to provide a reason for the failure.</span></span>

## <a name="usage"></a><span data-ttu-id="9fb53-110">Verwendung</span><span class="sxs-lookup"><span data-stu-id="9fb53-110">Usage</span></span>

```typescript
export interface IVisualHost extends extensibility.IVisualHost {
    eventService: IVisualEventService ;
}

/**
 * An interface for reporting rendering events
 */
export interface IVisualEventService {
    /**
     * Should be called just before the actual rendering starts, 
     * usually at the start of the update method
     *
     * @param options - the visual update options received as an update parameter
     */
    renderingStarted(options: VisualUpdateOptions): void;

    /**
     * Should be called immediately after rendering finishes successfully
     * 
     * @param options - the visual update options received as an update parameter
     */
    renderingFinished(options: VisualUpdateOptions): void;

    /**
     * Called when rendering fails, with an optional reason string
     * 
     * @param options - the visual update options received as an update parameter
     * @param reason - the optional failure reason string
     */
    renderingFailed(options: VisualUpdateOptions, reason?: string): void;
}
```

### <a name="sample-the-visual-displays-no-animations"></a><span data-ttu-id="9fb53-111">Beispiel: Das Visual zeigt keine Animationen an.</span><span class="sxs-lookup"><span data-stu-id="9fb53-111">Sample: The visual displays no animations</span></span>

```typescript
    export class Visual implements IVisual {
        ...
        private events: IVisualEventService;
        ...

        constructor(options: VisualConstructorOptions) {
            ...
            this.events = options.host.eventService;
            ...
        }

        public update(options: VisualUpdateOptions) {
            this.events.renderingStarted(options);
            ...
            this.events.renderingFinished(options);
        }
```

### <a name="sample-the-visual-displays-animations"></a><span data-ttu-id="9fb53-112">Beispiel: Das Visual zeigt Animationen an.</span><span class="sxs-lookup"><span data-stu-id="9fb53-112">Sample: The visual displays animations</span></span>

<span data-ttu-id="9fb53-113">Wenn das Visual Animationen oder asynchrone Funktionen für das Rendering aufweist, muss nach der Animation oder innerhalb der asynchronen Funktion die `renderingFinished`-Methode aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="9fb53-113">If the visual has animations or async functions for rendering, the `renderingFinished` method should be called after the animation or inside async function.</span></span>

```typescript
    export class Visual implements IVisual {
        ...
        private events: IVisualEventService;
        private element: HTMLElement;
        ...

        constructor(options: VisualConstructorOptions) {
            ...
            this.events = options.host.eventService;
            this.element = options.element;
            ...
        }

        public update(options: VisualUpdateOptions) {
            this.events.renderingStarted(options);
            ...
            // Learn more at https://github.com/d3/d3-transition/blob/master/README.md#transition_end
            d3.select(this.element).transition().duration(100).style("opacity","0").end().then(() => {
                // renderingFinished called after transition end
                this.events.renderingFinished(options);
            });
        }
```

## <a name="rendering-events-for-visual-certification"></a><span data-ttu-id="9fb53-114">Rendern von Ereignissen für die Zertifizierung von Visuals</span><span class="sxs-lookup"><span data-stu-id="9fb53-114">Rendering events for visual certification</span></span>

<span data-ttu-id="9fb53-115">Eine Anforderung für die Zertifizierung von Visuals besteht darin, dass das Visual das Rendern von Ereignissen unterstützen muss.</span><span class="sxs-lookup"><span data-stu-id="9fb53-115">One requirement of visuals certification is the support of rendering events by the visual.</span></span> <span data-ttu-id="9fb53-116">Weitere Informationen finden Sie unter [certification requirements](https://docs.microsoft.com/power-bi/power-bi-custom-visuals-certified?#certification-requirements) (Zertifizierungsanforderungen).</span><span class="sxs-lookup"><span data-stu-id="9fb53-116">For more information, see [certification requirements](https://docs.microsoft.com/power-bi/power-bi-custom-visuals-certified?#certification-requirements).</span></span>
