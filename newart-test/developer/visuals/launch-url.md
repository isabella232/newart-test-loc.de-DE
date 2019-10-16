---
title: Erstellen einer Start-URL
description: In diesem Artikel wird beschrieben, wie die URL mithilfe von Power BI-Visuals auf der neuen Registerkarte geöffnet werden kann.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 7dff03f0ad192e0598c677d41709447fbdc0688d
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424475"
---
# <a name="create-a-launch-url"></a><span data-ttu-id="600c1-103">Erstellen einer Start-URL</span><span class="sxs-lookup"><span data-stu-id="600c1-103">Create a launch URL</span></span>

<span data-ttu-id="600c1-104">Durch das Erstellen einer Start-URL können Sie eine neue Browserregisterkarte (oder ein Fenster) öffnen, indem Sie tatsächliche Arbeit an Power BI delegieren.</span><span class="sxs-lookup"><span data-stu-id="600c1-104">By creating a launch URL, you can open a new browser tab (or window) by delegating the actual work to Power BI.</span></span>

## <a name="sample"></a><span data-ttu-id="600c1-105">Beispiel</span><span class="sxs-lookup"><span data-stu-id="600c1-105">Sample</span></span>

```typescript
   this.host.launchUrl('https://powerbi.microsoft.com');
```

## <a name="usage"></a><span data-ttu-id="600c1-106">Verwendung</span><span class="sxs-lookup"><span data-stu-id="600c1-106">Usage</span></span>

<span data-ttu-id="600c1-107">Verwenden Sie den API-Aufruf `host.launchUrl()`, wobei Sie Ihre Ziel-URL als Zeichenfolgenargument übergeben:</span><span class="sxs-lookup"><span data-stu-id="600c1-107">Use the `host.launchUrl()` API call, passing your destination URL as a string argument:</span></span>

```typescript
this.host.launchUrl('http://some.link.net');
```

## <a name="restrictions"></a><span data-ttu-id="600c1-108">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="600c1-108">Restrictions</span></span>

* <span data-ttu-id="600c1-109">Verwenden Sie nur absolute, aber keine relativen Pfade.</span><span class="sxs-lookup"><span data-stu-id="600c1-109">Use only absolute paths, not relative paths.</span></span> <span data-ttu-id="600c1-110">Verwenden Sie beispielsweise einen absoluten Pfad wie `http://some.link.net/subfolder/page.html`.</span><span class="sxs-lookup"><span data-stu-id="600c1-110">For example, use an absolute path such as `http://some.link.net/subfolder/page.html`.</span></span> <span data-ttu-id="600c1-111">Der relative Pfad `/page.html` wird nicht geöffnet.</span><span class="sxs-lookup"><span data-stu-id="600c1-111">The relative path,`/page.html`, won't be opened.</span></span>

* <span data-ttu-id="600c1-112">Derzeit werden nur *HTTP*- und *HTTPS*-Protokolle unterstützt.</span><span class="sxs-lookup"><span data-stu-id="600c1-112">Currently, only *HTTP* and *HTTPS* protocols are supported.</span></span> <span data-ttu-id="600c1-113">Vermeiden Sie *FTP*, *MAILTO* usw.</span><span class="sxs-lookup"><span data-stu-id="600c1-113">Avoid *FTP*, *MAILTO*, and so on.</span></span>

## <a name="best-practices"></a><span data-ttu-id="600c1-114">Bewährte Methoden</span><span class="sxs-lookup"><span data-stu-id="600c1-114">Best practices</span></span>

* <span data-ttu-id="600c1-115">In der Regel empfiehlt es sich, einen Link nur als Reaktion auf die explizite Aktion eines Benutzers zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="600c1-115">Usually, it's best to open a link only as a response to a user's explicit action.</span></span> <span data-ttu-id="600c1-116">Für den Benutzer sollte leicht erkennbar sein, dass durch das Klicken auf den Link oder die Schaltfläche eine neue Registerkarte geöffnet wird. Wenn ein `launchUrl()`-Aufruf ohne Benutzeraktion oder als Nebeneffekt einer anderen Aktion ausgelöst wird, kann dies verwirrend oder frustrierend für den Benutzer sein.</span><span class="sxs-lookup"><span data-stu-id="600c1-116">Make it easy for the user to understand that clicking the link or button will result in opening a new tab. Triggering a `launchUrl()` call without a user's action, or as a side effect of a different action can be confusing or frustrating for the user.</span></span>

* <span data-ttu-id="600c1-117">Wenn der Link für die ordnungsgemäße Funktionsweise des Visuals nicht wichtig ist, wird empfohlen, dem Berichtsautor eine Möglichkeit zum Deaktivieren und Ausblenden des Links anzubieten.</span><span class="sxs-lookup"><span data-stu-id="600c1-117">If the link isn't essential for the proper functioning of the visual, we recommend that you give the report's author a way to disable and hide the link.</span></span> <span data-ttu-id="600c1-118">Diese Empfehlung gilt besonders für bestimmte Power BI-Anwendungsfälle wie das Einbetten eines Berichts in eine Drittanbieteranwendung oder dessen Veröffentlichung im Web.</span><span class="sxs-lookup"><span data-stu-id="600c1-118">This recommendation is especially relevant for special Power BI use cases, such as embedding a report in a third-party application or publishing it to the web.</span></span>

