---
title: Grundlegendes zur Zuordnung von Datenansichten in Power BI-Visuals
description: In diesem Artikel wird beschrieben, wie Power BI Daten transformiert, bevor diese an Visuals übergeben werden.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 77d5d6423c8246712d12dcc041d32ae73f68b72e
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424450"
---
# <a name="understand-data-view-mapping-in-power-bi-visuals"></a>Grundlegendes zur Zuordnung von Datenansichten in Power BI-Visuals

In diesem Artikel wird erläutert, wie Datenansichten zugeordnet werden. Außerdem wird beschrieben, wie Datenrollen zusammenhängen und wie Sie für sie Bedingungen festlegen. Darüber hinaus werden die einzelnen `dataMappings`-Typen behandelt.

Jede gültige Zuordnung erzeugt eine Datenansicht. Aktuell wird jedoch nur eine Abfrage pro Visual unterstützt. Üblicherweise ist daher nur eine Datensicht verfügbar. Sie können allerdings mehrere Datenzuordnungen mit unterschiedlichen Bedingungen bereitstellen, die folgende Möglichkeiten bieten:

```json
"dataViewMappings": [
    {
        "conditions": [ ... ],
        "categorical": { ... },
        "single": { ... },
        "table": { ... },
        "matrix": { ... }
    }
]
```

Power BI erstellt nur dann eine Zuordnung zu einer Datenansicht, wenn die gültige Zuordnung in `dataViewMappings` angegeben ist.

Anders ausgedrückt: `categorical` kann in `dataViewMappings` definiert werden, andere Zuordnungen wie `table` oder `single` jedoch möglicherweise nicht. Beispiel:

```json
"dataViewMappings": [
    {
        "categorical": { ... }
    }
]
```

Power BI erzeugt eine Datenansicht mit einer einzelnen `categorical`-Zuordnung. `table` und andere Zuordnungen sind nicht definiert:

```javascript
{
    "categorical": {
        "categories": [ ... ],
        "values": [ ... ]
    },
    "metadata": { ... }
}
```

## <a name="conditions"></a>„conditions“

In diesem Abschnitt werden Bedingungen für eine bestimmte Datenzuordnung beschrieben. Sie können mehrere Gruppen von Bedingungen bereitstellen. Wenn die Daten mit einer der beschriebenen Bedingungsgruppen übereinstimmen, werden die Daten vom Visual als gültig betrachtet.

Derzeit können Sie für jedes Feld einen minimalen und einen maximalen Wert angeben. Diese Werte stellen die Anzahl von Feldern dar, die an die Datenrolle gebunden werden können. 

> [!NOTE]
> Wenn eine Datenrolle in der Bedingung ausgelassen wird, kann sie über eine beliebige Anzahl von Feldern verfügen.

### <a name="example-1"></a>Beispiel 1

Sie können mehrere Felder in jede Datenrolle ziehen. In diesem Beispiel beschränken Sie die Kategorie auf ein Datenfeld und das Measure auf zwei Datenfelder.

```json
"conditions": [
    { "category": { "max": 1 }, "y": { "max": 2 } },
]
```

### <a name="example-2"></a>Beispiel 2

In diesem Beispiel ist eine von zwei Bedingungen erforderlich:
* Genau ein category-Datenfeld und genau zwei Measures
* Genau zwei Kategorien und genau ein Measure

```json
"conditions": [
    { "category": { "min": 1, "max": 1 }, "measure": { "min": 2, "max": 2 } },
    { "category": { "min": 2, "max": 2 }, "measure": { "min": 1, "max": 1 } }
]
```

## <a name="single-data-mapping"></a>Einzelne Datenzuordnung

Die einzelne Datenzuordnung ist die einfachste Form der Datenzuordnung. Sie akzeptiert ein einzelnes Measurefeld und gibt die Summe zurück. Bei einem numerischen Feld wird die Summe zurückgegeben. Andernfalls wird die Anzahl der eindeutigen Werte ermittelt.

