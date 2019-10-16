---
title: QuickInfos in Power BI-Visuals
description: In diesem Artikel wird erläutert, wie Sie QuickInfos in Power BI-Visuals anzeigen können.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 3b530da369dad2bcecde47c8f92202387abd42a6
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424215"
---
# <a name="tooltips-in-power-bi-visuals"></a>QuickInfos in Power BI-Visuals

In Visuals können jetzt auch Power BI-QuickInfos verwendet werden. Interaktionen für QuickInfos in Power BI:

* Anzeigen einer QuickInfo
* Ausblenden einer QuickInfo
* Verschieben einer QuickInfo

Eine QuickInfo kann als Textelement mit einem Titel, einem Wert in einer bestimmten Farbe und Deckkraft und einer Gruppe von Koordinaten angezeigt werden. Diese Daten werden anschließend der API zur Verfügung gestellt und vom Power BI-Host auf dieselbe Weise gerendert wie QuickInfos für native Visuals.

Auf dem folgenden Screenshot ist eine QuickInfo in einem beispielhaften Balkendiagramm zu sehen:

![QuickInfo in beispielhaftem Balkendiagramm](./media/tooltips-in-samplebarchart.png)

Auf dem obigen Screenshot mit der QuickInfo wird eine einzelne Kategorie und ein einzelner Wert für ein Balkendiagramm veranschaulicht. Sie können eine einzelne QuickInfo so erweitern, dass mehrere Werte angezeigt werden.

## <a name="manage-tooltips"></a>Verwalten von QuickInfos

Die Schnittstelle für die Verwaltung von QuickInfos heißt ITooltipService. Über diese wird der Host darüber benachrichtigt, dass eine QuickInfo angezeigt, entfernt oder verschoben werden muss.

```typescript
    interface ITooltipService {
        enabled(): boolean;
        show(options: TooltipShowOptions): void;
        move(options: TooltipMoveOptions): void;
        hide(options: TooltipHideOptions): void;
    }
```

Das Visual muss die eigenen Mausereignisse überwachen und bei Bedarf den Delegaten `show()`, `move()` oder `hide()` mit dem Inhalt aufrufen, der in den `Tooltip****Options`-Objekten enthalten ist.
Durch `TooltipShowOptions` und `TooltipHideOptions` wird wiederum definiert, was bei Auftreten dieser Ereignisse angezeigt wird und wie sich die QuickInfo verhält.

Da beim Aufrufen dieser Methoden auch Benutzerereignisse wie Mausbewegungen und Touchereignisse eine Rolle spielen, empfiehlt es sich, Listener für diese Ereignisse zu erstellen. Dadurch werden dann die `TooltipService`-Member aufgerufen.
In unserem Beispiel werden Daten in der `TooltipServiceWrapper`-Klasse aggregiert.

### <a name="the-tooltipservicewrapper-class"></a>Die TooltipServiceWrapper-Klasse

Diese Klasse ist dafür verantwortlich, die Instanz von `TooltipService` aufzunehmen, D3-Mausereignisse oder relevante Elemente zu überwachen und dann bei Bedarf `show()` oder `hide()` für die Elemente aufzurufen.

Die Klasse enthält und verwaltet relevante Zustands- und Logikinformationen für diese Ereignisse, die hauptsächlich auf die Verknüpfung mit dem zugrunde liegenden D3-Code ausgerichtet sind. Die Verknüpfung und Konvertierung von D3-Code wird in diesem Artikel nicht behandelt.

Den vollständigen Beispielcode finden Sie im [SampleBarChart-Visualrepository](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/commit/981b021612d7b333adffe9f723ab27783c76fb14).

### <a name="create-tooltipservicewrapper"></a>Erstellen einer TooltipServiceWrapper-Instanz

Der Konstruktor des Balkendiagramms verfügt jetzt über einen `TooltipServiceWrapper`-Member, der im Konstruktor mit der `tooltipService`-Hostinstanz instanziiert wird.

```typescript
        private tooltipServiceWrapper: ITooltipServiceWrapper;

        this.tooltipServiceWrapper = createTooltipServiceWrapper(this.host.tooltipService, options.element);
```

Die `TooltipServiceWrapper`-Klasse enthält die `tooltipService`-Instanz zusätzlich zum D3-Stammelement der Visual- und Touchparameter.

