---
title: Aktivieren der Funktion „Slicer synchronisieren“ in Power BI-Visuals
description: In diesem Artikel wird beschrieben, wie die Funktion „Slicer synchronisieren“ zu Power BI-Visuals hinzugefügt werden kann.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 47f0148528d1ccfd451aa8e8ed87b4bec99d087e
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424310"
---
# <a name="sync-slicers-in-power-bi-visuals"></a>Synchronisieren von Slicern in Power BI-Visuals

Zur Unterstützung der Funktion [Slicer synchronisieren](https://docs.microsoft.com/power-bi/desktop-slicers) muss die benutzerdefinierte Slicer-Visual API-Version 1.13 oder höher verwenden werden.

Außerdem müssen Sie wie im folgenden Code veranschaulicht die Option in der Datei *capabilities.json* aktivieren:

```json
{
    ...
    "supportsHighlight": true,
    "suppressDefaultTitle": true,
    "supportsSynchronizingFilterState": true,
    "sorting": {
        "default": {}
    }
}
```

Nachdem Sie die Datei *capabilities.json* aktualisiert haben, können Sie den Optionsbereich **Slicers synchronisieren** anzeigen, wenn Sie Ihr benutzerdefiniertes Slicer-Visual auswählen.

> [!NOTE]
> Die Funktion „Slicer synchronisieren“ unterstützt nicht mehr als ein Feld. Wenn Ihr Slicer mehr als ein Feld (**Kategorie** oder **Measure**) aufweist, ist die Funktion deaktiviert.

![Der Bereich „Slicer synchronisieren“](./media/sync-slicers-panel.png)

Im Bereich **Slicer synchronisieren** sehen Sie, dass die Sichtbarkeit und Filterung des Slicers auf mehrere Berichtsseiten angewendet werden können.