Wenn Sie eine einzelne Datenzuordnung verwenden möchten, müssen Sie den Namen der Datenrolle festlegen, die Sie zuordnen möchten. Diese Zuordnung funktioniert nur mit einem einzelnen Measurefeld. Wenn ein zweites Feld zugewiesen ist, wird keine Datenansicht generiert. Es wird daher zusätzlich empfohlen, eine Bedingung bereitzustellen, die die Daten auf ein einzelnes Feld beschränkt.

> [!NOTE]
> Diese Datenzuordnung kann nicht in Verbindung mit einer anderen Datenzuordnung verwendet werden. Sie soll Daten auf einen einzelnen numerischen Wert reduzieren.

### <a name="example-3"></a>Beispiel 3

```json
"dataViewMappings": {
    "conditions": [
        { "Y": { "max": 1 } }
    ],
    "single": {
        "role": "Y"
    }
}  
```

Die resultierende Datenansicht enthält immer noch die anderen Typen („table“, „categorical“ usw.). Jede Zuordnung enthält jedoch nur den einzelnen Wert. Es empfiehlt sich, nur auf den Wert in „single“ zuzugreifen.

```JSON
{
    "dataView": [
        {
            "metadata": null,
            "categorical": null,
            "matrix": null,
            "table": null,
            "tree": null,
            "single": {
                "value": 94163140.3560001
            }
        }
    ]
}
```

## <a name="categorical-data-mapping"></a>Kategorische Datenzuordnung

Die kategorische Datenzuordnung wird verwendet, um eine oder zwei unabhängige Datengruppierungen zu erhalten.

### <a name="example-4"></a>Beispiel 4

Die Datenrollen aus dem vorherigen Beispiel sind wie folgt definiert:

```json
"dataRole":[
    {
        "displayName": "Category",
        "name": "category",
        "kind": "Grouping"
    },
    {
        "displayName": "Y Axis",
        "name": "measure",
        "kind": "Measure"
    }
]
```

Die Zuordnung sieht so aus:

```json
"dataViewMappings": {
    "categorical": {
        "categories": {
            "for": { "in": "category" }
        },
        "values": {
            "select": [
                { "bind": { "to": "measure" } }
            ]
        }
    }
}
```

Dies ist ein einfaches Beispiel. Es kann wie folgt interpretiert werden: Die `category`-Datenrolle wird so zugeordnet, dass für jedes Feld, das in `category` gezogen wird, die Daten `categorical.categories` zugeordnet werden. Außerdem wird die `measure`-Datenrolle `categorical.values` zugeordnet.

* **for...in:** fügt alle Elemente dieser Datenrolle in die Datenabfrage ein.
* **bind...to:** erzeugt dasselbe Ergebnis wie *for...in*, erwartet jedoch, dass die Datenrolle über eine Bedingung verfügt, die sie auf ein einzelnes Feld beschränkt.

### <a name="example-5"></a>Beispiel 5

Im folgenden Beispiel werden die ersten beiden Datenrollen aus dem vorherigen Beispiel verwendet. Zusätzlich werden `grouping` und `measure2` definiert.

```json
"dataRole":[
    {
        "displayName": "Category",
        "name": "category",
        "kind": "Grouping"
    },
    {
        "displayName": "Y Axis",
        "name": "measure",
        "kind": "Measure"
    },
    {
        "displayName": "Grouping with",
        "name": "grouping",
        "kind": "Grouping"
    },
    {
        "displayName": "X Axis",
        "name": "measure2",
        "kind": "Grouping"
    }
]
```

Die Zuordnung sieht so aus:

```json
"dataViewMappings":{
    "categorical": {
        "categories": {
            "for": { "in": "category" }
        },
        "values": {
            "group": {
                "by": "grouping",
                "select":[
                    { "bind": { "to": "measure" } },
                    { "bind": { "to": "measure2" } }
                ]
            }
        }
    }
}
```

Hier liegt der Unterschied in der Zuordnung von „categorical.values“. Das Beispiel kann wie folgt interpretiert werden: Die Datenrollen `measure` und `measure2` werden so zugeordnet, dass diese von der Datenrolle `grouping` gruppiert werden.

### <a name="example-6"></a>Beispiel 6

Die Datenrollen sehen wie folgt aus:

