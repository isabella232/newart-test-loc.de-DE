---
title: Einführung in Komponententests für Power BI-Visualprojekte
description: In diesem Artikel wird beschrieben, wie Sie Komponententests für Power BI-Visualprojekte erstellen.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: bb9835ceba302716c2c4b1e28eda33c6e4b1db42
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424130"
---
# <a name="tutorial-add-unit-tests-for-power-bi-visual-projects"></a>Tutorial: Hinzufügen von Komponententests zu Power BI-Visualprojekten

In diesem Artikel werden die Grundlagen zur Erstellung von Komponententests für Power BI-Visualprojekte beschrieben. Dabei wird u. a. Folgendes thematisiert:

* Einrichten des Testframeworks Jasmine und des Test Runners Karma für JavaScript-Code
* Verwenden des Pakets „powerbi-visuals-utils-testutils“
* Verwenden von Pseudo- und Fakeobjekten, um Komponententests für Power BI-Visuals zu vereinfachen

## <a name="prerequisites"></a>Voraussetzungen

* Ein installiertes Power BI-Visualprojekt
* Eine konfigurierte Node.js-Umgebung

## <a name="install-and-configure-the-karma-javascript-test-runner-and-jasmine"></a>Installieren und Konfigurieren des Jasmine-Testframeworks und des Test Runners Karma für JavaScript-Code

Fügen Sie der Datei *package.json* im Abschnitt `devDependencies` die erforderlichen Bibliotheken hinzu:

```json
"@babel/polyfill": "^7.2.5",
"@types/d3": "5.5.0",
"@types/jasmine": "2.5.37",
"@types/jasmine-jquery": "1.5.28",
"@types/jquery": "2.0.41",
"@types/karma": "3.0.0",
"@types/lodash-es": "4.17.1",
"coveralls": "3.0.2",
"istanbul-instrumenter-loader": "^3.0.1",
"jasmine": "2.5.2",
"jasmine-core": "2.5.2",
"jasmine-jquery": "2.1.1",
"jquery": "3.1.1",
"karma": "3.1.1",
"karma-chrome-launcher": "2.2.0",
"karma-coverage": "1.1.2",
"karma-coverage-istanbul-reporter": "^2.0.4",
"karma-jasmine": "2.0.1",
"karma-junit-reporter": "^1.2.0",
"karma-sourcemap-loader": "^0.3.7",
"karma-typescript": "^3.0.13",
"karma-typescript-preprocessor": "0.4.0",
"karma-webpack": "3.0.5",
"puppeteer": "1.17.0",
"style-loader": "0.23.1",
"ts-loader": "5.3.0",
"ts-node": "7.0.1",
"tslint": "^5.12.0",
"webpack": "4.26.0"
```

Weitere Informationen zum Paket finden Sie unter:

Speichern Sie die Datei *package.json*. Führen Sie anschließend am Speicherort von `package.json` den folgenden Befehl aus:

```cmd
npm install
```

Der Paket-Manager installiert alle neuen Pakete, die *package.json* hinzugefügt wurden.

Konfigurieren Sie den Test Runner und die `webpack`-Konfiguration, um Komponententests auszuführen.

Der folgende Code ist ein Beispiel aus der Datei *test.webpack.config.js*:

```typescript
const path = require('path');
const webpack = require("webpack");

module.exports = {
    devtool: 'source-map',
    mode: 'development',
    optimization : {
        concatenateModules: false,
        minimize: false
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: 'ts-loader',
                exclude: /node_modules/
            },
            {
                test: /\.json$/,
                loader: 'json-loader'
            },
            {
                test: /\.tsx?$/i,
                enforce: 'post',
                include: /(src)/,
                exclude: /(node_modules|resources\/js\/vendor)/,
                loader: 'istanbul-instrumenter-loader',
                options: { esModules: true }
            },
            {
                test: /\.less$/,
                use: [
                    {
                        loader: 'style-loader'
                    },
                    {
                        loader: 'css-loader'
                    },
                    {
                        loader: 'less-loader',
                        options: {
                            paths: [path.resolve(__dirname, 'node_modules')]
                        }
                    }
                ]
            }
        ]
    },
    externals: {
        "powerbi-visuals-api": '{}'
    },
    resolve: {
        extensions: ['.tsx', '.ts', '.js', '.css']
    },
    output: {
        path: path.resolve(__dirname, ".tmp/test")
    },
    plugins: [
        new webpack.ProvidePlugin({
            'powerbi-visuals-api': null
        })
    ]
};
```

