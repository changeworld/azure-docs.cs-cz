---
title: Vytvoření aplikace Xamarin s rozhraním .NET a rozhraním API Azure Cosmos DB pro MongoDB
description: Představuje ukázku kódu Xamarin, kterou můžete použít k připojení a dotazování s rozhraním API Služby Azure Cosmos DB pro MongoDB.
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/16/2020
ms.author: masoucou
ms.openlocfilehash: 98b0ddf345ebd19e2cd974db3891e88c9f72530d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79481683"
---
# <a name="quickstart-build-a-xamarinforms-app-with-net-sdk-and-azure-cosmos-dbs-api-for-mongodb"></a>Rychlý start: Vytvoření aplikace Xamarin.Forms s rozhraním .NET SDK a rozhraním API služby Azure Cosmos DB pro MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru Azure Cosmos DB.

Tento rychlý start ukazuje, jak vytvořit [účet Cosmos nakonfigurovaný pomocí rozhraní API Azure Cosmos DB pro MongoDB](mongodb-introduction.md), databázi dokumentů a kolekci pomocí portálu Azure. Potom vytvoříte aplikaci Todo App Xamarin.Forms pomocí [ovladače MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Požadavky na spuštění ukázkové aplikace

Pro spuštění ukázky budete potřebovat sadu [Visual Studio](https://www.visualstudio.com/downloads/) nebo [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) a platný účet služby Azure Cosmos DB.

Pokud ještě visual studio nemáte, stáhněte si [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) s vývojem Mobile s zatížením **.NET** nainstalovaným s nastavením.

Pokud chcete raději pracovat na Macu, stáhněte si [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) a spusťte instalaci.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Ukázka popsaná v tom článku je kompatibilní s MongoDB.Driver verze 2.6.1.

## <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

Nejprve si stáhněte ukázkovou aplikaci z GitHubu. Tato aplikace implementuje aplikaci seznamu úkolů s modelem úložiště dokumentů MongoDB.



# <a name="windows"></a>[Windows](#tab/windows)

1. V systému Windows otevřete příkazový řádek nebo na Macu otevřete terminál, vytvořte novou složku s názvem git-samples a zavřete okno.

    ```batch
    md "C:\git-samples"
    ```

    ```bash
    mkdir '$home\git-samples\
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Pokud nechcete používat git, můžete [si také stáhnout projekt jako soubor ZIP](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string).

Následující úryvky jsou převzaty `MongoService` z třídy, které se nacházejí na následující cestě: src/TaskList.Core/Services/MongoService.cs.

* Inicializuje se klient Mongo.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    settings.RetryWrites = false;

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Načte se odkaz na databázi a kolekci. Sada .NET SDK MongoDB databázi i kolekci automaticky vytvoří, pokud ještě neexistují.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Načtou se všechny dokumenty v podobě seznamu.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Odešle se dotaz na konkrétní dokumenty.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Vytvořte úkol a vložte jej do kolekce.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Aktualizace úkolu v kolekci.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Odstraňte úkol z kolekce.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace.

1. Na webu [Azure Portal](https://portal.azure.com/) klikněte v účtu databáze Azure Cosmos v levém navigačním panelu na možnost **Připojovací řetězec** a potom klikněte na **Klíče pro čtení i zápis**. V dalších krocích pomocí tlačítek pro kopírování na pravé straně obrazovky zkopírujete Primární připojovací řetězec.

2. Otevřete soubor **APIKeys.cs** v adresáři **Helpers** projektu **TaskList.Core**.

3. Z portálu si zkopírujte hodnotu **primárního připojovacího řetězce** (pomocí tlačítka pro kopírování) a nastavte ji jako hodnotu pole **ConnectionString** v souboru **APIKeys.cs**.

4. Odebrat `&replicaSet=globaldb` z připojovacího řetězce. Pokud tuto hodnotu neodeberete z řetězce dotazu, zobrazí se chyba za běhu.

> [!IMPORTANT]
> Chcete-li `&replicaSet=globaldb` se vyhnout chybě za běhu, je nutné odebrat dvojici klíč/hodnota z řetězce dotazu připojovacího řetězce.

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB.

## <a name="run-the-app"></a>Spuštění aplikace

### <a name="visual-studio-2019"></a>Visual Studio 2019

1. V sadě Visual Studio postupně klikněte v **Průzkumníku řešení** pravým tlačítkem na jednotlivé projekty a potom klikněte na **Spravovat balíčky NuGet**.
2. Klikněte na **Obnovit všechny balíčky NuGet**.
3. Klikněte pravým tlačítkem na **TaskList.Android** a vyberte **Nastavit jako spouštěný projekt**.
4. Stisknutím klávesy F5 spusťte ladění aplikace.
5. Pokud chcete aplikaci spustit v iOS, nejprve se váš počítač připojí k Macu (informace o [postupu](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio) najdete tady).
6. Klikněte pravým tlačítkem na **TaskList.iOS** a vyberte **Nastavit jako spouštěný projekt**.
7. Stisknutím klávesy F5 spusťte ladění aplikace.

### <a name="visual-studio-for-mac"></a>Visual Studio pro Mac

1. V závislosti na tom, na které platformě chcete aplikaci spustit, vyberte v rozevíracím seznamu platformy TaskList.iOS nebo TaskList.Android.
2. Stisknutím cmd + Enter spusťte ladění aplikace.

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu služby Azure Cosmos DB a spuštění aplikace Xamarin.Forms pomocí rozhraní API pro MongoDB. Teď můžete do účtu databáze Cosmos importovat další data.

> [!div class="nextstepaction"]
> [Import dat do Azure Cosmos DB nakonfigurovaného pomocí rozhraní API Azure Cosmos DB pro MongoDB](mongodb-migrate.md)
