---
title: 'Azure Cosmos DB: příklady v Javě pro rozhraní SQL API'
description: Vyhledejte si příklady v Javě na GitHubu pro běžné úlohy prováděné pomocí rozhraní SQL API služby Azure Cosmos DB, včetně operací CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: sample
ms.date: 02/08/2019
ms.author: sngun
ms.openlocfilehash: 8b133f0044bdf8f99fdee657177d561ef5bb406b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238479"
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB: příklady v Javě pro rozhraní SQL API

> [!div class="op_single_selector"]
> * [Příklady sady .NET V2 SDK](sql-api-dotnet-samples.md)
> * [Příklady sady .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Příklady v Javě](sql-api-java-samples.md)
> * [Příklady v asynchronní Javě](sql-api-async-java-samples.md)
> * [Příklady v Node.js](sql-api-nodejs-samples.md)
> * [Příklady v Pythonu](sql-api-python-samples.md)
> * [Galerie ukázkového kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Nejnovější ukázkové aplikace, které provádí operace CRUD a další běžné operace s prostředky služby Azure Cosmos DB, jsou součástí úložiště [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) na GitHubu. Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v Javě. 
* Odkazy na související referenční obsah rozhraní API.

**Požadavky**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Můžete si [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Za své předplatné sady Visual Studio každý měsíc získáváte kredity, které můžete použít k placení za služby Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Ke spuštění této ukázkové aplikace budete potřebovat:

* Sadu Java Development Kit (JDK) 7
* Sadu Microsoft Azure DocumentDB Java SDK

Volitelně můžete použít Maven k získání nejnovějších binárních souborů sady Microsoft Azure DocumentDB Java SDK pro použití ve vašem projektu. Maven automaticky přidá všechny potřebné závislosti. Můžete taky přímo stáhnout závislosti uvedené v souboru pom.xml a přidat je do cesty sestavení.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**Spuštění ukázkových aplikací**

Naklonování ukázkového úložiště:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

Ukázky můžete spustit buď pomocí Eclipse nebo z příkazového řádku pomocí Mavenu.

Spuštění z Eclipse:
* Načtěte soubor pom.xml hlavního nadřazeného projektu do Eclipse; měl by automaticky načíst příklady documentdb.
* Ke spuštění příkladů budete potřebovat platný koncový bod služby Azure Cosmos DB. Koncové body se načítají z `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java`.
* Přihlašovací údaje koncového bodu můžete předat jako argumenty virtuálních počítačů v Eclipse JUnit Run Config nebo můžete přihlašovací údaje koncového bodu vložit do souboru AccountCredentials.java.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Nyní můžete spustit ukázky jako testy JUnit v Eclipse.

Spuštění z příkazového řádku:
* Další způsob, jak můžete spustit ukázky, je použít Maven:
* Spusťte Maven a předejte přihlašovací údaje koncového bodu služby Azure Cosmos DB:
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > Každá ukázka je samostatná, sama se nastaví a po ukončení příkladu vyčistí svoje prostředky. Ukázky vydávat více volání [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection). Pokaždé, když k tomu dojde, bude pro vaše předplatné účtována 1 hodina využití pro úroveň výkonu vytvořené kolekce. 
   > 
   > 

## <a name="database-examples"></a>Příklady pro databáze
Soubor [DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) ukazuje, jak provádět následující úkoly. Informace o databázích Azure Cosmos před spuštěním následujících ukázek najdete v článku [Práce s databázemi, kontejnery a položkami.](databases-containers-items.md) 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření a čtení databáze](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |
| [Vytvoření a odstranění databáze](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedatabase) |
| [Vytvoření a dotazování databáze](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydatabases) |

## <a name="collection-examples"></a>Příklady pro kolekce
Soubor [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) ukazuje, jak provádět následující úkoly. Informace o kolekcích Azure Cosmos před spuštěním následující ukázky, najdete [v tématu práce s databázemi, kontejnery a položky](databases-containers-items.md) koncepční článek.

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce tvořené jedním oddílem](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [Vytvoření vlastní kolekce s více oddíly](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions) |
| [Odstranění kolekce](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletecollection) |

## <a name="document-examples"></a>Příklady pro dokumenty
Soubor [DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) ukazuje, jak provádět následující úkoly. Informace o dokumentech Azure Cosmos před spuštěním následujícíukázky, najdete [v tématu práce s databázemi, kontejnery a položky](databases-containers-items.md) koncepční článek.

| Úkol | API – referenční informace |
| --- | --- |
| [Vytváření, čtení a odstranění dokumentu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument) |
| [Vytvoření dokumentu s programovatelnou definicí dokumentu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [Dokumentu](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |

## <a name="indexing-examples"></a>Příklady indexování
Soubor [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) ukazuje, jak provádět následující úkoly. Další informace o indexování v Azure Cosmos DB před spuštěním následujícíukázky, najdete v [tématu indexování zásady](index-policy.md), [indexování typy](index-types.md)a [indexování cesty](index-paths.md) koncepční články. 

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření indexu a nastavení zásad indexování](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy) |

Další informace o indexování najdete v tématu pojednávajícím o [zásadách indexování služby Azure DB Cosmos](index-policy.md).

## <a name="query-examples"></a>Příklady dotazů
Soubor [DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) ukazuje, jak provádět následující úkoly. Informace o odkazu na dotaz SQL v Azure Cosmos DB před spuštěním následujících ukázek najdete v článku [příklady dotazů SQL.](how-to-sql-query.md) 


| Úkol | API – referenční informace |
| --- | --- |
| [Provedení jednoduchého dotazu na dokument napříč oddíly](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setenablecrosspartitionquery) |
| [Řazení podle dotazu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse\<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse.getqueryiterator) |

Další informace o psaní dotazů najdete v tématu věnovaném [dotazům SQL ve službě Azure Cosmos DB](how-to-sql-query.md).

## <a name="offer-examples"></a>Příklady pro nabídky
Soubor [OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) ukazuje, jak provádět následující úlohy:

| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce a nastavení propustnosti](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions.setofferthroughput) |
| [Čtení kolekce kvůli nalezení související nabídky](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.queryoffers) |

## <a name="partition-key-examples"></a>Příklady pro klíče oddílu
[SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) soubor ukazuje, jak provádět následující úkoly. Další informace o dělení a klíče oddílů v Azure Cosmos DB před spuštěním následující ukázky, najdete v článku [dělení](partitioning-overview.md) koncepční. 


| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření kolekce tvořené jedním oddílem](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [Změna nabídky propustnost pro kolekci tvořenou jedním oddílem](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>Příklady pro uložené procedury
Soubor [StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) ukazuje, jak provádět následující úkoly. Informace o programování na straně serveru v Azure Cosmos DB před spuštěním následující ukázky, najdete [v tématu uložené procedury, aktivační události a uživatelem definované funkce](stored-procedures-triggers-udfs.md) koncepční článek. 


| Úkol | API – referenční informace |
| --- | --- |
| [Vytvoření uložené procedury](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createstoredprocedure) |
| [Spuštění uložené procedury s argumenty](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
| [Spuštění uložené procedury s argumenty objektu](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
