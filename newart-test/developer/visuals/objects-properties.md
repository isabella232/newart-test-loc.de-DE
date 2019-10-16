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
# <a name="objects-and-properties-of-power-bi-visuals"></a><span data-ttu-id="bd0ba-103">Objekte und Eigenschaften von Power BI-Visuals</span><span class="sxs-lookup"><span data-stu-id="bd0ba-103">Objects and properties of Power BI visuals</span></span>

<span data-ttu-id="bd0ba-104">Objekte beschreiben anpassbare Eigenschaften, die einem Visual zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-104">Objects describe customizable properties that are associated with a visual.</span></span> <span data-ttu-id="bd0ba-105">Ein Objekt kann mehrere Eigenschaften aufweisen, und jede Eigenschaft verfügt über einen zugehörigen Typ, der beschreibt, was die Eigenschaft sein wird.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-105">An object can have multiple properties, and each property has an associated type that describes what the property will be.</span></span> <span data-ttu-id="bd0ba-106">In diesem Artikel werden Informationen zu Objekten und Eigenschaftstypen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-106">This article provides information about objects and property types.</span></span>

<span data-ttu-id="bd0ba-107">`myCustomObject` ist der interne Name, mit dem auf das Objekt in `dataView` und `enumerateObjectInstances` verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-107">`myCustomObject` is the internal name that's used to reference the object within `dataView` and `enumerateObjectInstances`.</span></span>

```json
"objects": {
    "myCustomObject": {
        "displayName": "My Object Name",
        "properties": { ... }
    }
}
```

## <a name="display-name"></a><span data-ttu-id="bd0ba-108">Anzeigename</span><span class="sxs-lookup"><span data-stu-id="bd0ba-108">Display name</span></span>

<span data-ttu-id="bd0ba-109">`displayName` ist der Name, der im Eigenschaftenbereich angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-109">`displayName` is the name that will be shown in the property pane.</span></span>

## <a name="properties"></a><span data-ttu-id="bd0ba-110">Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="bd0ba-110">Properties</span></span>

<span data-ttu-id="bd0ba-111">`properties` ist eine Zuordnung von Eigenschaften, die vom Entwickler definiert wurden.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-111">`properties` is a map of properties that are defined by the developer.</span></span>

```json
"properties": {
    "myFirstProperty": {
        "displayName": "firstPropertyName",
        "type": ValueTypeDescriptor | StructuralTypeDescriptor
    }
}
```

> [!NOTE]
> <span data-ttu-id="bd0ba-112">`show` ist eine spezielle Eigenschaft, die einen Schalter zum Ein-/Ausschalten des Objekts aktiviert.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-112">`show` is a special property that enables a switch to toggle the object.</span></span>

<span data-ttu-id="bd0ba-113">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd0ba-113">Example:</span></span>

```json
"properties": {
    "show": {
        "displayName": "My Property Switch",
        "type": {"bool": true}
    }
}
```

### <a name="property-types"></a><span data-ttu-id="bd0ba-114">Eigenschaftentypen</span><span class="sxs-lookup"><span data-stu-id="bd0ba-114">Property types</span></span>

<span data-ttu-id="bd0ba-115">Es gibt zwei Eigenschaftentypen: `ValueTypeDescriptor` und `StructuralTypeDescriptor`.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-115">There are two property types: `ValueTypeDescriptor` and `StructuralTypeDescriptor`.</span></span>

#### <a name="value-type-descriptor"></a><span data-ttu-id="bd0ba-116">ValueTypeDescriptor</span><span class="sxs-lookup"><span data-stu-id="bd0ba-116">Value type descriptor</span></span>

<span data-ttu-id="bd0ba-117">`ValueTypeDescriptor`-Typen sind meistens primitiv und werden in der Regel als statische Objekte verwendet.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-117">`ValueTypeDescriptor` types are mostly primitive and are ordinarily used as a static object.</span></span>

<span data-ttu-id="bd0ba-118">Es folgen einige allgemeine `ValueTypeDescriptor`-Elemente:</span><span class="sxs-lookup"><span data-stu-id="bd0ba-118">Here are some of the common `ValueTypeDescriptor` elements:</span></span>

```typescript
export interface ValueTypeDescriptor {
    text?: boolean;
    numeric?: boolean;
    integer?: boolean;
    bool?: boolean;
}
```

#### <a name="structural-type-descriptor"></a><span data-ttu-id="bd0ba-119">StructuralTypeDescriptor</span><span class="sxs-lookup"><span data-stu-id="bd0ba-119">Structural type descriptor</span></span>