```typescript
    class TooltipServiceWrapper implements ITooltipServiceWrapper {
        private handleTouchTimeoutId: number;
        private visualHostTooltipService: ITooltipService;
        private rootElement: Element;
        private handleTouchDelay: number;

        constructor(tooltipService: ITooltipService, rootElement: Element, handleTouchDelay: number) {
            this.visualHostTooltipService = tooltipService;
            this.handleTouchDelay = handleTouchDelay;
            this.rootElement = rootElement;
        }
        .
        .
        .
    }
```

Der einzige Einstiegspunkt, über den diese Klasse Ereignislistener registrieren kann, ist die `addTooltip`-Methode.

### <a name="the-addtooltip-method"></a>Die addTooltip-Methode

```typescript
        public addTooltip<T>(
            selection: d3.Selection<Element>,
            getTooltipInfoDelegate: (args: TooltipEventArgs<T>) => VisualTooltipDataItem[],
            getDataPointIdentity: (args: TooltipEventArgs<T>) => ISelectionId,
            reloadTooltipDataOnMouseMove?: boolean): void {

            if (!selection || !this.visualHostTooltipService.enabled()) {
                return;
            }
        ...
        ...
        }
```

* **selection: d3.Selection:<Element>** die D3-Elemente, über die QuickInfos verarbeitet werden.

* **getTooltipInfoDelegate: (args: TooltipEventArgs<T>) => VisualTooltipDataItem[]:** der Delegat zum Auffüllen des anzuzeigenden QuickInfo-Inhalts für einen Kontext.

* **getDataPointIdentity: (args: TooltipEventArgs<T>) => ISelectionId:** der Delegat zum Abrufen der Datenpunkt-ID (in diesem Beispiel nicht verwendet). 

* **reloadTooltipDataOnMouseMove? boolean:** ein boolescher Wert, der angibt, ob die QuickInfo-Daten während eines MouseMove-Ereignisses aktualisiert werden (in diesem Beispiel nicht verwendet).

Wie Sie sehen, wird `addTooltip` ohne Aktion beendet, wenn `tooltipService` deaktiviert ist oder keine Auswahl vorliegt.

### <a name="call-the-show-method-to-display-a-tooltip"></a>Aufrufen der show-Methode zum Anzeigen einer QuickInfo

Die `addTooltip`-Methode überwacht, ob ein D3-`mouseover`-Ereignis auftritt. Dies wird im folgenden Code gezeigt:

```typescript
        ...
        ...
        selection.on("mouseover.tooltip", () => {
            // Ignore mouseover while handling touch events
            if (!this.canDisplayTooltip(d3.event))
                return;

            let tooltipEventArgs = this.makeTooltipEventArgs<T>(rootNode, true, false);
            if (!tooltipEventArgs)
                return;

            let tooltipInfo = getTooltipInfoDelegate(tooltipEventArgs);
            if (tooltipInfo == null)
                return;

            let selectionId = getDataPointIdentity(tooltipEventArgs);

            this.visualHostTooltipService.show({
                coordinates: tooltipEventArgs.coordinates,
                isTouchEvent: false,
                dataItems: tooltipInfo,
                identities: selectionId ? [selectionId] : [],
            });
        });
```

* **makeTooltipEventArgs:** Extrahiert den Kontext aus den ausgewählten D3-Elementen in tooltipEventArg. Die Koordinaten werden ebenfalls berechnet.

* **getTooltipInfoDelegate:** Anschließend wird der QuickInfo-Inhalt auf der Basis von tooltipEventArgs erstellt. Dies ist ein Rückruf an die BarChart-Klasse, da es sich hier um die Logikinformationen des Visuals handelt. Dies ist der tatsächliche Textinhalt, der in der QuickInfo angezeigt wird.

* **getDataPointIdentity:** in diesem Beispiel nicht verwendet.

* **this.visualHostTooltipService.show:** der Aufruf zur Anzeige der QuickInfo.  

Weitere praktische Informationen finden Sie in den Beispielen für das `mouseout`-Ereignis und das `mousemove`-Ereignis.

Weitere Informationen finden Sie im [SampleBarChart-Visualrepository](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/commit/981b021612d7b333adffe9f723ab27783c76fb14).

