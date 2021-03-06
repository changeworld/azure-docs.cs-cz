---
title: 'Rychlý start: Vytvoření znalostní báze – REST, Java – QnA Maker'
description: Tento rychlý start založený na REST a Javě vás provede procesem vytvoření ukázkové znalostní báze služby QnA Maker prostřednictvím kódu programu. Tato znalostní báze se zobrazí na řídicím panelu Azure účtu rozhraní API služeb Cognitive Services.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 90ab36389ceac2e8aad12332db433732525c62f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851825"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Rychlý start: Vytvoření znalostní báze ve službě QnA Maker pomocí Javy

Tento rychlý start vás provede vytvořením ukázkové znalostní báze služby QnA Maker pomocí kódu programu. Služba QnA Maker automaticky extrahuje otázky a odpovědi z částečně strukturovaného obsahu, jako jsou třeba časté otázky, ze [zdrojů dat](../Concepts/knowledge-base.md). Model pro znalostní bázi je definovaný v kódu ve formátu JSON poslaném v těle požadavku rozhraní API.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Create KB](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Získat podrobnosti operace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Java Ukázka](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Go 1.10.1](https://golang.org/dl/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst klíč a koncový bod (který obsahuje název prostředku), vyberte **Rychlý start** pro váš prostředek na webu Azure Portal.

[Ukázkový kód](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) je k dispozici na úložišti GitHub pro QnA Maker s Javou.

## <a name="create-a-knowledge-base-file"></a>Vytvoření souboru pro znalostní bázi

Vytvořte soubor s názvem `CreateKB.java`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `CreateKB.java` přidejte následující řádky k přidání potřebných závislostí do projektu:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant
Za předcházející požadované závislosti přidejte do třídy `CreateKB` požadované konstanty pro přístup ke službě QnA Maker.

Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst název klíče a prostředku, vyberte na webu Azure Portal pro prostředek QnA Makeru **rychlý start.**

Nastavte následující hodnoty:

* `<your-qna-maker-subscription-key>`- **Klíč** je řetězec 32 znaků a je k dispozici na portálu Azure, na prostředek QnA Maker, na stránce Rychlý start. To není stejné jako klíč koncového bodu předpověď.
* `<your-resource-name>`- Název **vašeho prostředku** se používá k vytvoření adresy URL koncového `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`bodu pro vytváření ve formátu . Toto není stejná adresa URL, která se používá k dotazování koncového bodu předpověď.

Na konec třídy není potřeba přidávat ukončovací složenou závorku, ta je v konečném fragmentu kódu na konci tohoto rychlého startu.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>Přidání tříd definice modelu znalostní báze
Za konstanty přidejte do třídy `CreateKB` následující třídy a funkce pro serializaci objektu definice modelu do formátu JSON.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Přidejte podpůrné funkce

Dále do třídy `CreateKB` přidejte následující podpůrné funkce.

1. Přidejte následující funkci na tisk JSON v čitelném formátu:

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. Přidejte následující třídu pro správu odpovědi HTTP:

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Přidejte následující metodu pro vytvoření požadavku POST na rozhraní API služby QnA Maker. `Ocp-Apim-Subscription-Key` je klíč služby QnA Maker používaný k ověřování.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Přidejte následující metodu pro vytvoření požadavku GET na rozhraní API služby QnA Maker.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>Přidání metody pro vytvoření znalostní báze
Přidejte následující metodu pro vytvoření znalostní báze zavoláním metody Post.

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i ID operace. Toto ID operace použijte ke zjištění toho, jestli se znalostí báze úspěšně vytvořila.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>Přidání metody pro získání stavu
Přidejte následující metodu pro kontrolu stavu vytváření.

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

Volání opakujte, dokud neskočí úspěchem nebo neúspěchem:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>Přidání metody main
Metoda main vytvoří znalostní bázi a pak se dotazuje na stav. ID operace je vráceno v poli hlavičky odpovědi POST **Lokace**, pak se používá jako součást postupu v požadavku GET. Smyčka `while` opakuje stav, pokud není dokončena.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>Kompilace a spuštění programu

1. Ujistěte se, že adresář `./libs` obsahuje knihovnu gson. Na příkazovém řádku zkompilujte soubor `CreateKB.java`:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Spusťte program zadáním následujícího příkazu na příkazovém řádku. Program pošle požadavek na vytvoření znalostní báze do rozhraní API služby QnA Maker a pak se bude dotazovat na výsledky každých 30 sekund. Každá odpověď se zobrazí v okně konzoly.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Jakmile se znalostní báze vytvoří, můžete se na ni podívat na portálu služby QnA Maker na stránce [vašich znalostních bází](https://www.qnamaker.ai/Home/MyServices).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)