Der folgende Code ist ein Beispiel aus der Datei *karma.conf.ts*:

```typescript
"use strict";

const webpackConfig = require("./test.webpack.config.js");
const tsconfig = require("./test.tsconfig.json");
const path = require("path");

const testRecursivePath = "test/visualTest.ts";
const srcOriginalRecursivePath = "src/**/*.ts";
const coverageFolder = "coverage";

process.env.CHROME_BIN = require("puppeteer").executablePath();

import { Config, ConfigOptions } from "karma";

module.exports = (config: Config) => {
    config.set(<ConfigOptions>{
        mode: "development",
        browserNoActivityTimeout: 100000,
        browsers: ["ChromeHeadless"], // or Chrome to use locally installed Chrome browser
        colors: true,
        frameworks: ["jasmine"],
        reporters: [
            "progress",
            "junit",
            "coverage-istanbul"
        ],
        junitReporter: {
            outputDir: path.join(__dirname, coverageFolder),
            outputFile: "TESTS-report.xml",
            useBrowserName: false
        },
        singleRun: true,
        plugins: [
            "karma-coverage",
            "karma-typescript",
            "karma-webpack",
            "karma-jasmine",
            "karma-sourcemap-loader",
            "karma-chrome-launcher",
            "karma-junit-reporter",
            "karma-coverage-istanbul-reporter"
        ],
        files: [
            "node_modules/jquery/dist/jquery.min.js",
            "node_modules/jasmine-jquery/lib/jasmine-jquery.js",
            {
                pattern: './capabilities.json',
                watched: false,
                served: true,
                included: false
            },
            testRecursivePath,
            {
                pattern: srcOriginalRecursivePath,
                included: false,
                served: true
            }
        ],
        preprocessors: {
            [testRecursivePath]: ["webpack", "coverage"]
        },
        typescriptPreprocessor: {
            options: tsconfig.compilerOptions
        },
        coverageIstanbulReporter: {
            reports: ["html", "lcovonly", "text-summary", "cobertura"],
            dir: path.join(__dirname, coverageFolder),
            'report-config': {
                html: {
                    subdir: 'html-report'
                }
            },
            combineBrowserReports: true,
            fixWebpackSourcePaths: true,
            verbose: false
        },
        coverageReporter: {
            dir: path.join(__dirname, coverageFolder),
            reporters: [
                // reporters not supporting the `file` property
                { type: 'html', subdir: 'html-report' },
                { type: 'lcov', subdir: 'lcov' },
                // reporters supporting the `file` property, use `subdir` to directly
                // output them in the `dir` directory
                { type: 'cobertura', subdir: '.', file: 'cobertura-coverage.xml' },
                { type: 'lcovonly', subdir: '.', file: 'report-lcovonly.txt' },
                { type: 'text-summary', subdir: '.', file: 'text-summary.txt' },
            ]
        },
        mime: {
            "text/x-typescript": ["ts", "tsx"]
        },
        webpack: webpackConfig,
        webpackMiddleware: {
            stats: "errors-only"
        }
    });
};
```

Sie können diese Konfiguration bei Bedarf ändern.

Der Code in *karma.conf.js* enthält die folgende Variablen:

* `recursivePathToTests`: der Speicherort des Testcodes.

