---
title: Vytvoření webové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a .NET SDK
description: Představuje ukázku kódu .NET, kterou můžete použít k připojení a dotazování pomocí rozhraní API Služby Azure Cosmos DB pro MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.openlocfilehash: 55f40e8f22b39caa3527a378f33982276bed597d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240181"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Úvodní příručka: Vytvoření webové aplikace .NET pomocí rozhraní API služby Azure Cosmos DB pro MongoDB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Můžete rychle vytvářet a dotazovat dokument, klíč/hodnotu a grafdatabáze, které mají prospěch z globální distribuce a horizontální škálování schopnosti v jádru Cosmos DB. 

Tento rychlý start ukazuje, jak vytvořit účet Cosmos s [rozhraním API Azure Cosmos DB pro MongoDB](mongodb-introduction.md). Potom vytvoříte a nasadíte webovou aplikaci se seznamem úloh vytvořenou pomocí [ovladače MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Požadavky na spuštění ukázkové aplikace

Ke spuštění ukázky budete potřebovat [Visual Studio](https://www.visualstudio.com/downloads/) a platný účet Azure Cosmos DB.

Pokud ještě visual studio nemáte, stáhněte si [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) s ASP.NET a zatížení vývoje **mů e-ASP.NET** nainstalovaným s nastavením.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Ukázka popsaná v tom článku je kompatibilní s MongoDB.Driver verze 2.6.1.

## <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

Nejprve si stáhněte ukázkovou aplikaci z GitHubu. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Pokud nechcete použít git, můžete [projekt stáhnout také jako soubor ZIP](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string). 

Všechny následující fragmenty kódu pocházejí ze souboru Dal.cs v adresáři DAL.

* Inicializovat klienta.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* Načte se databáze a kolekce.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Načtou se všechny dokumenty.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

Vytvoření úkolu a jeho vložení do kolekce

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   Podobně můžete dokumenty aktualizovat a odstraňovat pomocí metod [collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) a [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html). 

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace.

1. Na [portálu Azure](https://portal.azure.com/)klikněte ve svém účtu Cosmos na připojovací řetězec v levém **navigačním řetězci**a potom klikněte na **Tlačítko Klíče pro čtení a zápis**. V dalším kroku zkopírujete pomocí tlačítek kopírování na pravé straně obrazovky uživatelské jméno, heslo a hostitele do souboru Dal.cs.

2. Otevřete soubor **Dal.cs** v adresáři **DAL**. 

3. Z portálu zkopírujte hodnotu **username** (pomocí tlačítka kopírování) a nastavte ji jako hodnotu položky **username** v souboru **Dal.cs**. 

4. Potom z portálu zkopírujte hodnotu **host** a nastavte ji jako hodnotu **host** v souboru **Dal.cs**. 

5. Nakonec z portálu zkopírujte hodnotu **password** a nastavte ji jako hodnotu **password** v souboru **Dal.cs**. 

Nyní jste aktualizovali aplikaci se všemi informacemi, které potřebuje ke komunikaci s Cosmos DB. 
    
## <a name="run-the-web-app"></a>Spuštění webové aplikace

1. V sadě Visual Studio klikněte v **Průzkumníku řešení** pravým tlačítkem myši na projekt a potom klikněte na **Spravovat balíčky NuGet**. 

2. Do pole **Procházet** v NuGetu zadejte *MongoDB.Driver*.

3. Z výsledků nainstalujte knihovnu **MongoDB.Driver**. Tím se nainstaluje balíček MongoDB.Driver a všechny závislosti.

4. Spusťte aplikaci stisknutím CTRL+F5. Aplikace se zobrazí v prohlížeči. 

5. V prohlížeči klikněte na **Vytvořit** a vytvořte v aplikaci seznamu úkolů několik nových úloh.

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Cosmos, vytvořit kolekci a spustit konzolovou aplikaci. Nyní můžete importovat další data do databáze Cosmos. 

> [!div class="nextstepaction"]
> [Importování dat MongoDB do databáze Azure Cosmos](mongodb-migrate.md)