```json
"dataRoles": [
    {
        "displayName": "Categories",
        "name": "category",
        "kind": "Grouping"
    },
    {
        "displayName": "Measures",
        "name": "measure",
        "kind": "Measure"
    },
    {
        "displayName": "Series",
        "name": "series",
        "kind": "Measure"
    }
]
```

Die Zuordnung der Datensicht sieht wie folgt aus:

```json
"dataViewMappings": [
    {
        "categorical": {
            "categories": {
                "for": {
                    "in": "category"
                }
            },
            "values": {
                "group": {
                    "by": "series",
                    "select": [{
                            "for": {
                                "in": "measure"
                            }
                        }
                    ]
                }
            }
        }
    }
]
```

Die kategorische Datensicht kann beispielsweise wie folgt visualisiert werden:

| Kategorisch |  |  | | | |
|-----|-----|------|------|------|------|
| | Jahr | 2013 | 2014 | 2015 | 2016 |
| Land | | |
| USA | | x | x | 125 | 100 |
| Kanada | | x | 50 | 200 | x |
| Mexiko | | 300 | x | x | x |
| VEREINIGTES KÖNIGREICH | | x | x | 75 | x |

Power BI erstellt sie als kategorische Datenansicht. Dabei handelt es sich um die Gruppe von Kategorien.

```JSON
{
    "categorical": {
        "categories": [
            {
                "source": {...},
                "values": [
                    "Canada",
                    "Mexico",
                    "UK",
                    "USA"
                ],
                "identity": [...],
                "identityFields": [...],
            }
        ]
    }
}
```

Jede Kategorie wird auch einer Gruppe von Werten zugeordnet. Zur Gruppierung der einzelnen Werte wird eine Reihe verwendet, die auf Jahren basiert.

Beispielsweise belaufen sich die Verkaufszahlen in Kanada im Jahr 2013 auf 0 und im Jahr 2014 auf 50.

```JSON
{
    "values": [
        {
            "source": {...},
            "values": [
                null,
                300,
                null,
                null
            ],
            "identity": [...],
        },
        {
            "source": {...},
            "values": [
                50,
                null,
                150,
                null
            ],
            "identity": [...],
        },
        {
            "source": {...},
            "values": [
                200,
                null,
                null,
                125
            ],
            "identity": [...],
        },
        {
            "source": {...},
            "values": [
                null,
                null,
                null,
                100
            ],
            "identity": [...],
        }
    ]
}
```

## <a name="table-data-mapping"></a>Tabellendatenzuordnung

Die Tabellendatenansicht ist eine einfache Datenzuordnung. Im Wesentlichen handelt es sich um eine Liste von Datenpunkten, in der numerische Datenpunkte aggregiert werden können.

### <a name="example-7"></a>Beispiel 7

Mit den angegebenen Funktionen

```json
"dataRoles": [
    {
        "displayName": "Values",
        "name": "values",
        "kind": "Measure"
    }
]
```

```json
"dataViewMappings": [
    {
        "table": {
            "rows": {
                "for": {
                    "in": "values"
                }
            }
        }
    }
]
```

Mit diesen können Sie die Tabellendatenansicht wie folgt visualisieren:  

| Land| Jahr | Vertrieb |
|-----|-----|------|
| USA | 2016 | 100 |
| USA | 2015 | 50 |
| Kanada | 2015 | 200 |
| Kanada | 2015 | 50 |
| Mexiko | 2013 | 300 |
| VEREINIGTES KÖNIGREICH | 2014 | 150 |
| USA | 2015 | 75 |

In Power BI werden die Daten als Tabellendatenansicht angezeigt. Sie sollten nicht davon ausgehen, dass die Daten sortiert wurden.

```JSON
{
    "table" : {
        "columns": [...],
        "rows": [
            [
                "Canada",
                2014,
                50
            ],
            [
                "Canada",
                2015,
                200
            ],
            [
                "Mexico",
                2013,
                300
            ],
            [
                "UK",
                2014,
                150
            ],
            [
                "USA",
                2015,
                100
            ],
            [
                "USA",
                2015,
                75
            ],
            [
                "USA",
                2016,
                100
            ]
        ]
    }
}
```

