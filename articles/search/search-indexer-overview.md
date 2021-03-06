---
title: Indexery pro procházení dat během importu
titleSuffix: Azure Cognitive Search
description: Procházení azure sql databáze, Azure Cosmos DB nebo úložiště Azure extrahovat prohledávatelná data a naplnit index Azure cognitive search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282988"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexery ve službě Azure Cognitive Search

*Indexer* v Azure Cognitive Search je prohledávač, který extrahuje prohledávatelná data a metadata z externího zdroje dat Azure a naplní index založený na mapování pole na pole mezi indexem a zdrojem dat. Tento přístup se někdy označuje jako "model vyžádat", protože služba získává data, aniž byste museli psát jakýkoli kód, který přidává data do indexu.

Indexery jsou založené na typech nebo platformách zdrojů dat, s jednotlivými indexery pro SQL Server v Azure, Cosmos DB, Azure Table Storage a Blob Storage. Indexery úložiště objektů blob mají další vlastnosti specifické pro typy obsahu objektů blob.

Můžete použít indexer jako jediný prostředek přijímání dat, nebo můžete použít kombinaci postupů, kdy se indexer použije k načtení jenom některých polí v indexu.

Indexery můžete spouštět na vyžádání nebo podle plánu opakované aktualizace dat, který běží tak často, jak každých pět minut. Častější aktualizace vyžadují nabízený model, který současně aktualizuje data v Azure Cognitive Search i ve vašem externím zdroji dat.

## <a name="approaches-for-creating-and-managing-indexers"></a>Přístupy k vytváření a správě indexerů

Indexery můžete vytvářet a spravovat pomocí těchto přístupů:

* [Průvodce importem dat portálu >](search-import-data-portal.md)
* [Rozhraní API služby REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [Sada SDK rozhraní .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Nový indexer je zpočátku oznámen jako funkce ve verzi Preview. Funkce ve verzi Preview se zavádějí v rozhraních API (REST a .NET) a po přechodu do všeobecné dostupnosti se integrují do portálu. Pokud vyhodnocujete nový indexer, měli byste počítat s psaním kódu.

## <a name="permissions"></a>Oprávnění

Všechny operace související s indexery, včetně get požadavky na stav nebo definice, vyžadují [správce api-key](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Podporované zdroje dat

Indexery procházet úložiště dat v Azure.

* [Úložiště objektů blob Azure](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (ve verzi Preview)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server na Azure Virtual Machines](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Instance spravované SQL v Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Postup základní konfigurace
Indexery můžou nabízet funkce, které jsou jedinečné pro daný zdroj dat. Z toho důvodu se budou některé aspekty konfigurace indexeru nebo zdroje dat lišit podle typu indexeru. Všechny indexery ale sdílejí stejné základní složení a požadavky. Níže najdete popis kroků společných pro všechny indexery.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat
Indexer získá připojení ke zdroji dat z objektu *zdroje dat.* Definice zdroje dat poskytuje připojovací řetězec a případně pověření. Volání [vytvořit rozhraní](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API zdroje dat nebo [DataSource třídy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) k vytvoření prostředku.

Zdroje dat se konfigurují a spravují nezávisle na indexerech, které je používají, což znamená, že několik indexerů může používat zdroj dat k načtení více indexů současně.

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Indexer automatizuje některé úkoly související s příjmem dat, ale vytváření indexu k nim obvykle nepatří. K základním požadavkům patří předdefinovaný index s poli, která odpovídají polím v externím zdroji dat. Pole se musí shodovat podle názvu a datového typu. Další informace o strukturování indexu najdete [v tématu vytvoření indexu (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) nebo [index třídy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Nápovědu k přidružení polí najdete [v tématu Mapování polí v indexerech Azure Cognitive Search](search-indexer-field-mappings.md).

> [!Tip]
> Přestože indexery nedokážou vygenerovat index za vás, může vám pomoct průvodce **importem dat** na portálu. Ve většině případů dokáže průvodce odvodit schéma indexu ze stávajících metadat ve zdroji a zobrazit předběžné schéma indexu, které můžete upravit přímo v aktivním průvodci. Po vytvoření indexu ve službě jsou další úpravy na portálu omezené hlavně na přidávání nových polí. K vytvoření, ale ne revidování, indexu zvažte použití průvodce. Praktickou výuku najdete v [průvodci portálem](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Krok 3: Vytvoření a naplánování indexeru
Definice indexeru je konstrukce, která sdružuje všechny prvky související s ingestování dat. Požadované prvky zahrnují zdroj dat a index. Volitelné prvky zahrnují mapování plánu a polí. Mapování polí je volitelné pouze v případě, že zdrojová pole a pole indexu jasně odpovídají. Další informace o strukturování indexeru naleznete v [tématu Vytvoření indexeru (Azure Cognitive Search REST API).](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Spuštění indexerů na vyžádání

I když je běžné naplánovat indexování, indexer lze také vyvolat na vyžádání pomocí [příkazu Spustit](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Při úspěšném vrácení rozhraní API spustit, vyvolání indexeru bylo naplánováno, ale skutečné zpracování probíhá asynchronně. 

Můžete sledovat stav indexeru na portálu nebo prostřednictvím rozhraní Get Indexer Status API. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Získat stav indexeru

Historii stavu a spuštění indexeru můžete načíst pomocí [příkazu Získat stav indexeru](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Odpověď obsahuje celkový stav indexeru, poslední (nebo probíhající) vyvolání indexeru a historii nedávné vyvolání indexeru.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Historie spuštění obsahuje až 50 posledních dokončených spuštění, které jsou seřazeny v obráceném chronologickém pořadí (takže nejnovější spuštění je na prvním místě v odpovědi).

## <a name="next-steps"></a>Další kroky
Teď jste získali základní představu. V dalším kroku se zaměříme na požadavky a úlohy specifické pro různé typy zdrojů dat.

* [Azure SQL Database nebo SQL Server na virtuálním počítači Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Úložiště objektů blob Azure](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Indexování objektů BLOB CSV pomocí indexeru objektů blob azure cognitive search](search-howto-index-csv-blobs.md)
* [Indexování objektů BLOB JSON pomocí azure cognitive search blob indexer](search-howto-index-json-blobs.md)
