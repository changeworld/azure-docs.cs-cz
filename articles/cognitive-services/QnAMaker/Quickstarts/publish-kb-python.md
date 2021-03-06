---
title: 'Úvodní příručka: Publikování znalostní báze REST, Python - QnA Maker'
description: Tento rychlý start založený na Pythonu REST publikuje znalostní bázi a vytvoří koncový bod, který lze volat ve vaší aplikaci nebo chatovacím robotovi.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 6f053ecbd58d3c2527c1b904437dcc4715c76af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851649"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí Pythonu

Tento rychlý start založený na rest vás provede programovým publikováním znalostní báze (KB). Publikování odešle nejnovější verzi znalostní báze do vyhrazeného indexu Azure Cognitive Search a vytvoří koncový bod, který lze volat ve vaší aplikaci nebo chatovacím robotu.

Tento rychlý start volá qnA maker REST API:
* [Publikování](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* [Python 3.7](https://www.python.org/downloads/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst klíč a koncový bod (který obsahuje název prostředku), vyberte **Rychlý start** pro váš prostředek na webu Azure Portal.
* ID znalostní báze QnA Maker (KB) nalezené v adrese URL v parametru řetězce dotazu, `kbid` jak je znázorněno níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](../how-to/create-knowledge-base.md).

> [!NOTE]
> Kompletní soubor řešení jsou k dispozici v [ **azure-samples/cognitive-services-qnamaker-python** úložiště GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-knowledge-base-python-file"></a>Vytvoření souboru Pythonu pro znalostní bázi

Vytvořte soubor s názvem `publish-kb-3x.py`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `publish-kb-3x.py` přidejte následující řádky k přidání potřebných závislostí do projektu:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Přidání požadovaných konstant

Za předcházející požadované závislosti přidejte požadované konstanty pro přístup ke službě QnA Maker. Nahraďte hodnoty vlastními.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Přidat požadavek POST pro publikování znalostní báze

Po požadovaných konstantách přidejte následující kód, který do rozhraní API qnA makeru vytvoří požadavek HTTPS, aby publikoval znalostní bázi a obdrží odpověď:

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. Kód v případě odpovědi se stavem 204 přidá obsah.

V případě jakékoli jiné odpověď se vrátí tato odpověď beze změny.

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Spusťte program zadáním následujícího příkazu na příkazovém řádku. Odešle požadavek do rozhraní API QnA Maker, aby publikoval znalostní bázi a pak vytiskl 204 pro úspěch nebo chyby.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Po publikování znalostní báze potřebujete [adresu URL koncového bodu ke generování odpovědi](./get-answer-from-knowledge-base-python.md).

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)

[Přehled služby QnA Maker](../Overview/overview.md)