* `srcRecursivePath`: der Speicherort des JavaScript-Ausgabecodes nach dem Kompilieren.

* `srcCssRecursivePath`: der Speicherort der CSS-Ausgabe nach dem Kompilieren der LESS-Datei mit Formatangaben.

* `srcOriginalRecursivePath`: der Speicherort des Visualquellcodes.

* `coverageFolder`: der Speicherort, an dem der Bericht zur Code Coverage erstellt werden soll.

Die Konfigurationsdatei enthält die folgenden Eigenschaften:

* `singleRun: true`: Tests lassen sich entweder einmalig oder auf einem CI-System (Continuous Integration) ausführen. Sie können für das Debuggen von Tests die Einstellung *false* festlegen. Der Browser wird von Karma weiterhin ausgeführt, damit Sie die Konsole zum Debuggen verwenden können.

* `files: [...]`: In diesem Array können Sie die Dateien festlegen, die in den Browser geladen werden sollen. Normalerweise handelt es sich um Quelldateien, Testfälle und Bibliotheken (für Jasmine und Testhilfsprogramme). Sie können die Liste bei Bedarf um weitere Dateien ergänzen.

* `preprocessors`: In diesem Abschnitt konfigurieren Sie die Aktionen, die vor den Komponententests ausgeführt werden. Die Aktionen kompilieren TypeScript vorab zu JavaScript, bereiten Quellzuordnungsdateien vor und generieren den Bericht zur Code Coverage. Sie können `coverage` deaktivieren, wenn Sie Tests debuggen. Durch „coverage“ wird zusätzlicher Testcode generiert, der bei der Code Coverage für Tests berücksichtigt wird. Dies erschwert das Debuggen von Tests.

