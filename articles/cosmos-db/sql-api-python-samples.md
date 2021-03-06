---
title: Příklady pythonu ROZHRANÍ SQL API pro Azure Cosmos DB
description: Najděte příklady Pythonu na GitHubu pro běžné úlohy v Azure Cosmos DB, včetně operací CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: 1e7c3e06c8aa49f9a23d3cc600ee6f60218c2fbd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "72170143"
---
# <a name="azure-cosmos-db-python-examples"></a>Příklady Azure Cosmos DB Python

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

Ukázková řešení, která provádějí operace CRUD a další běžné operace na prostředcích Azure Cosmos DB, jsou součástí úložiště [GitHub azure-documentdb-python.](https://github.com/Azure/azure-documentdb-python) Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v Pythonu. 
* Odkazy na související referenční obsah rozhraní API.

**Požadavky**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Můžete si [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Za své předplatné sady Visual Studio každý měsíc získáváte kredity, které můžete použít k placení za služby Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Potřebujete taky [Python SDK](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Každá ukázka je samostatná, sama se nastaví a po ukončení příkladu vyčistí svoje prostředky. Ukázky vydat více volání [CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-). Po každém dokončení se na vaše předplatné fakturuje jedna hodina využití. Další informace o fakturaci služby Azure Cosmos DB najdete v tématu [Ceny Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Příklady pro databáze
Soubor [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) projektu [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) ukazuje, jak provést následující úkoly. Informace o databázích Azure Cosmos před spuštěním následujících ukázek najdete v článku [Práce s databázemi, kontejnery a položkami.](databases-containers-items.md) 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření databáze](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L65-L76) |[CosmosClient.CreateDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createdatabase-database--options-none-) |
| [Čtení databáze podle ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L79-L96) |[CosmosClient.ReadDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabase-database-link--options-none-) |
| [Výpis databází pro účet](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L99-L110) |[CosmosClient.ReadDatabáze](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabases-options-none-) |
| [Odstranění databáze](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L113-L126) |[CosmosClient.DeleteDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletedatabase-database-link--options-none-) |

## <a name="collection-examples"></a>Příklady pro kolekce
Soubor [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) projektu [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) ukazuje, jak provést následující úkoly. Informace o kolekcích Azure Cosmos před spuštěním následující ukázky, najdete [v tématu práce s databázemi, kontejnery a položky](databases-containers-items.md) koncepční článek. 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření kolekce](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L84-L135) |[CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-) |
| [Čtení seznamu všech kolekcí v databázi](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L210-L222) |[CosmosClient.ReadContainers](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainers-database-link--options-none-) |
| [Získání kolekce podle ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L190-L208) |[CosmosClient.ReadContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainer-collection-link--options-none-) |
| [Změna propustnosti kolekce](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L184-L188) | [CosmosClient.ReplaceOffer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceoffer-offer-link--offer-)|
| [Odstranění kolekce](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L224-L238) |[CosmosClient.DeleteContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletecontainer-collection-link--options-none-) |

## <a name="document-examples"></a>Příklady pro dokumenty
Soubor [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) projektu [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) ukazuje, jak provést následující úkoly. Informace o dokumentech Azure Cosmos před spuštěním následujícíukázky, najdete [v tématu práce s databázemi, kontejnery a položky](databases-containers-items.md) koncepční článek. 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Vytvoření dokumentu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [Vytvoření kolekce dokumentů](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [Čtení dokumentu podle ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[CosmosClient.ReadItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitem-document-link--options-none-) |
| [Čtení všech dokumentů v kolekci](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[CosmosClient.ReadItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitems-collection-link--feed-options-none-) |
| [Nahrazení dokumentu pomocí podmíněné kontroly ETag](https://github.com/Azure/azure-cosmos-python/blob/a21f6fb4bad3f59909ef43558b598f9fb476b7bc/test/crud_tests.py#L1216-L1218) | [CosmosClient.ReplaceItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceitem-document-link--new-document--options-none-) |

## <a name="indexing-examples"></a>Příklady indexování
Program.py [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) soubor u projektu [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) ukazuje, jak provést následující úkoly.  Další informace o indexování v Azure Cosmos DB před spuštěním následujícíukázky, najdete v [tématu indexování zásady](index-policy.md), [indexování typy](index-types.md)a [indexování cesty](index-paths.md) koncepční články. 

| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Použití ručního indexování (místo automatického)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) | Zásady automatického indexování |
| [Vyloučení zadaných cest dokumentů z indexu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) | Zásady indexování s vyloučenými cestami|
| [Vyloučení dokumentu z indexu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[IndexingDirective.Exclude](/python/api/azure-cosmos/azure.cosmos.documents.indexingdirective#exclude) |
| [Nastavení režimu indexování](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[IndexováníRežim](/python/api/azure-cosmos/azure.cosmos.documents.indexingmode) |
| [Použití indexů rozsahu na řetězce](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) | Zásady indexování s zahrnutými cestami|
| [Provedení transformace indexu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[CosmosClient.ReplaceContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replacecontainer-collection-link--collection--options-none-) |

## <a name="query-examples"></a>Příklady dotazů
Ukázkové projekty také ukazují, jak provést následující úkoly dotazu. Informace o odkazu na dotaz SQL v Azure Cosmos DB před spuštěním následujících ukázek najdete v článku [příklady dotazů SQL.](how-to-sql-query.md) Informace o odkazu na dotaz SQL v Azure Cosmos DB před spuštěním následujících ukázek najdete v článku [příklady dotazů SQL.](how-to-sql-query.md) 


| Úkol | referenční dokumentace k rozhraní API |
| --- | --- |
| [Dotaz na účet pro databázi](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L49-L62) |[CosmosClient.QueryDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#querydatabases-query--options-none-) |
| [Dotaz na dokumenty](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[CosmosClient.QueryItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#queryitems-database-or-container-link--query--options-none--partition-key-none-) |
| [Vynucení operace prohledávání rozsahu v cestě s indexem hash](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[HttpHeaders.EnableScanInQuery](/python/api/azure-cosmos/azure.cosmos.http_constants.httpheaders#enablescaninquery) |