Sie können die Daten aggregieren, indem Sie das gewünschte Feld auswählen und dann „Summe“ auswählen.  

![Datenaggregation](./media/data-aggregation.png)

## <a name="matrix-data-mapping"></a>Matrixdatenzuordnung

Die Matrixdatenzuordnung ist mit der Tabellendatenzuordnung vergleichbar, allerdings werden Zeilen hierarchisch dargestellt. Alle Datenrollenwerte können als Werte für die Spaltenkopfzeile verwendet werden.

```json
{
    "dataRoles": [
        {
            "name": "Category",
            "displayName": "Category",
            "displayNameKey": "Visual_Category",
            "kind": "Grouping"
        },
        {
            "name": "Column",
            "displayName": "Column",
            "displayNameKey": "Visual_Column",
            "kind": "Grouping"
        },
        {
            "name": "Measure",
            "displayName": "Measure",
            "displayNameKey": "Visual_Values",
            "kind": "Measure"
        }
    ],
    "dataViewMappings": [
        {
            "matrix": {
                "rows": {
                    "for": {
                        "in": "Category"
                    }
                },
                "columns": {
                    "for": {
                        "in": "Column"
                    }
                },
                "values": {
                    "select": [
                        {
                            "for": {
                                "in": "Measure"
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

Power BI erzeugt eine hierarchische Datenstruktur. Das Stammelement dieser Struktur enthält die Daten aus der Spalte **Parents** (Eltern) der `Category`-Datenrolle und die Daten aus der Spalte **Children** (Kinder) aus der Datenrollentabelle.

Dataset:

| Übergeordnete Elemente | Untergeordnete Elemente | Grandchildren (Enkelkinder) | Spalten | Werte |
|-----|-----|------|-------|-------|
| Parent1 | Child1 | Grand child1 | Col1 | 5 |
| Parent1 | Child1 | Grand child1 | Col2 | 6 |
| Parent1 | Child1 | Grand child2 | Col1 | 7 |
| Parent1 | Child1 | Grand child2 | Col2 | 8 |
| Parent1 | Child2 | Grand child3 | Col1 | 5 |
| Parent1 | Child2 | Grand child3 | Col2 | 3 |
| Parent1 | Child2 | Grand child4 | Col1 | 4 |
| Parent1 | Child2 | Grand child4 | Col2 | 9 |
| Parent1 | Child2 | Grand child5 | Col1 | 3 |
| Parent1 | Child2 | Grand child5 | Col2 | 5 |
| Parent2 | Child3 | Grand child6 | Col1 | 1 |
| Parent2 | Child3 | Grand child6 | Col2 | 2 |
| Parent2 | Child3 | Grand child7 | Col1 | 7 |
| Parent2 | Child3 | Grand child7 | Col2 | 1 |
| Parent2 | Child3 | Grand child8 | Col1 | 10 |
| Parent2 | Child3 | Grand child8 | Col2 | 13 |

Das Hauptmatrixvisual von Power BI rendert die Daten als Tabelle.

![Matrixvisual](./media/matrix-visual-smaple.png)

Die Datenstruktur für das Visual wird wie im folgenden Code definiert. Nur die ersten beiden Tabellenzeilen werden hier aufgeführt:

```json
{
    "metadata": {...},
    "matrix": {
        "rows": {
            "levels": [...],
            "root": {
                "childIdentityFields": [...],
                "children": [
                    {
                        "level": 0,
                        "levelValues": [...],
                        "value": "Parent1",
                        "identity": {...},
                        "childIdentityFields": [...],
                        "children": [
                            {
                                "level": 1,
                                "levelValues": [...],
                                "value": "Child1",
                                "identity": {...},
                                "childIdentityFields": [...],
                                "children": [
                                    {
                                        "level": 2,
                                        "levelValues": [...],
                                        "value": "Grand child1",
                                        "identity": {...},
                                        "values": {
                                            "0": {
                                                "value": 5 // value for Col1
                                            },
                                            "1": {
                                                "value": 6 // value for Col2
                                            }
                                        }
                                    },
                                    ...
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        },
        "columns": {
            "levels": [...],
            "root": {
                "childIdentityFields": [...],
                "children": [
                    {
                        "level": 0,
                        "levelValues": [...],
                        "value": "Col1",
                        "identity": {...}
                    },
                    {
                        "level": 0,
                        "levelValues": [...],
                        "value": "Col2",
                        "identity": {...}
                    },
                    ...
                ]
            }
        },
        "valueSources": [...]
    }
}
```

## <a name="data-reduction-algorithm"></a>Datenverringerungsalgorithmus

Sie können einen Datenverringerungsalgorithmus verwenden, um festzulegen, wie viele Daten für die Datenansicht verwendet werden.

Auf alle Power BI-Visuals wird standardmäßig der Datenverringerungsalgorithmus „top“ angewendet, wobei *count* auf 1000 Datenpunkte festgelegt ist. Das Festlegen der folgenden Eigenschaften in *capabilities.json* hat dieselben Auswirkungen:

```json
"dataReductionAlgorithm": {
    "top": {
        "count": 1000
    }
}
```

Sie können für *count* einen anderen Integerwert bis 30.000 festlegen. R-basierte Power BI-Visuals können bis zu 150000 Zeilen unterstützen.

## <a name="data-reduction-algorithm-types"></a>Typen von Datenverringerungsalgorithmen

Für den Datenverringerungsalgorithmus sind vier Einstellungstypen verfügbar:

* `top`: Hiermit beschränken Sie die Daten auf Werte, die vom Anfang des Datasets abgerufen wurden. Die ersten Werte, deren Anzahl durch *count* vorgegeben wird, werden aus dem Dataset abgerufen.
* `bottom`: Hiermit beschränken Sie die Daten auf Werte, die vom Ende des Datasets abgerufen werden. Die letzten Werte, deren Anzahl durch „count“ vorgegeben wird, werden aus dem Dataset abgerufen.
* `sample`: Hiermit beschränken Sie das Dataset durch einen einfachen Algorithmus für die Stichprobenentnahme auf eine durch *count* vorgegebene Anzahl von Elementen. Dies bedeutet, dass das erste und das letzte Element einbezogen werden und dass zwischen einer durch *count* vorgegebenen Anzahl von Elementen dieselben Intervalle bestehen.
Wenn z. B. das Dataset [0, 1, 2, ... 100] vorliegt und *count* 9 beträgt, ergeben sich daraus die Werte [0, 10, 20 ... 100].
* `window`: Hiermit wird jeweils ein Segment (*window*) mit Datenpunkten mit der durch *count* angegebenen Anzahl von Elementen geladen. `top` und `window` sind derzeit gleichwertig. An der vollständigen Unterstützung der Segmentierungseinstellung wird aktuell gearbeitet.

## <a name="data-reduction-algorithm-usage"></a>Verwenden des Datenverringerungsalgorithmus

Der Datenverringerungsalgorithmus kann für Datenansichtszuordnungen der Typen „categorical“, „table“ oder „matrix“ genutzt werden.

Sie können den Algorithmus in `categories` und/oder im Gruppenabschnitt von `values` für kategorische Datenzuordnungen verwenden.

### <a name="example-8"></a>Beispiel 8

```json
"dataViewMappings": {
    "categorical": {
        "categories": {
            "for": { "in": "category" },
            "dataReductionAlgorithm": {
                "window": {
                    "count": 300
                }
            }  
        },
        "values": {
            "group": {
                "by": "series",
                "select": [{
                        "for": {
                            "in": "measure"
                        }
                    }
                ],
                "dataReductionAlgorithm": {
                    "top": {
                        "count": 100
                    }
                }  
            }
        }
    }
}
```

Sie können den Datenverringerungsalgorithmus auf den `rows`-Abschnitt der Zuordnungstabelle für Datenansichten anwenden.

### <a name="example-9"></a>Beispiel 9

```json
"dataViewMappings": [
    {
        "table": {
            "rows": {
                "for": {
                    "in": "values"
                },
                "dataReductionAlgorithm": {
                    "top": {
                        "count": 2000
                    }
                } 
            }
        }
    }
]
```

Sie können den Datenverringerungsalgorithmus auf die Abschnitte `rows` und `columns` der Zuordnungsmatrix für Datenansichten anwenden.