Eine Beschreibung aller Karma-Konfigurationen finden Sie auf der Seite [Karma-Konfigurationsdatei](https://karma-runner.github.io/1.0/config/configuration-file.html).

Sie können `scripts` praktischerweise einen Testbefehl hinzufügen:

```json
{
    "scripts": {
        "pbiviz": "pbiviz",
        "start": "pbiviz start",
        "typings":"node node_modules/typings/dist/bin.js i",
        "lint": "tslint -r \"node_modules/tslint-microsoft-contrib\"  \"+(src|test)/**/*.ts\"",
        "pretest": "pbiviz package --resources --no-minify --no-pbiviz --no-plugin",
        "test": "karma start"
    }
    ...
}
```

Nun können Sie mit dem Erstellen von Komponententests beginnen.

## <a name="check-the-dom-element-of-the-visual"></a>Überprüfen des DOM-Elements des Visuals

Erstellen Sie zunächst eine Instanz des Visuals, um dieses zu testen.

### <a name="create-a-visual-instance-builder"></a>Erstellen des Visualinstanz-Generators

Fügen Sie dem Ordner *test* die Datei *visualBuilder.ts* hinzu, indem Sie folgenden Code verwenden:

```typescript
import {
    VisualBuilderBase
} from "powerbi-visuals-utils-testutils";

import {
    BarChart as VisualClass
} from "../src/visual";

import  powerbi from "powerbi-visuals-api";
import VisualConstructorOptions = powerbi.extensibility.visual.VisualConstructorOptions;

export class BarChartBuilder extends VisualBuilderBase<VisualClass> {
    constructor(width: number, height: number) {
        super(width, height);
    }

    protected build(options: VisualConstructorOptions) {
        return new VisualClass(options);
    }

    public get mainElement() {
        return this.element.children("svg.barChart");
    }
}
```

Es gibt eine `build`-Methode, mit der Sie eine Instanz des Visuals erstellen können. `mainElement` ist eine get-Methode, die eine Instanz des DOM-Stammelements im Visual zurückgibt. Der Getter ist optional, erleichtert jedoch das Erstellen von Komponententests.

Sie verfügen nun über eine Instanz des Visuals. Als Nächstes schreiben Sie den Testfall. Mit dem Testfall werden die SVG-Elemente überprüft, die beim Anzeigen des Visuals erstellt werden.

### <a name="create-a-typescript-file-to-write-test-cases"></a>Erstellen einer TypeScript-Datei zum Schreiben von Testfällen

Fügen Sie den Testfällen die Datei *visualTest.ts* hinzu, indem Sie folgenden Code verwenden:

```typescript
import powerbi from "powerbi-visuals-api";

import { BarChartBuilder } from "./VisualBuilder";

import {
    BarChart as VisualClass
} from "../src/visual";

import VisualBuilder = powerbi.extensibility.visual.test.BarChartBuilder;

describe("BarChart", () => {
    let visualBuilder: VisualBuilder;
    let dataView: DataView;

    beforeEach(() => {
        visualBuilder = new VisualBuilder(500, 500);
    });

    it("root DOM element is created", () => {
        expect(visualBuilder.mainElement).toBeInDOM();
    });
});
```

Mehrere Methoden werden aufgerufen:

* [`describe`](https://jasmine.github.io/api/2.6/global.html#describe): beschreibt einen Testfall. Im Kontext des Jasmine-Frameworks wird damit häufig eine Sammlung oder eine Gruppe von Spezifikationen beschrieben.

* `beforeEach`: wird vor jedem Aufruf der `it`-Methode aufgerufen, die in der Methode [`describe`](https://jasmine.github.io/api/2.6/global.html#beforeEach) definiert ist.

* [`it`](https://jasmine.github.io/api/2.6/global.html#it): definiert eine einzelne Spezifikation. Die `it`-Methode sollte eine oder mehrere `expectations` enthalten.

* [`expect`](https://jasmine.github.io/api/2.6/global.html#expect): erstellt eine Erwartung für eine Spezifikation. Eine Spezifikation ist erfolgreich, wenn alle Erwartungen ohne Fehler erfüllt werden.

* `toBeInDOM`: eine der *matchers*-Methoden. Weitere Informationen zu Matchern finden Sie unter [Jasmine-Namespace: matchers](https://jasmine.github.io/api/2.6/matchers.html).

Weitere Informationen zu Jasmine finden Sie auf der Seite [Dokumentation zum Jasmine-Framework](https://jasmine.github.io/).

### <a name="launch-unit-tests"></a>Starten von Komponententests

Mit diesem Test wird überprüft, ob das SVG-Stammelement der Visuals erstellt wird. Geben Sie den folgenden Befehl im Befehlszeilentool ein, um den Komponententest auszuführen:

```cmd
npm run test
```

`karma.js` führt den Testfall im Chrome-Browser aus.

![In Chrome geöffnetes Karma-JavaScript](./media/karmajs-chrome.png)

> [!NOTE]
> Sie müssen Google Chrome lokal installieren.

Im Befehlszeilenfenster wird die folgende Ausgabe angezeigt:

```cmd
> karma start

23 05 2017 12:24:26.842:WARN [watcher]: Pattern "E:/WORKSPACE/PowerBI/PowerBI-visuals-sampleBarChart/data/*.csv" does not match any file.
23 05 2017 12:24:30.836:WARN [karma]: No captured browser, open http://localhost:9876/
23 05 2017 12:24:30.849:INFO [karma]: Karma v1.3.0 server started at http://localhost:9876/
23 05 2017 12:24:30.850:INFO [launcher]: Launching browser Chrome with unlimited concurrency
23 05 2017 12:24:31.059:INFO [launcher]: Starting browser Chrome
23 05 2017 12:24:33.160:INFO [Chrome 58.0.3029 (Windows 10 0.0.0)]: Connected on socket /#2meR6hjXFmsE_fjiAAAA with id 5875251
Chrome 58.0.3029 (Windows 10 0.0.0): Executed 1 of 1 SUCCESS (0.194 secs / 0.011 secs)

=============================== Coverage summary ===============================
Statements   : 27.43% ( 65/237 )
Branches     : 19.84% ( 25/126 )
Functions    : 43.86% ( 25/57 )
Lines        : 20.85% ( 44/211 )
================================================================================
```

### <a name="how-to-add-static-data-for-unit-tests"></a>Hinzufügen statischer Daten zu Komponententests

Erstellen Sie die Datei *visualData.ts* im Ordner *test*, indem Sie folgenden Code verwenden:

```typescript
import powerbi from "powerbi-visuals-api";
import DataView = powerbi.DataView;

import {
    testDataViewBuilder,
    getRandomNumbers
} from "powerbi-visuals-utils-testutils";

export class SampleBarChartDataBuilder extends TestDataViewBuilder {
    public static CategoryColumn: string = "category";
    public static MeasureColumn: string = "measure";

    public constructor() {
        super();
        ...
    }

    public getDataView(columnNames?: string[]): DataView {
        let dateView: any = this.createCategoricalDataViewBuilder([
            ...
        ],
        [
            ...
        ], columnNames).build();

        // there's client side computed maxValue
        let maxLocal = 0;
        this.valuesMeasure.forEach((item) => {
                if (item > maxLocal) {
                    maxLocal = item;
                }
        });
        (<any>dataView).categorical.values[0].maxLocal = maxLocal;
    }
}
```

Die `SampleBarChartDataBuilder`-Klasse erbt von `TestDataViewBuilder` und implementiert die abstrakte Methode `getDataView`.

Wenn Sie Daten in Datenfeldbuckets eingeben, generiert Power BI ein `dataview`-Kategorieobjekt, das auf den Daten basiert.

![Datenfeldbuckets](./media/fields-buckets.png)

In Komponententests stehen keine Power BI-Kernfunktionen zur Verfügung, mit denen sich die Daten kopieren lassen. Sie müssen die statischen Daten jedoch dem `dataview`-Kategorieobjekt zuordnen. Dabei unterstützt Sie die `TestDataViewBuilder`-Klasse.

Weitere Informationen zur Zuordnung von Datenansichten finden Sie unter [DataViewMappings](https://github.com/Microsoft/PowerBI-visuals/blob/master/Capabilities/DataViewMappings.md).

In der `getDataView`-Methode rufen Sie die `createCategoricalDataViewBuilder`-Methode mit Ihren Daten auf.

Die Datei [capabilities.json](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/blob/master/capabilities.json#L2) des `sampleBarChart`-Visuals enthält das dataRoles-Objekt und das dataViewMapping-Objekt:

```json
"dataRoles": [
    {
        "displayName": "Category Data",
        "name": "category",
        "kind": "Grouping"
    },
    {
        "displayName": "Measure Data",
        "name": "measure",
        "kind": "Measure"
    }
],
"dataViewMappings": [
    {
        "conditions": [
            {
                "category": {
                    "max": 1
                },
                "measure": {
                    "max": 1
                }
            }
        ],
        "categorical": {
            "categories": {
                "for": {
                    "in": "category"
                }
            },
            "values": {
                "select": [
                    {
                        "bind": {
                            "to": "measure"
                        }
                    }
                ]
            }
        }
    }
],
```

Um die gleiche Zuordnung zu generieren, müssen Sie die folgenden Parameter auf die `createCategoricalDataViewBuilder`-Methode festlegen:

```typescript
([
    {
        source: {
            displayName: "Category",
            queryName: SampleBarChartData.ColumnCategory,
            type: ValueType.fromDescriptor({ text: true }),
            roles: {
                Category: true
            },
        },
        values: this.valuesCategory
    }
],
[
    {
        source: {
            displayName: "Measure",
            isMeasure: true,
            queryName: SampleBarChartData.MeasureColumn,
            type: ValueType.fromDescriptor({ numeric: true }),
            roles: {
                Measure: true
            },
        },
        values: this.valuesMeasure
    },
], columnNames)
```

Dabei ist `this.valuesCategory` ein Array von Kategorien:

```ts
public valuesCategory: string[] = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"];
```

`this.valuesMeasure` ist ein Array von Measures für jede Kategorie:

```ts
public valuesMeasure: number[] = [742731.43, 162066.43, 283085.78, 300263.49, 376074.57, 814724.34, 570921.34];
```

Jetzt können Sie die `SampleBarChartDataBuilder`-Klasse im Komponententest verwenden.

Die `ValueType`-Klasse ist im Paket „powerbi-visuals-utils-testutils“ definiert. Für die `createCategoricalDataViewBuilder`-Methode ist die `lodash`-Bibliothek erforderlich.

Fügen Sie die folgenden Pakete den Abhängigkeiten hinzu.

In `package.json` im Abschnitt `devDependencies`

```json
"lodash-es": "4.17.1",
"powerbi-visuals-utils-testutils": "2.2.0"
```

Aufruf

```cmd
npm install
```

zur Installation der `lodash-es`-Bibliothek

Nun können Sie den Komponententest erneut ausführen. Folgende Ausgabe muss angezeigt werden:

```cmd
> karma start

23 05 2017 16:19:54.318:WARN [watcher]: Pattern "E:/WORKSPACE/PowerBI/PowerBI-visuals-sampleBarChart/data/*.csv" does not match any file.
23 05 2017 16:19:58.333:WARN [karma]: No captured browser, open http://localhost:9876/
23 05 2017 16:19:58.346:INFO [karma]: Karma v1.3.0 server started at http://localhost:9876/
23 05 2017 16:19:58.346:INFO [launcher]: Launching browser Chrome with unlimited concurrency
23 05 2017 16:19:58.394:INFO [launcher]: Starting browser Chrome
23 05 2017 16:19:59.873:INFO [Chrome 58.0.3029 (Windows 10 0.0.0)]: Connected on socket /#NcNTAGH9hWfGMCuEAAAA with id 3551106
Chrome 58.0.3029 (Windows 10 0.0.0): Executed 1 of 1 SUCCESS (1.266 secs / 1.052 secs)

=============================== Coverage summary ===============================
Statements   : 56.72% ( 135/238 )
Branches     : 32.54% ( 41/126 )
Functions    : 66.67% ( 38/57 )
Lines        : 52.83% ( 112/212 )
================================================================================
```

Das Visual wird im Chrome-Browser geöffnet und wie folgt dargestellt:

![Komponententest wird in Chrome gestartet.](./media/karmajs-chrome-ut-runned.png)

Aus der Zusammenfassung geht hervor, dass sich die Code Coverage erhöht hat. Weitere Informationen zur aktuellen Code Coverage finden Sie in der Datei `coverage\index.html`.

![Code Coverage-Index des Komponententests](./media/code-coverage-index.png)

Sie können sich auch die Code Coverage für den Ordner `src` ansehen:

![Code Coverage für Ordner „src“](./media/code-coverage-src-folder.png)

Sie können sich für jede Datei den Quellcode anzeigen lassen. Die `Coverage`-Hilfsprogramme heben Zeilen rot hervor, wenn bestimmter Code während der Komponententests nicht ausgeführt wird.

![Code Coverage der Datei „visual.ts“](./media/code-coverage-visual-src.png)

> [!IMPORTANT]
> Die Code Coverage sagt nichts darüber aus, ob ein Großteil der Visualfunktionen getestet wurde. Ein einfacher Komponententest führt zu einer Code Coverage von über 96 % in `src\visual.ts`.

## <a name="next-steps"></a>Nächste Schritte

Sobald Ihr Visual bereit ist, können Sie es zur Veröffentlichung übermitteln. Weitere Informationen finden Sie unter [Veröffentlichen von Power BI-Visuals in AppSource](../office-store.md).
