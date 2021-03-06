---
title: 'Úvodní příručka: Vytvoření první funkce v Azure pomocí Visual Studia'
description: V tomto rychlém startu se dozvíte, jak vytvořit a publikovat funkci Azure spouštěnou HTTP pomocí sady Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: e58414fa94a4ef54eb2f288b2cd636c10611460d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308937"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Úvodní příručka: Vytvoření první funkce v Azure pomocí Visual Studia

Funkce Azure umožňuje spouštět kód v prostředí bez serveru, aniž byste museli nejprve vytvořit virtuální počítač nebo publikovat webovou aplikaci.

V tomto rychlém startu se dozvíte, jak pomocí Visual Studia 2019 místně vytvořit a otestovat aplikaci c# spouštěnou na http svět hello, kterou pak publikujete do Azure. 

![Odezva místního hostitele funkce v prohlížeči](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Tento rychlý start je určen pro Visual Studio 2019. 

## <a name="prerequisites"></a>Požadavky

Chcete-li tento kurz dokončit, nainstalujte [nejprve Visual Studio 2019](https://azure.microsoft.com/downloads/). Ujistěte se, že jste během instalace vybrali vývojové úlohy **Azure.** Pokud chcete vytvořit projekt Azure Functions pomocí Visual Studia 2017 místo, musíte nejprve nainstalovat [nejnovější nástroje Azure Functions](functions-develop-vs.md#check-your-tools-version).

![Instalace Visual Studia s úlohou vývoje Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Pokud ještě nemáte [předplatné Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/dotnet/) před tím, než začnete.

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio vytvoří projekt a třídu, která obsahuje často používaný kód pro typ funkce aktivační události HTTP. Atribut `FunctionName` metody nastaví název funkce, která `Function1`je ve výchozím nastavení . Atribut `HttpTrigger` určuje, že funkce je spuštěna požadavkem HTTP. Často používaný kód odešle odpověď HTTP obsahující hodnotu z textu žádosti nebo řetězce dotazu.

Rozšiřte možnosti funkce pomocí vstupních a výstupních vazeb použitím příslušných atributů na metodu. Další informace najdete v části [Triggery a vazby](functions-dotnet-class-library.md#triggers-and-bindings) v [referenčních informacích pro vývojáře v jazyce C# v Azure Functions](functions-dotnet-class-library.md).

Teď máte vytvořený projekt funkcí a funkci aktivovanou protokolem HTTP, takže ji můžete otestovat na místním počítači.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Visual Studio se integruje s nástroji Azure Functions Core Tools, takže můžete otestovat své funkce místně pomocí úplného runtime Azure Functions.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Než budete moci publikovat svůj projekt, musíte mít ve svém předplatném Azure aplikaci funkcí. Publikování v sadě Visual Studio vytvoří aplikaci funkcí při prvním publikování projektu.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte základní adresu URL aplikace funkce ze stránky **Profil publikování.** Nahraďte `localhost:port` část adresy URL, kterou jste použili k místnímu testování funkce, novou základní adresou URL. Přidejte řetězec `?name=<YOUR_NAME>` dotazu k této adrese URL a spusťte požadavek.

    Adresa URL, která volá spuštěnou funkci HTTP, je v následujícím formátu:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Následující obrázek znázorňuje odpověď v prohlížeči na vzdálený požadavek GET vrácený funkcí:

    ![Odezva funkce v prohlížeči](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí Visual Studia vytvořili a publikovali aplikaci funkce C# v Azure pomocí jednoduché funkce aktivované http. 

Přejdete k dalšímu článku, kde se dozvíte, jak do vaší funkce přidat vazbu fronty Azure Storage:
> [!div class="nextstepaction"]
> [Přidání vazby fronty azure storage do vaší funkce](functions-add-output-binding-storage-queue-vs.md)

