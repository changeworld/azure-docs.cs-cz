---
title: 'Azure Cosmos DB: Vytvoření aplikace seznamu úkolů v prostředí Xamarin'
description: Obsahuje ukázku kódu Xamarin, kterou můžete použít k připojení ke službě Azure Cosmos DB a jejímu dotazování.
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/30/2018
ms.author: masoucou
ms.openlocfilehash: 125cc2516fbd694bbe62545e85d4e14225fb2a6a
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756427"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Úvodní příručka: Vytvoření aplikace todo s Xamarinem pomocí účtu SQL API Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru Azure Cosmos DB.

> [!NOTE]
> Vzorový kód celé ukázkové kanonické aplikace Xamarin ukazující několik nabídek Azure, včetně služby Cosmos DB, najdete [tady](https://github.com/xamarinhq/app-geocontacts) na GitHubu. Tato aplikace ukazuje, jak zobrazit kontakty rozptýlené v různých geografických umístěních a jak těmto kontaktům umožnit aktualizaci svého umístění.

V tomto rychlém startu se dozvíte, jak vytvořit účet rozhraní SQL API služby Azure Cosmos DB, databázi dokumentů a kontejner pomocí webu Azure Portal. Potom vytvoříte a nasadíte mobilní aplikaci seznamu úkolů postavenou na [rozhraní CHAPI SQL .NET API](sql-api-sdk-dotnet.md) a [Xamarinu](https://docs.microsoft.com/xamarin/) využívajícím [Xamarin.Forms](https://docs.microsoft.com/xamarin/) a [architektonický vzor MVVM](https://docs.microsoft.com/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm).

![Aplikace seznamu úkolů Xamarin spuštěná v systému iOS](./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png)

## <a name="prerequisites"></a>Požadavky

Pokud vyvíjíte v systému Windows a ještě nemáte nainstalovanou Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [verzi Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit sady funkcí **Vývoj pro Azure** a **Vývoj mobilních aplikací pomocí .NET**.

Pokud používáte Mac, můžete si stáhnout **bezplatnou verzi** [Visual Studio pro Mac](https://www.visualstudio.com/vs/mac/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Přidání kontejneru

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci Xamarin SQL API z GitHubu, zkontrolujeme kód, získáme klíče rozhraní API a spusťte ho. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu.

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a pak příkazový řádek zavřete.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. Pak v sadě Visual Studio otevřete soubor ToDoItems.sln ze složky samples/xamarin/ToDoItems.

## <a name="obtain-your-api-keys"></a>Získání klíčů rozhraní API

Vraťte se na Azure Portal, kde najdete informace o klíčích rozhraní API, a zkopírujte je do aplikace.

1. Na webu [Azure Portal](https://portal.azure.com/) v levém navigačním panelu vašeho účtu rozhraní SQL API služby Azure Cosmos DB klikněte na **Klíče** a pak na **Klíče pro čtení i zápis**. V dalším kroku zkopírujete pomocí tlačítek kopírování na pravé straně obrazovky identifikátor URI a primární klíč do souboru APIKeys.cs.

    ![Zobrazení a zkopírování přístupového klíče na portálu Azure Portal v okně Klíče](./media/create-sql-api-xamarin-dotnet/keys.png)

2. Ve Visual Studiu 2019 nebo Visual Studiu pro Mac otevřete soubor APIKeys.cs ve složce azure-cosmos-db-sql-xamarin-getting-started/src/ToDoItems.Core/Helpers.

3. Z portálu zkopírujte hodnotu identifikátoru URI (pomocí tlačítka kopírování) a nastavte ji jako hodnotu proměnné `CosmosEndpointUrl` v souboru APIKeys.cs.

    `public static readonly string CosmosEndpointUrl = "";`

4. Potom z portálu zkopírujte hodnotu PRIMÁRNÍHO KLÍČE a nastavte ji jako hodnotu `Cosmos Auth Key` v souboru APIKeys.cs.

    `public static readonly string CosmosAuthKey = "";`

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>Kontrola kódu

Toto řešení ukazuje, jak vytvořit aplikaci seznamu úkolů s využitím rozhraní SQL API služby Azure Cosmos DB a Xamarin.Forms. Aplikace obsahuje dvě karty. První karta obsahuje zobrazení seznamu ještě nesplněných položek seznamu úkolů. Druhá karta zobrazuje splněné položky seznamu úkolů. Kromě zobrazení nesplněných položek seznamu úkolů můžete na první kartě také přidávat nové položky seznamu úkolů, upravovat stávající a označovat položky jako splněné.

![Zkopírujte data json a v Průzkumníku dat na webu Azure Portal klikněte na Uložit.](./media/create-sql-api-xamarin-dotnet/android-todo-screen.png)

Kód v řešení ToDoItems obsahuje:

* ToDoItems.Core: Toto je projekt .NET Standard obsahující projekt Xamarin.Forms a kód sdílené logiky aplikace, který udržuje položky seznamu úkolů ve službě Azure Cosmos DB.
* ToDoItems.Android: Tento projekt obsahuje aplikaci pro Android.
* ToDoItems.iOS: Tento projekt obsahuje aplikaci pro iOS.

Teď se rychle podíváme na to, jak aplikace komunikuje se službou Azure Cosmos DB.

* Do všech projektů je potřeba přidat balíček NuGet [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).
* Třída `ToDoItem` ve složce azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Models modeluje dokumenty v kontejneru **Items** vytvořeném výše. Mějte na paměti, že v názvech vlastností se rozlišují malá a velká písmena.
* Třída `CosmosDBService` ve složce azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Services zapouzdřuje komunikaci se službou Azure Cosmos DB.
* V třídě `CosmosDBService` je proměnná typu `DocumentClient`. Používá `DocumentClient` se ke konfiguraci a spouštění požadavků proti účtu Azure Cosmos DB a je vytvořena instance:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Při dotazování kontejneru pro `DocumentClient.CreateDocumentQuery<T>` dokumenty se používá metoda, `CosmosDBService.GetToDoItems` jak je vidět zde ve funkci:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    Trvá `CreateDocumentQuery<T>` IDENTIFIKÁTOR URI, který odkazuje na kontejner vytvořený v předchozí části. Můžete také zadat operátory LINQ, jako je například klauzule `Where`. V tomto případě se vrátí pouze nesplněné položky seznamu úkolů.

    Funkce `CreateDocumentQuery<T>` se provádí asynchronně a vrací `IQueryable<T>`. Metoda `AsDocumentQuery` však převede `IQueryable<T>` na objekt `IDocumentQuery<T>`, který je možné spustit asynchronně. Díky tomu se v mobilních aplikacích neblokuje vlákno uživatelského rozhraní.

    Funkce `IDocumentQuery<T>.ExecuteNextAsync<T>` načte stránku výsledků ze služby Azure Cosmos DB a pomocí `HasMoreResults` zkontroluje, jestli zbývá vrátit ještě nějaké výsledky.

> [!TIP]
> Několik funkcí, které pracují na kontejnerech a dokumentech Azure Cosmos, považuje identifikátor URI jako parametr, který určuje adresu kontejneru nebo dokumentu. Tento identifikátor URI se vytváří pomocí třídy `URIFactory`. Pomocí této třídy lze vytvářet identifikátory URI pro databáze, kontejnery a dokumenty.

* Funkce `ComsmosDBService.InsertToDoItem` ukazuje, jak vložit nový dokument:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    Položka URI je zadána, stejně jako položka, která má být vložena.

* Funkce `CosmosDBService.UpdateToDoItem` ukazuje, jak nahradit existující dokument novým dokumentem:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Zde je potřeba nový identifikátor URI k jednoznačné identifikaci `UriFactory.CreateDocumentUri` dokumentu, který má být nahrazen, a je získán pomocí a předáním názvů databáze a kontejnerů a ID dokumentu.

    Metoda `DocumentClient.ReplaceDocumentAsync` nahradí dokument určený identifikátorem URI za nový dokument určený jako parametr.

* Odstranění položky je demonstrováno pomocí `CosmosDBService.DeleteToDoItem` funkce:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Znovu si všimněte jedinečného dokumentu `DocumentClient.DeleteDocumentAsync` URI, který je vytvářen a předán funkci.

## <a name="run-the-app"></a>Spuštění aplikace

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB.

V následujících krocích se dozvíte, jak aplikaci spustit s použitím ladicího programu sady Visual Studio pro Mac.

> [!NOTE]
> Použití aplikace ve verzi pro Android je naprosto stejné, případné rozdíly budou popsané v následujících krocích. Pokud chcete ladit v sadě Visual Studio ve Windows, přečtěte si návod v příslušné dokumentaci pro [iOS](https://docs.microsoft.com/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin) a [Android](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/).

1. Nejprve vyberte platformu, na kterou chcete cílit, kliknutím na zvýrazněnou rozevírací nabídku a výběrem možnosti ToDoItems.iOS pro iOS nebo ToDoItems.Android pro Android.

    ![Výběr platformy pro ladění v sadě Visual Studio pro Mac](./media/create-sql-api-xamarin-dotnet/ide-select-platform.png)

2. Pokud chcete zahájit ladění aplikace, stiskněte Cmd + Enter nebo klikněte na tlačítko Přehrát.

    ![Zahájení ladění v sadě Visual Studio pro Mac](./media/create-sql-api-xamarin-dotnet/ide-start-debug.png)

3. Po spuštění simulátoru iOS nebo emulátoru Android se v dolní (iOS) nebo horní (Android) části obrazovky aplikace zobrazí 2 karty. Na první kartě se zobrazí ještě nesplněné položky seznamu úkolů a na druhé se zobrazí splněné položky seznamu úkolů.

    ![Spouštěcí obrazovka aplikace seznamu úkolů](./media/create-sql-api-xamarin-dotnet/ios-droid-started.png)

4. Pokud chcete splnit položku seznamu úkolů v iOS, posuňte ji doleva a klepněte na tlačítko **Complete** (Splněno). Pokud chcete splnit položku seznamu úkolů v Androidu, dlouze ji stiskněte a pak klepněte na tlačítko Complete (Splněno).

    ![Splnění položky seznamu úkolů](./media/create-sql-api-xamarin-dotnet/simulator-complete.png)

5. Pokud chcete upravit položku seznamu úkolů, klepněte na ni a zobrazí se nová obrazovka, na které můžete zadat nové hodnoty. Klepnutím na tlačítko Save (Uložit) se změny uloží do služby Azure Cosmos DB.

    ![Úprava položky seznamu úkolů](./media/create-sql-api-xamarin-dotnet/simulator-edit.png)

6. Pokud chcete přidat položku seznamu úkolů, klepněte na tlačítko **Add** (Přidat) v pravém horním rohu domovské obrazovky a zobrazí se nová prázdná stránka pro úpravy.

    ![Přidání položky seznamu úkolů](./media/create-sql-api-xamarin-dotnet/simulator-add.png)

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Azure Cosmos, vytvořit kontejner pomocí Průzkumníka dat a vytvořit a nasadit aplikaci Xamarin. Teď můžete importovat další data do svého účtu Azure Cosmos.

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