* <span data-ttu-id="600c1-119">Vermeiden Sie es, einen `launchUrl()`-Aufruf aus einer Schleife, der `update`-Funktion des Visuals oder sonstigem häufig wiederkehrenden Code heraus aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="600c1-119">Avoid triggering a `launchUrl()` call from inside a loop, the visual's `update` function, or any other frequently recurring code.</span></span>

## <a name="a-step-by-step-example"></a><span data-ttu-id="600c1-120">Ausführliches Beispiel</span><span class="sxs-lookup"><span data-stu-id="600c1-120">A step-by-step example</span></span>

### <a name="add-a-link-launching-element"></a><span data-ttu-id="600c1-121">Hinzufügen eines „Start-Link“-Elements</span><span class="sxs-lookup"><span data-stu-id="600c1-121">Add a link-launching element</span></span>

<span data-ttu-id="600c1-122">Die folgenden Zeilen wurden der `constructor`-Funktion des Visuals hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="600c1-122">The following lines were added to the visual's `constructor` function:</span></span>

```typescript
    this.helpLinkElement = this.createHelpLinkElement();
    options.element.appendChild(this.helpLinkElement);
```

<span data-ttu-id="600c1-123">Es wurde eine private Funktion zum Erstellen und Anfügen des Anchor-Elements hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="600c1-123">A private function that creates and attaches the anchor element was added:</span></span>

```typescript
private createHelpLinkElement(): Element {
    let linkElement = document.createElement("a");
    linkElement.textContent = "?";
    linkElement.setAttribute("title", "Open documentation");
    linkElement.setAttribute("class", "helpLink");
    linkElement.addEventListener("click", () => {
        this.host.launchUrl("https://docs.microsoft.com/power-bi/developer/visuals/custom-visual-develop-tutorial");
    });
    return linkElement;
};
```

<span data-ttu-id="600c1-124">Zuletzt wird der Stil des Link-Elements durch einen Eintrag in der Datei *visual.less* definiert:</span><span class="sxs-lookup"><span data-stu-id="600c1-124">Finally, an entry in the *visual.less* file defines the style for the link element:</span></span>

```less
.helpLink {
    position: absolute;
    top: 0px;
    right: 12px;
    display: block;
    width: 20px;
    height: 20px;
    border: 2px solid #80B0E0;
    border-radius: 20px;
    color: #80B0E0;
    text-align: center;
    font-size: 16px;
    line-height: 20px;
    background-color: #FFFFFF;
    transition: all 900ms ease;

    &:hover {
        background-color: #DDEEFF;
        color: #5080B0;
        border-color: #5080B0;
        transition: all 250ms ease;
    }

    &.hidden {
        display: none;
    }
}
```

### <a name="add-a-toggling-mechanism"></a><span data-ttu-id="600c1-125">Hinzufügen eines Mechanismus zum Ein-/Ausblenden</span><span class="sxs-lookup"><span data-stu-id="600c1-125">Add a toggling mechanism</span></span>

<span data-ttu-id="600c1-126">Zum Hinzufügen eines Mechanismus zum Ein-/Ausblenden müssen Sie ein statisches Objekt hinzufügen, damit der Autor des Berichts die Sichtbatkeit des Link-Elements umstellen kann.</span><span class="sxs-lookup"><span data-stu-id="600c1-126">To add a toggling mechanism, you need to add a static object so that the report's author can toggle the visibility of the link element.</span></span> <span data-ttu-id="600c1-127">(Die Standardeinstellung ist auf *hidden* festgelegt.) Weitere Informationen finden Sie im [Tutorial zu statischen Objekten](https://microsoft.github.io/PowerBI-visuals/docs/concepts/objects-and-properties).</span><span class="sxs-lookup"><span data-stu-id="600c1-127">(The default is set to *hidden*.) For more information, see the [static object tutorial](https://microsoft.github.io/PowerBI-visuals/docs/concepts/objects-and-properties).</span></span>

<span data-ttu-id="600c1-128">Wie im folgenden Code veranschaulicht wurde dem Objekteintrag der *capabilities.json*-Datei das statische boolesche Objekt `showHelpLink` hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="600c1-128">A `showHelpLink` Boolean static object was added to the *capabilities.json* file's objects entry, as shown in the following code:</span></span>

```typescript
"objects": {
    "generalView": {
            "displayName": "General View",
            "properties":
                "showHelpLink": {
                    "displayName": "Show Help Button",
                    "type": {
                        "bool": true
                    }
                }
            }
        }
    }
```

![Ein-/Ausblenden der Start-URL](./media/launchurl-toggle.png)

<span data-ttu-id="600c1-130">Außerdem wurden der `update`-Funktion des Visuals die folgenden Zeilen hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="600c1-130">And, in the visual's `update` function, the following lines were added:</span></span>

```typescript
if (settings.generalView.showHelpLink) {
    this.helpLinkElement.classList.remove("hidden");
} else {
    this.helpLinkElement.classList.add("hidden");
}
```

<span data-ttu-id="600c1-131">Die Klasse *hidden* wurde in der *visual.less*-Datei definiert, um die Anzeige des Elements zu steuern.</span><span class="sxs-lookup"><span data-stu-id="600c1-131">The *hidden* class is defined in the *visual.less* file to control the display of the element.</span></span>