### <a name="populate-the-tooltip-content-by-the-gettooltipdata-method"></a>Auffüllen des QuickInfo-Inhalts durch die getTooltipData-Methode

Die BarChart-Klasse wurde mit einem `getTooltipData`-Member hinzugefügt, durch den `category`, `value` und `color` des Datenpunkts einfach in ein VisualTooltipDataItem[]-Element extrahiert wird.

```typescript
        private static getTooltipData(value: any): VisualTooltipDataItem[] {
            return [{
                displayName: value.category,
                value: value.value.toString(),
                color: value.color,
                header: 'ToolTip Title'
            }];
        }
```

In der vorangehenden Implementierung ist der `header`-Member eine Konstante. Er kann jedoch auch für komplexere Implementierungen verwendet werden, die dynamische Werte erfordern. Sie können `VisualTooltipDataItem[]` mit mehreren Elementen auffüllen, um der QuickInfo mehrere Zeilen hinzuzufügen. Dies kann in Visuals wie gestapelten Balkendiagrammen nützlich sein, in denen QuickInfos Daten aus mehreren Datenpunkten enthalten können.

### <a name="call-the-addtooltip-method"></a>Aufrufen der addTooltip-Methode

Der letzte Schritt besteht darin, die `addTooltip`-Methode aufzurufen, wenn sich die Daten ändern. Dieser Aufruf findet in der `BarChart.update()`-Methode statt. Durch den Aufruf wird die Auswahl aller bar-Elemente überwacht und wie oben erwähnt nur `BarChart.getTooltipData()` übergeben.

```typescript
        this.tooltipServiceWrapper.addTooltip(this.barContainer.selectAll('.bar'),
            (tooltipEvent: TooltipEventArgs<number>) => BarChart.getTooltipData(tooltipEvent.data),
            (tooltipEvent: TooltipEventArgs<number>) => null);
```

## <a name="add-report-page-tooltips"></a>Hinzufügen von QuickInfos für Berichtsseiten

Wenn Sie QuickInfos für Berichtsseiten hinzufügen möchten, finden Sie die meisten erforderlichen Änderungen in der Datei *capabilities.json*.

Ein Beispielschema:

```json
{
    "tooltips": {
        "supportedTypes": {
            "default": true,
            "canvas": true
        },
        "roles": [
            "tooltips"
        ]
    }
}
```

QuickInfos für Berichtseiten können Sie im Bereich **Format** definieren.

![QuickInfo für Berichtsseiten](media/report-page-tooltip.png)

* `supportedTypes`: die QuickInfo-Konfiguration, die vom Visual unterstützt wird und im Bereich „Felder“ angezeigt wird. 
   * `default`: gibt an, ob die „automatische“ Bindung von QuickInfos über das Datenfeld unterstützt wird. 
   * `canvas`: gibt an, ob QuickInfos für Berichtsseiten unterstützt werden.

* `roles`: optionale Einstellung, mit der festgelegt wird, welche Datenrollen an die ausgewählte QuickInfo-Option im Bereich „Felder“ gebunden werden sollen.

Weitere Informationen finden Sie unter [Richtlinien zur Nutzung von QuickInfos für Berichtsseiten](https://powerbi.microsoft.com/blog/power-bi-desktop-march-2018-feature-summary/#tooltips).

Der Power BI-Host ruft zuerst `ITooltipService.Show(options: TooltipShowOptions)` oder `ITooltipService.Move(options: TooltipMoveOptions)` auf, um das QuickInfo für die Berichtsseite anzuzeigen. Anschließend wird die selectionId genutzt (`identities`-Eigenschaft des vorangehenden `options`-Arguments). Die selectionId sollte die ausgewählten Daten (Kategorie, Reihe usw.) des Elements darstellen, auf das Sie gezeigt haben, damit die selectionId vom QuickInfo abgerufen werden kann.

Wie die selectionId an die QuickInfo-Anzeigeaufrufe gesendet werden kann, sehen Sie im folgenden Codebeispiel:

```typescript
    this.tooltipServiceWrapper.addTooltip(this.barContainer.selectAll('.bar'),
        (tooltipEvent: TooltipEventArgs<number>) => BarChart.getTooltipData(tooltipEvent.data),
        (tooltipEvent: TooltipEventArgs<number>) => tooltipEvent.data.selectionID);
```
