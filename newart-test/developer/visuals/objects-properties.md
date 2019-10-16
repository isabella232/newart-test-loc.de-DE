---
title: Objekte und Eigenschaften von Power BI-Visuals
description: In diesem Artikel werden anpassbare Eigenschaften von Power BI-Visuals beschrieben.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b2043c6727e4cf8c5c46c4e277b01a9ea04a969b
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424120"
---
# <a name="objects-and-properties-of-power-bi-visuals"></a>Objekte und Eigenschaften von Power BI-Visuals

Objekte beschreiben anpassbare Eigenschaften, die einem Visual zugeordnet sind. Ein Objekt kann mehrere Eigenschaften aufweisen, und jede Eigenschaft verfügt über einen zugehörigen Typ, der beschreibt, was die Eigenschaft sein wird. In diesem Artikel werden Informationen zu Objekten und Eigenschaftstypen bereitgestellt.

`myCustomObject` ist der interne Name, mit dem auf das Objekt in `dataView` und `enumerateObjectInstances` verwiesen wird.

```json
"objects": {
    "myCustomObject": {
        "displayName": "My Object Name",
        "properties": { ... }
    }
}
```

## <a name="display-name"></a>Anzeigename

`displayName` ist der Name, der im Eigenschaftenbereich angezeigt wird.

## <a name="properties"></a>Eigenschaften

`properties` ist eine Zuordnung von Eigenschaften, die vom Entwickler definiert wurden.

```json
"properties": {
    "myFirstProperty": {
        "displayName": "firstPropertyName",
        "type": ValueTypeDescriptor | StructuralTypeDescriptor
    }
}
```

> [!NOTE]
> `show` ist eine spezielle Eigenschaft, die einen Schalter zum Ein-/Ausschalten des Objekts aktiviert.

Beispiel:

```json
"properties": {
    "show": {
        "displayName": "My Property Switch",
        "type": {"bool": true}
    }
}
```

### <a name="property-types"></a>Eigenschaftentypen

Es gibt zwei Eigenschaftentypen: `ValueTypeDescriptor` und `StructuralTypeDescriptor`.

#### <a name="value-type-descriptor"></a>ValueTypeDescriptor

`ValueTypeDescriptor`-Typen sind meistens primitiv und werden in der Regel als statische Objekte verwendet.

Es folgen einige allgemeine `ValueTypeDescriptor`-Elemente:

```typescript
export interface ValueTypeDescriptor {
    text?: boolean;
    numeric?: boolean;
    integer?: boolean;
    bool?: boolean;
}
```

#### <a name="structural-type-descriptor"></a>StructuralTypeDescriptor

`StructuralTypeDescriptor`-Typen werden meistens für datengebundene Objekte verwendet.
Der gängigste `StructuralTypeDescriptor`-Typ ist *fill*.

```typescript
export interface StructuralTypeDescriptor {
    fill?: FillTypeDescriptor;
}
```

## <a name="gradient-property"></a>Gradient-Eigenschaft

Die Gradient-Eigenschaft kann nicht als Standardeigenschaft festgelegt werden. Stattdessen müssen Sie eine Regel für die Ersetzung der Farbauswahleigenschaft (*fill*-Typ) festlegen.

Im folgenden Code wird ein Beispiel dafür veranschaulicht:

```json
"properties": {
    "showAllDataPoints": {
        "displayName": "Show all",
        "displayNameKey": "Visual_DataPoint_Show_All",
        "type": {
            "bool": true
        }
    },
    "fill": {
        "displayName": "Fill",
        "displayNameKey": "Visual_Fill",
        "type": {
            "fill": {
                "solid": {
                    "color": true
                }
            }
        }
    },
    "fillRule": {
        "displayName": "Color saturation",
        "displayNameKey": "Visual_ColorSaturation",
        "type": {
            "fillRule": {}
        },
        "rule": {
            "inputRole": "Gradient",
            "output": {
                "property": "fill",
                    "selector": [
                        "Category"
                    ]
            }
        }
    }
}
```

Achten Sie auf die Eigenschaften *fill* und *fillRule*. Die erste Eigenschaft ist die Farbauswahl und die zweite ist die Ersetzungsregel für den Farbverlauf, der die *fill-Eigenschaft* `visually` ersetzt, wenn die Bedingungen der Regel erfüllt werden.

Die Verknüpfung zwischen der *fill*-Eigenschaft und der Ersetzungsregel wird im Abschnitt `"rule"`>`"output"` der *fillRule*-Eigenschaft festgelegt.