<span data-ttu-id="bd0ba-120">`StructuralTypeDescriptor`-Typen werden meistens für datengebundene Objekte verwendet.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-120">`StructuralTypeDescriptor` types are mostly used for data-bound objects.</span></span>
<span data-ttu-id="bd0ba-121">Der gängigste `StructuralTypeDescriptor`-Typ ist *fill*.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-121">The most common `StructuralTypeDescriptor` type is *fill*.</span></span>

```typescript
export interface StructuralTypeDescriptor {
    fill?: FillTypeDescriptor;
}
```

## <a name="gradient-property"></a><span data-ttu-id="bd0ba-122">Gradient-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="bd0ba-122">Gradient property</span></span>

<span data-ttu-id="bd0ba-123">Die Gradient-Eigenschaft kann nicht als Standardeigenschaft festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-123">The gradient property is a property that can't be set as a standard property.</span></span> <span data-ttu-id="bd0ba-124">Stattdessen müssen Sie eine Regel für die Ersetzung der Farbauswahleigenschaft (*fill*-Typ) festlegen.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-124">Instead, you need to set a rule for the substitution of the color picker property (*fill* type).</span></span>

<span data-ttu-id="bd0ba-125">Im folgenden Code wird ein Beispiel dafür veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="bd0ba-125">An example is shown in the following code:</span></span>

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

<span data-ttu-id="bd0ba-126">Achten Sie auf die Eigenschaften *fill* und *fillRule*.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-126">Pay attention to the *fill* and *fillRule* properties.</span></span> <span data-ttu-id="bd0ba-127">Die erste Eigenschaft ist die Farbauswahl und die zweite ist die Ersetzungsregel für den Farbverlauf, der die *fill-Eigenschaft* `visually` ersetzt, wenn die Bedingungen der Regel erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-127">The first is the color picker, and the second is the substitution rule for the gradient that will replace the *fill property*, `visually`, when the rule conditions are met.</span></span>

<span data-ttu-id="bd0ba-128">Die Verknüpfung zwischen der *fill*-Eigenschaft und der Ersetzungsregel wird im Abschnitt `"rule"`>`"output"` der *fillRule*-Eigenschaft festgelegt.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-128">This link between the *fill* property and the substitution rule is set in the `"rule"`>`"output"` section of the *fillRule* property.</span></span>

<span data-ttu-id="bd0ba-129">Die `"Rule"`>`"InputRole"`-Eigenschaft legt fest, durch welche Datenrolle die Regel (Bedingung) ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-129">`"Rule"`>`"InputRole"` property sets which data role triggers the rule (condition).</span></span> <span data-ttu-id="bd0ba-130">Wenn die `"Gradient"`-Datenrolle in diesem Beispiel die Daten enthält, wird die Regel auf die `"fill"`-Eigenschaft angewendet.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-130">In this example, if data role `"Gradient"` contains data, the rule is applied for the `"fill"` property.</span></span>

<span data-ttu-id="bd0ba-131">Im folgenden Code sehen Sie ein Beispiel für die Datenrolle, die die Füllregel auslöst (`the last item`):</span><span class="sxs-lookup"><span data-stu-id="bd0ba-131">An example of the data role that triggers the fill rule (`the last item`) is shown in the following code:</span></span>

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

## <a name="the-enumerateobjectinstances-method"></a><span data-ttu-id="bd0ba-132">Die enumerateObjectInstances-Methode</span><span class="sxs-lookup"><span data-stu-id="bd0ba-132">The enumerateObjectInstances method</span></span>

<span data-ttu-id="bd0ba-133">Sie benötigen eine Funktion namens `enumerateObjectInstances` in Ihrem benutzerdefinierten Visual, um Objekte effektiv zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-133">To use objects effectively, you need a function in your custom visual called `enumerateObjectInstances`.</span></span> <span data-ttu-id="bd0ba-134">Diese Funktion füllt den Eigenschaftenbereich mit Objekten auf und bestimmt auch, wo Ihre Objekte in dataView gebunden werden sollen.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-134">This function populates the property pane with objects and also determines where your objects should be bound within the dataView.</span></span>  

<span data-ttu-id="bd0ba-135">Ein typisches Beispiel sieht folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="bd0ba-135">Here is what a typical setup looks like:</span></span>

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

### <a name="properties"></a><span data-ttu-id="bd0ba-136">Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="bd0ba-136">Properties</span></span>

<span data-ttu-id="bd0ba-137">Die Eigenschaften in `enumerateObjectInstances` entsprechen den in Ihren Funktionen definierten Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-137">The properties in `enumerateObjectInstances` reflect the properties that you defined in your capabilities.</span></span> <span data-ttu-id="bd0ba-138">Ein Beispiel finden Sie am Ende dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-138">For an example, go to the end of this article.</span></span>

