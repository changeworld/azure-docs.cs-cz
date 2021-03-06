---
title: 'Úvodní příručka: Vytvoření projektu detekce objektů pomocí sady Custom Vision SDK pro soubor Node.js'
titleSuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, trénujte projekt a detekujte objekty pomocí sady SDK Node.js.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: 94013b735f70358d0c49512e6d90cd1d03e78d5f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705713"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-nodejs-sdk"></a>Úvodní příručka: Vytvoření projektu detekce objektů pomocí sady SDK Custom Vision Node.js

Tento článek ukazuje, jak začít používat vlastní vize SDK s Node.js k vytvoření modelu detekce objektů. Po vytvoření můžete přidat označené oblasti, nahrát obrázky, trénovat projekt, získat adresu URL koncového bodu publikované předpovědi projektu a použít koncový bod k programovému testování bitové kopie. Tento příklad použijte jako šablonu pro vytváření vlastní aplikace Node.js.

## <a name="prerequisites"></a>Požadavky

- [Node.js 8](https://www.nodejs.org/en/download/) nebo novější nainstalován.
- [npm](https://www.npmjs.com/) nainstalován.
- [!INCLUDE [create-resources](includes/create-resources.md)]

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [node-get-images](includes/node-get-images.md)]


## <a name="install-the-custom-vision-sdk"></a>Instalace sady Custom Vision SDK

Chcete-li do projektu nainstalovat sady SDK služby Vlastní vize pro soubor Node.js, spusťte následující příkazy:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

## <a name="add-the-code"></a>Přidání kódu

Vytvořte nový soubor s názvem *sample.js* v preferovaném adresáři projektu.

### <a name="create-the-custom-vision-service-project"></a>Vytvoření projektu služby Custom Vision

Přidáním následujícího kódu do svého skriptu vytvořte nový projekt služby Custom Vision. Vložte klíče předplatného do příslušných definic a nastavte hodnotu cesty sampleDataRoot na cestu složky obrázku. Ujistěte se, že hodnota koncového bodu odpovídá koncovým bodům školení a předpovědi, které jste vytvořili v [Customvision.ai](https://www.customvision.ai/). Všimněte si, že rozdíl mezi vytvořením objektu detekce a image klasifikace projektu je doména zadaná ve volání **createProject.**

```javascript
const fs = require('fs');
const util = require('util');
const TrainingApi = require("@azure/cognitiveservices-customvision-training");
const PredictionApi = require("@azure/cognitiveservices-customvision-prediction");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://<my-resource-name>.cognitiveservices.azure.com/"

const publishIterationName = "detectModel";

const trainer = new TrainingApi.TrainingAPIClient(trainingKey, endPoint);

/* Helper function to let us use await inside a forEach loop.
 * This lets us insert delays between image uploads to accommodate the rate limit.
 */
async function asyncForEach (array, callback) {
    for (let i = 0; i < array.length; i++) {
        await callback(array[i], i, array);
    }
}

(async () => {
    console.log("Creating project...");
    const domains = await trainer.getDomains()
    const objDetectDomain = domains.find(domain => domain.type === "ObjectDetection");
    const sampleProject = await trainer.createProject("Sample Obj Detection Project", { domainId: objDetectDomain.id });
```

### <a name="create-tags-in-the-project"></a>Vytvoření značek v projektu

Chcete-li do projektu vytvořit značky klasifikace, přidejte na konec *souboru sample.js*následující kód :

```javascript
    const forkTag = await trainer.createTag(sampleProject.id, "Fork");
    const scissorsTag = await trainer.createTag(sampleProject.id, "Scissors");
```

### <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Když označíte obrázky v projektech detekce objektů, je nutné zadat oblast každého tagovaného objektu pomocí normalizovaných souřadnic. 

> [!NOTE]
> Pokud nemáte nástroj pro klepnutí a přetažení, který by označoval souřadnice oblastí, můžete použít webové uživatelské uživatelské tlačítko na [Customvision.ai](https://www.customvision.ai/). V tomto příkladu jsou již k dispozici souřadnice.

Obrázky, značky a oblasti do projektu přidáte tak, že po vytvoření značky vložíte následující kód. Všimněte si, že pro účely tohoto kurzu jsou oblasti pevně vloženy v kódu. Oblasti určují ohraničující rámeček s normalizovanými souřadnicemi, které jsou v tomto pořadí: vlevo, nahoře, šířka, výška. V jedné dávce můžete nahrát až 64 obrázků.

```javascript
const forkImageRegions = {
    "fork_1.jpg": [0.145833328, 0.3509314, 0.5894608, 0.238562092],
    "fork_2.jpg": [0.294117659, 0.216944471, 0.534313738, 0.5980392],
    "fork_3.jpg": [0.09191177, 0.0682516545, 0.757352948, 0.6143791],
    "fork_4.jpg": [0.254901975, 0.185898721, 0.5232843, 0.594771266],
    "fork_5.jpg": [0.2365196, 0.128709182, 0.5845588, 0.71405226],
    "fork_6.jpg": [0.115196079, 0.133611143, 0.676470637, 0.6993464],
    "fork_7.jpg": [0.164215669, 0.31008172, 0.767156839, 0.410130739],
    "fork_8.jpg": [0.118872553, 0.318251669, 0.817401946, 0.225490168],
    "fork_9.jpg": [0.18259804, 0.2136765, 0.6335784, 0.643790841],
    "fork_10.jpg": [0.05269608, 0.282303959, 0.8088235, 0.452614367],
    "fork_11.jpg": [0.05759804, 0.0894935, 0.9007353, 0.3251634],
    "fork_12.jpg": [0.3345588, 0.07315363, 0.375, 0.9150327],
    "fork_13.jpg": [0.269607842, 0.194068655, 0.4093137, 0.6732026],
    "fork_14.jpg": [0.143382356, 0.218578458, 0.7977941, 0.295751631],
    "fork_15.jpg": [0.19240196, 0.0633497, 0.5710784, 0.8398692],
    "fork_16.jpg": [0.140931368, 0.480016381, 0.6838235, 0.240196079],
    "fork_17.jpg": [0.305147052, 0.2512582, 0.4791667, 0.5408496],
    "fork_18.jpg": [0.234068632, 0.445702642, 0.6127451, 0.344771236],
    "fork_19.jpg": [0.219362751, 0.141781077, 0.5919118, 0.6683006],
    "fork_20.jpg": [0.180147052, 0.239820287, 0.6887255, 0.235294119]
};

const scissorsImageRegions = {
    "scissors_1.jpg": [0.4007353, 0.194068655, 0.259803921, 0.6617647],
    "scissors_2.jpg": [0.426470578, 0.185898721, 0.172794119, 0.5539216],
    "scissors_3.jpg": [0.289215684, 0.259428144, 0.403186262, 0.421568632],
    "scissors_4.jpg": [0.343137264, 0.105833367, 0.332107842, 0.8055556],
    "scissors_5.jpg": [0.3125, 0.09766343, 0.435049027, 0.71405226],
    "scissors_6.jpg": [0.379901975, 0.24308826, 0.32107842, 0.5718954],
    "scissors_7.jpg": [0.341911763, 0.20714055, 0.3137255, 0.6356209],
    "scissors_8.jpg": [0.231617644, 0.08459154, 0.504901946, 0.8480392],
    "scissors_9.jpg": [0.170343131, 0.332957536, 0.767156839, 0.403594762],
    "scissors_10.jpg": [0.204656869, 0.120539248, 0.5245098, 0.743464053],
    "scissors_11.jpg": [0.05514706, 0.159754932, 0.799019635, 0.730392158],
    "scissors_12.jpg": [0.265931368, 0.169558853, 0.5061275, 0.606209159],
    "scissors_13.jpg": [0.241421565, 0.184264734, 0.448529422, 0.6830065],
    "scissors_14.jpg": [0.05759804, 0.05027781, 0.75, 0.882352948],
    "scissors_15.jpg": [0.191176474, 0.169558853, 0.6936275, 0.6748366],
    "scissors_16.jpg": [0.1004902, 0.279036, 0.6911765, 0.477124184],
    "scissors_17.jpg": [0.2720588, 0.131977156, 0.4987745, 0.6911765],
    "scissors_18.jpg": [0.180147052, 0.112369314, 0.6262255, 0.6666667],
    "scissors_19.jpg": [0.333333343, 0.0274019931, 0.443627447, 0.852941155],
    "scissors_20.jpg": [0.158088237, 0.04047389, 0.6691176, 0.843137264]
};

console.log("Adding images...");
let fileUploadPromises = [];

const forkDir = `${sampleDataRoot}/Fork`;
const forkFiles = fs.readdirSync(forkDir);

await asyncForEach(forkFiles, async (file) => {
    const region = { tagId : forkTag.id, left : forkImageRegions[file][0], top : forkImageRegions[file][1], width : forkImageRegions[file][2], height : forkImageRegions[file][3] };
    const entry = { name : file, contents : fs.readFileSync(`${forkDir}/${file}`), regions : [region] };
    const batch = { images : [entry] };
    // Wait one second to accommodate rate limit.
    await setTimeoutPromise(1000, null);
    fileUploadPromises.push(trainer.createImagesFromFiles(sampleProject.id, batch));
});

const scissorsDir = `${sampleDataRoot}/Scissors`;
const scissorsFiles = fs.readdirSync(scissorsDir);

await asyncForEach(scissorsFiles, async (file) => {
    const region = { tagId : scissorsTag.id, left : scissorsImageRegions[file][0], top : scissorsImageRegions[file][1], width : scissorsImageRegions[file][2], height : scissorsImageRegions[file][3] };
    const entry = { name : file, contents : fs.readFileSync(`${scissorsDir}/${file}`), regions : [region] };
    const batch = { images : [entry] };
    // Wait one second to accommodate rate limit.
    await setTimeoutPromise(1000, null);
    fileUploadPromises.push(trainer.createImagesFromFiles(sampleProject.id, batch));
});

await Promise.all(fileUploadPromises);
```

### <a name="train-the-project-and-publish"></a>Trénování projektu a publikování

Tento kód vytvoří první iteraci modelu předpověď a potom publikuje tuto iteraci do koncového bodu předpověď. Název zadaný pro publikovanou iteraci lze použít k odeslání požadavků předpovědi. Iterace není k dispozici v koncovém bodu předpověď, dokud je publikován.

```javascript
console.log("Training...");
let trainingIteration = await trainer.trainProject(sampleProject.id);

// Wait for training to complete
console.log("Training started...");
while (trainingIteration.status == "Training") {
    console.log("Training status: " + trainingIteration.status);
    // wait for one second
    await setTimeoutPromise(1000, null);
    trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
}
console.log("Training status: " + trainingIteration.status);

// Publish the iteration to the end point
await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Získat a použít publikovanou iteraci v koncovém bodě předpověď

Pokud chcete odeslat obrázek do koncového bodu předpovědi a načíst předpověď, přidejte na konec souboru následující kód:

```javascript
    const predictor = new PredictionApi.PredictionAPIClient(predictionKey, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_od_image.jpg`);

    const results = await predictor.detectImage(sampleProject.id, publishIterationName, testFile)

    // Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}% ${predictedResult.boundingBox.left},${predictedResult.boundingBox.top},${predictedResult.boundingBox.width},${predictedResult.boundingBox.height}`);
    });
})()
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte *soubor sample.js*.

```shell
node sample.js
```

V konzole by se měl zobrazit výstup aplikace. Pak můžete ověřit správné označení testovacího obrázku (ve složce **samples/vision/images/Test**) a správnost oblasti detekce.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Další kroky

Právě jste viděli, jak se dají jednotlivé kroky procesu detekce objektů provádět v kódu. Tato ukázka provede jednu iteraci trénování, ale často je potřeba model trénovat a testovat vícekrát, aby byl přesnější. Následující příručka se zabývá klasifikací obrázků, ale její principy jsou podobné jako u detekce objektů.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)
