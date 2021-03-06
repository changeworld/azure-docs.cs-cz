---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: a79d42255a9d04f7a7c8c1be151beda1804df89b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656500"
---
:::row:::
    :::column span="3":::
        Sada JavaScript Speech SDK je k dispozici jako balíček npm, viz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a je to doprovodné úložiště GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Přestože javascriptová sada Speech SDK je k dispozici jako balíček npm, tak jej mohou využívat soubor Node.js i klientské webové prohlížeče – zvažte různé architektonické důsledky každého prostředí. Například <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">objektový model dokumentu <span class="docon docon-navigate-external x-hidden-focus"></span> (DOM)</a> není k dispozici pro aplikace na straně serveru, stejně jako systém <a href="https://nodejs.org/api/fs.html" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> souborů</a> není k dispozici pro aplikace na straně klienta.

### <a name="nodejs-package-manager-npm"></a>Správce balíčků node.js (NPM)

Chcete-li nainstalovat sadu JavaScript Speech `npm install` SDK, spusťte následující příkaz níže.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Další informace naleznete v <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">rychlém spuštění <span class="docon docon-navigate-external x-hidden-focus"> </span>sady Node.js Speech SDK </a>.