### <a name="objects-selector"></a><span data-ttu-id="bd0ba-139">Objektselektor</span><span class="sxs-lookup"><span data-stu-id="bd0ba-139">Objects selector</span></span>

<span data-ttu-id="bd0ba-140">Der Selektor in `enumerateObjectInstances` bestimmt, wo die einzelnen Objekte in dataView gebunden sind.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-140">The selector in `enumerateObjectInstances` determines where each object is bound in the dataView.</span></span> <span data-ttu-id="bd0ba-141">Es gibt vier verschiedene Optionen.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-141">There are four distinct options.</span></span>

#### <a name="static"></a><span data-ttu-id="bd0ba-142">static</span><span class="sxs-lookup"><span data-stu-id="bd0ba-142">static</span></span>

<span data-ttu-id="bd0ba-143">Dieses Objekt wird wie hier gezeigt an `dataviews[index].metadata.objects`-Metadaten gebunden.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-143">This object is bound to metadata `dataviews[index].metadata.objects`, as shown here.</span></span>

```typescript
selector: null
```

#### <a name="columns"></a><span data-ttu-id="bd0ba-144">columns</span><span class="sxs-lookup"><span data-stu-id="bd0ba-144">columns</span></span>

<span data-ttu-id="bd0ba-145">Dieses Objekt wird an Spalten mit entsprechendem `QueryName` gebunden.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-145">This object is bound to columns with the matching `QueryName`.</span></span>

```typescript
selector: {
    metadata: 'QueryName'
}
```

#### <a name="selector"></a><span data-ttu-id="bd0ba-146">selector</span><span class="sxs-lookup"><span data-stu-id="bd0ba-146">selector</span></span>

<span data-ttu-id="bd0ba-147">Dieses Objekt wird an das Element gebunden, für das Sie `selectionID` erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-147">This object is bound to the element that you created a `selectionID` for.</span></span> <span data-ttu-id="bd0ba-148">In diesem Beispiel wird davon ausgegangen, dass Sie `selectionID`s für einige Datenpunkte erstellt haben und diese in einer Schleife durchlaufen.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-148">In this example, let's assume that we've created `selectionID`s for some data points, and that we're looping through them.</span></span>

```typescript
for (let dataPoint in dataPoints) {
    ...
    selector: dataPoint.selectionID.getSelector()
}
```

#### <a name="scope-identity"></a><span data-ttu-id="bd0ba-149">ScopeIdentity</span><span class="sxs-lookup"><span data-stu-id="bd0ba-149">Scope identity</span></span>

<span data-ttu-id="bd0ba-150">Dieses Objekt wird am Schnittpunkt von Gruppen an bestimmte Werte gebunden.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-150">This object is bound to particular values at the intersection of groups.</span></span> <span data-ttu-id="bd0ba-151">Wenn Sie beispielsweise über die Kategorien `["Jan", "Feb", "March", ...]` und die Reihe `["Small", "Medium", "Large"]` verfügen, möchten Sie möglicherweise über ein Objekt am Schnittpunkt der Werte verfügen, die mit `Feb` und `Large` übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-151">For example, if you have categories `["Jan", "Feb", "March", ...]` and series `["Small", "Medium", "Large"]`, you might want to have an object at the intersection of values that match `Feb` and `Large`.</span></span> <span data-ttu-id="bd0ba-152">Hierzu könnten Sie die `DataViewScopeIdentity`-Eigenschaft beider Spalten abrufen, diese an die Variable `identities` pushen und diese Syntax mit dem Selektor verwenden.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-152">To accomplish this, you could get the `DataViewScopeIdentity` of both columns, push them to variable `identities`, and use this syntax with the selector.</span></span>

```typescript
selector: {
    data: <DataViewScopeIdentity[]>identities
}
```

##### <a name="example"></a><span data-ttu-id="bd0ba-153">Beispiel</span><span class="sxs-lookup"><span data-stu-id="bd0ba-153">Example</span></span>

<span data-ttu-id="bd0ba-154">Im folgenden Beispiel wird gezeigt, wie eine objectEnumeration-Eigenschaft für ein customColor-Objekt mit einer *fill*-Eigenschaft aussehen würde.</span><span class="sxs-lookup"><span data-stu-id="bd0ba-154">The following example shows what one objectEnumeration would look like for a customColor object with one property, *fill*.</span></span> <span data-ttu-id="bd0ba-155">Dieses Objekt soll wie im Folgenden gezeigt statisch an `dataViews[index].metadata.objects` gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="bd0ba-155">We want this object bound statically to `dataViews[index].metadata.objects`, as shown:</span></span>

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
