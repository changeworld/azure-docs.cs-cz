---
title: 'Úvodní příručka: Získejte odpověď ze znalostní báze REST, C# - QnA Maker'
description: Tento rychlý start založený na c# rest vás provede získáním odpovědi z znalostní báze programově.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 16093ec5e837b098da3c9b038fe2a57cd76c7151
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851803"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Úvodní příručka: Získejte odpovědi na otázku z znalostní báze c #

Tento rychlý start vás provede programově získáním odpovědi z publikované znalostní báze QnA Maker. Znalostní báze obsahuje otázky a odpovědi ze [zdrojů dat,](../Concepts/knowledge-base.md) jako jsou časté dotazy. [Otázka](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) je odeslána službě QnA Maker. [Odpověď](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) obsahuje nejlépe předpovídanou odpověď.

[Ukázka referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Sample](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs)

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete klíč načíst, vyberte **klíče** v části **Správa prostředků** na řídicím panelu Azure pro prostředek QnA Maker.
* **Publikovat** nastavení stránky. Pokud nemáte publikovanou znalostní bázi, vytvořte prázdnou znalostní bázi, importujte znalostní bázi na stránce **Nastavení** a poté ji publikujte. Tuto [základní znalostní bázi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)si můžete stáhnout a použít .

    Nastavení stránky publikování zahrnuje hodnotu postupu POST, hodnotu hostitele a hodnotu EndpointKey.

    ![Publish settings (Nastavení publikování)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-knowledge-base-project"></a>Vytvoření projektu znalostní báze

1. Otevřete edici Visual Studio 2019 Community Edition.
1. Vytvořte nový projekt konzolové aplikace (.NET Core) a pojmenujte projekt QnaMakerQuickstart. U zbývajících nastavení přijměte výchozí hodnoty.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části Program.cs souboru nahraďte jeden using prohlášení s následujícími řádky přidat potřebné závislosti do projektu:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

V horní části `Program` třídy, `Main`uvnitř , přidejte požadované konstanty pro přístup qnA maker. Tyto hodnoty jsou na stránce **Publikovat** po publikování znalostní báze.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=9-41 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Přidání požadavku POST k odeslání otázky a získání odpovědi

Následující kód provede požadavek HTTPS na rozhraní API qnA maker u odesílání otázky do znalostní báze a obdrží odpověď:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=43-76 "Add a POST request to send question to knowledge base")]

Hodnota `Authorization` záhlaví obsahuje řetězec `EndpointKey`.

Přečtěte si další informace o [žádosti](../how-to/metadata-generateanswer-usage.md#generateanswer-request) a [odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Vytvořte a spusťte program z aplikace Visual Studio. Automaticky odešle požadavek do rozhraní QnA Maker API a poté se vytiskne do okna konzoly.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)