Die `"Rule"`>`"InputRole"`-Eigenschaft legt fest, durch welche Datenrolle die Regel (Bedingung) ausgelöst wird. Wenn die `"Gradient"`-Datenrolle in diesem Beispiel die Daten enthält, wird die Regel auf die `"fill"`-Eigenschaft angewendet.

Im folgenden Code sehen Sie ein Beispiel für die Datenrolle, die die Füllregel auslöst (`the last item`):

```json
{
    "dataRoles": [
            {
                "name": "Category",
                "kind": "Grouping",
                "displayName": "Details",
                "displayNameKey": "Role_DisplayName_Details"
            },
            {
                "name": "Series",
                "kind": "Grouping",
                "displayName": "Legend",
                "displayNameKey": "Role_DisplayName_Legend"
            },
            {
                "name": "Gradient",
                "kind": "Measure",
                "displayName": "Color saturation",
                "displayNameKey": "Role_DisplayName_Gradient"
            }
    ]
}
```

## <a name="the-enumerateobjectinstances-method"></a>Die enumerateObjectInstances-Methode

Sie benötigen eine Funktion namens `enumerateObjectInstances` in Ihrem benutzerdefinierten Visual, um Objekte effektiv zu verwenden. Diese Funktion füllt den Eigenschaftenbereich mit Objekten auf und bestimmt auch, wo Ihre Objekte in dataView gebunden werden sollen.  

Ein typisches Beispiel sieht folgendermaßen aus:

```typescript
public enumerateObjectInstances(options: EnumerateVisualObjectInstancesOptions): VisualObjectInstanceEnumeration {
    let objectName: string = options.objectName;
    let objectEnumeration: VisualObjectInstance[] = [];

    switch( objectName ) {
        case 'myCustomObject':
            objectEnumeration.push({
                objectName: objectName,
                properties: { ... },
                selector: { ... }
            });
            break;
    };

    return objectEnumeration;
}
```

### <a name="properties"></a>Eigenschaften

Die Eigenschaften in `enumerateObjectInstances` entsprechen den in Ihren Funktionen definierten Eigenschaften. Ein Beispiel finden Sie am Ende dieses Artikels.

### <a name="objects-selector"></a>Objektselektor

Der Selektor in `enumerateObjectInstances` bestimmt, wo die einzelnen Objekte in dataView gebunden sind. Es gibt vier verschiedene Optionen.

#### <a name="static"></a>static

Dieses Objekt wird wie hier gezeigt an `dataviews[index].metadata.objects`-Metadaten gebunden.

```typescript
selector: null
```

#### <a name="columns"></a>columns

Dieses Objekt wird an Spalten mit entsprechendem `QueryName` gebunden.

```typescript
selector: {
    metadata: 'QueryName'
}
```

#### <a name="selector"></a>selector

Dieses Objekt wird an das Element gebunden, für das Sie `selectionID` erstellt haben. In diesem Beispiel wird davon ausgegangen, dass Sie `selectionID`s für einige Datenpunkte erstellt haben und diese in einer Schleife durchlaufen.

```typescript
for (let dataPoint in dataPoints) {
    ...
    selector: dataPoint.selectionID.getSelector()
}
```

#### <a name="scope-identity"></a>ScopeIdentity

Dieses Objekt wird am Schnittpunkt von Gruppen an bestimmte Werte gebunden. Wenn Sie beispielsweise über die Kategorien `["Jan", "Feb", "March", ...]` und die Reihe `["Small", "Medium", "Large"]` verfügen, möchten Sie möglicherweise über ein Objekt am Schnittpunkt der Werte verfügen, die mit `Feb` und `Large` übereinstimmen. Hierzu könnten Sie die `DataViewScopeIdentity`-Eigenschaft beider Spalten abrufen, diese an die Variable `identities` pushen und diese Syntax mit dem Selektor verwenden.

```typescript
selector: {
    data: <DataViewScopeIdentity[]>identities
}
```

##### <a name="example"></a>Beispiel

Im folgenden Beispiel wird gezeigt, wie eine objectEnumeration-Eigenschaft für ein customColor-Objekt mit einer *fill*-Eigenschaft aussehen würde. Dieses Objekt soll wie im Folgenden gezeigt statisch an `dataViews[index].metadata.objects` gebunden werden:

```typescript
objectEnumeration.push({
    objectName: "customColor",
    displayName: "Custom Color",
    properties: {
        fill: {
            solid: {
                color: dataPoint.color
            }
        }
    },
    selector: null
});
```
