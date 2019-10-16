---
title: Erweiterter Bearbeitungsmodus in Power BI-Visuals
description: In diesem Artikel wird erläutert, wie Sie in Power BI-Visuals erweiterte UI-Steuerelemente festlegen.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: da72cf603027bc97060e7a00ed4a4e959a3a92e2
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424200"
---
# <a name="advanced-edit-mode-in-power-bi-visuals"></a><span data-ttu-id="298be-103">Erweiterter Bearbeitungsmodus in Power BI-Visuals</span><span class="sxs-lookup"><span data-stu-id="298be-103">Advanced edit mode in Power BI visuals</span></span>

<span data-ttu-id="298be-104">Wenn Sie in Ihrem Power BI-Visual erweiterte UI-Steuerelemente benötigen, können Sie den Modus „Erweiterte Bearbeitung“ nutzen.</span><span class="sxs-lookup"><span data-stu-id="298be-104">If you require advanced UI controls in your Power BI visual, you can take advantage of advanced edit mode.</span></span> <span data-ttu-id="298be-105">Wenn Sie sich im Bearbeitungsmodus des Berichts befinden, können Sie auf die Schaltfläche **Bearbeiten** klicken, um den Bearbeitungsmodus auf **Erweitert** festzulegen.</span><span class="sxs-lookup"><span data-stu-id="298be-105">When you're in report editing mode, you select an **Edit** button to set the edit mode to **Advanced**.</span></span> <span data-ttu-id="298be-106">Das Visual kann anhand des `EditMode`-Flags bestimmen, ob diese UI-Steuerelemente anzeigt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="298be-106">The visual can use the `EditMode` flag to determine whether it should display this UI control.</span></span>

<span data-ttu-id="298be-107">Der Modus „Erweiterte Bearbeitung“ wird vom Visual standardmäßig nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="298be-107">By default, the visual doesn't support advanced edit mode.</span></span> <span data-ttu-id="298be-108">Falls ein anderes Verhalten gewünscht ist, können Sie dies explizit in der *capabilities.json*-Datei des Visuals angeben, indem Sie die `advancedEditModeSupport`-Eigenschaft festlegen.</span><span class="sxs-lookup"><span data-stu-id="298be-108">If a different behavior is required, you can explicitly state this in the visual's *capabilities.json* file by setting the `advancedEditModeSupport` property.</span></span>

<span data-ttu-id="298be-109">Mögliche Werte:</span><span class="sxs-lookup"><span data-stu-id="298be-109">The possible values are:</span></span>

- <span data-ttu-id="298be-110">`0`: NotSupported</span><span class="sxs-lookup"><span data-stu-id="298be-110">`0` - NotSupported</span></span>

- <span data-ttu-id="298be-111">`1`: SupportedNoAction</span><span class="sxs-lookup"><span data-stu-id="298be-111">`1` - SupportedNoAction</span></span>

- <span data-ttu-id="298be-112">`2`: SupportedInFocus</span><span class="sxs-lookup"><span data-stu-id="298be-112">`2` - SupportedInFocus</span></span>

## <a name="enter-advanced-edit-mode"></a><span data-ttu-id="298be-113">Wechseln zum Modus „Erweiterte Bearbeitung“</span><span class="sxs-lookup"><span data-stu-id="298be-113">Enter advanced edit mode</span></span>

<span data-ttu-id="298be-114">In den folgenden Fällen wird die Schaltfläche **Bearbeiten** angezeigt:</span><span class="sxs-lookup"><span data-stu-id="298be-114">An **Edit** button is displayed if:</span></span>

* <span data-ttu-id="298be-115">Die Eigenschaft `advancedEditModeSupport` ist in der *capabilities.json*-Datei entweder auf `SupportedNoAction` oder auf `SupportedInFocus` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="298be-115">The `advancedEditModeSupport` property is set in the *capabilities.json* file to either `SupportedNoAction` or `SupportedInFocus`.</span></span>

* <span data-ttu-id="298be-116">Das Visual wird im Berichtsbearbeitungsmodus angezeigt.</span><span class="sxs-lookup"><span data-stu-id="298be-116">The visual is viewed in report editing mode.</span></span>

<span data-ttu-id="298be-117">Wenn die Eigenschaft `advancedEditModeSupport` in der *capabilities.json*-Datei fehlt oder auf `NotSupported` festgelegt ist, wird die Schaltfläche **Bearbeiten** nicht angezeigt.</span><span class="sxs-lookup"><span data-stu-id="298be-117">If `advancedEditModeSupport` property is missing from the *capabilities.json* file or set to `NotSupported`, the **Edit** button is not displayed.</span></span>

![Aktivieren des Bearbeitungsmodus](./media/edit-mode.png)

<span data-ttu-id="298be-119">Wenn Sie auf **Bearbeiten** klicken, empfängt das Visual einen update()-Aufruf, bei dem EditMode auf `Advanced` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="298be-119">When you select **Edit**, the visual gets an update() call with EditMode set to `Advanced`.</span></span> <span data-ttu-id="298be-120">Abhängig von dem Wert, der in der *capabilities.json*-Datei festgelegt wird, treten die folgenden Aktionen auf:</span><span class="sxs-lookup"><span data-stu-id="298be-120">Depending on the value that's set in the *capabilities.json* file, the following actions occur:</span></span>

* <span data-ttu-id="298be-121">`SupportedNoAction`: Es ist keine weitere Aktion durch den Host erforderlich.</span><span class="sxs-lookup"><span data-stu-id="298be-121">`SupportedNoAction`: No further action is required by the host.</span></span>
* <span data-ttu-id="298be-122">`SupportedInFocus`: Der Host öffnet das Visual im Fokusmodus.</span><span class="sxs-lookup"><span data-stu-id="298be-122">`SupportedInFocus`: The host pops out the visual into in focus mode.</span></span>

## <a name="exit-advanced-edit-mode"></a><span data-ttu-id="298be-123">Beenden des Modus „Erweiterte Bearbeitung“</span><span class="sxs-lookup"><span data-stu-id="298be-123">Exit advanced edit mode</span></span>

<span data-ttu-id="298be-124">Die Schaltfläche **Zurück zum Bericht** wird im folgenden Fall angezeigt:</span><span class="sxs-lookup"><span data-stu-id="298be-124">The **Back to report** button is displayed if:</span></span>

* <span data-ttu-id="298be-125">Die `advancedEditModeSupport`-Eigenschaft in der *capabilities.json*-Datei ist auf `SupportedInFocus` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="298be-125">The `advancedEditModeSupport` property is set in the *capabilities.json* file to `SupportedInFocus`.</span></span>
