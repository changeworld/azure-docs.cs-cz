---
title: Navrhování tabulek
description: Úvod do navrhování tabulek v fondu Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2802c62acef0d78f8cfa7dd7f06bc34d8eecca4c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742622"
---
# <a name="design-tables-in-synapse-sql-pool"></a>Návrhové tabulky v fondu Synapse SQL

Tento článek obsahuje klíčové úvodní koncepty pro navrhování tabulek ve fondu SQL.

## <a name="determine-table-category"></a>Určit kategorii tabulky

Hvězdné [schéma](https://en.wikipedia.org/wiki/Star_schema) uspořádá data do tabulek faktů a dimenzí. Některé tabulky se používají pro integraci nebo přípravu dat před přesunem do tabulky faktů nebo dimenzí. Při navrhování tabulky se rozhodněte, zda data tabulky patří do tabulky faktů, dimenzí nebo integrace. Toto rozhodnutí informuje příslušnou strukturu a distribuci tabulky.

- **Tabulky faktů** obsahují kvantitativní data, která jsou běžně generována v transakčním systému a poté načtena do fondu SQL. Například maloobchodní firma generuje prodejní transakce každý den a pak načte data do tabulky faktů fondu SQL pro analýzu.

- **Tabulky dimenzí** obsahují data atributů, která se mohou měnit, ale obvykle se mění zřídka. Například jméno a adresa zákazníka jsou uloženy v tabulce dimenzí a aktualizovány pouze při změně profilu zákazníka. Chcete-li minimalizovat velikost tabulky velkých faktů, nemusí být jméno a adresa zákazníka v každém řádku tabulky faktů. Místo toho může tabulka faktů a tabulka dimenzí sdílet ID zákazníka. Dotaz může spojit dvě tabulky a přidružit profil a transakce zákazníka.

- **Integrační tabulky** poskytují místo pro integraci nebo přípravu dat. Integrační tabulku můžete vytvořit jako běžnou tabulku, externí tabulku nebo dočasnou tabulku. Můžete například načíst data do pracovní tabulky, provádět transformace na datech v pracovní min. a potom je vložit do produkční tabulky.

## <a name="schema-and-table-names"></a>Názvy schémat a tabulek

Schémata jsou dobrým způsobem, jak seskupit tabulky, používané podobným způsobem, společně.  Pokud migrujete více databází z řešení on-prem do fondu SQL, je nejlepší migrovat všechny tabulky faktů, dimenzí a integrace do jednoho schématu ve fondu SQL.

Například můžete uložit všechny tabulky v [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ukázkový fond SQL v rámci jednoho schématu s názvem druhé světové války. Následující kód vytvoří [uživatelem definované schéma](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) nazývané první světová válka.

```sql
CREATE SCHEMA wwi;
```

Chcete-li zobrazit organizaci tabulek ve fondu SQL, můžete použít fakt, dim a int jako předpony názvů tabulek. V následující tabulce jsou uvedeny některé názvy schématu a tabulky pro WideWorldImportersDW.  

| Tabulka WideWorldImportersDW  | Typ tabulky | Fond SQL |
|:-----|:-----|:------|:-----|
| Město | Dimenze | Wwi. DimCity |
| Objednání | Fact | Wwi. Příkaz faktu |

## <a name="table-persistence"></a>Trvalost tabulky

Tabulky ukládají data buď trvale ve službě Azure Storage, dočasně ve službě Azure Storage, nebo v úložišti dat mimo fond SQL.

### <a name="regular-table"></a>Běžná tabulka

Běžná tabulka ukládá data ve službě Azure Storage jako součást fondu SQL. Tabulka a data přetrvávají bez ohledu na to, zda je otevřena relace.  Následující příklad vytvoří běžnou tabulku se dvěma sloupci.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Dočasná tabulka

Dočasná tabulka existuje pouze po dobu trvání relace. Pomocí dočasné tabulky můžete ostatním uživatelům zabránit v zobrazení dočasných výsledků a také snížit potřebu čištění.  

Dočasné tabulky využívají místní úložiště k rychlému výkonu.  Další informace naleznete v [tématu Dočasné tabulky](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Externí tabulka

Externí tabulka odkazuje na data umístěná v objektu blob Azure Storage nebo Azure Data Lake Store. Při použití ve spojení s příkazem CREATE TABLE AS SELECT importuje výběr z externí tabulky data do fondu SQL.

Jako takové externí tabulky jsou užitečné pro načítání dat. Kurz načítání najdete v [tématu Použití PolyBase k načtení dat z úložiště objektů blob Azure](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Typy dat

Fond SQL podporuje nejčastěji používané datové typy. Seznam podporovaných datových typů naleznete [v tématu datové typy v odkazu VYTVOŘIT TABULKU](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) v příkazu CREATE TABLE. Pokyny k používání datových typů najdete [v tématu Datové typy](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Distribuované tabulky

Základním rysem fondu SQL je způsob, jakým může ukládat a pracovat na tabulkách napříč [distribucemi](massively-parallel-processing-mpp-architecture.md#distributions).  Fond SQL podporuje tři metody pro distribuci dat: round-robin (výchozí), hash a replikovány.

### <a name="hash-distributed-tables"></a>Distribuované zatřiďovací tabulky (distribuce hodnot hash)

Distribuovaná tabulka hash distribuuje řádky na základě hodnoty ve sloupci distribuce. Distribuovaná tabulka hash je navržena tak, aby dosáhla vysokého výkonu pro dotazy na velké tabulky. Existuje několik faktorů, které je třeba vzít v úvahu při výběru distribučního sloupce.

Další informace naleznete v [pokynech k návrhu pro distribuované tabulky](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Replikované tabulky

Replikovaná tabulka má úplnou kopii tabulky, která je k dispozici na každém výpočetním uzlu. Dotazy běží rychle na replikovaných tabulkách, protože spojení v replikovaných tabulkách nevyžadují přesun dat. Replikace vyžaduje další úložiště, i když a není praktické pro velké tabulky.

Další informace naleznete v [pokynech k návrhu pro replikované tabulky](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Kulaté stoly

Tabulka kruhového dotazování rozděluje řádky tabulky rovnoměrně napříč všemi distribucemi. Řádky jsou distribuovány náhodně. Načítání dat do tabulky kruhového dotazování je rychlé.  Mějte na paměti, že dotazy mohou vyžadovat větší přesun dat než jiné metody distribuce.

Další informace naleznete v [pokynech k návrhu pro distribuované tabulky](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Běžné metody distribuce pro tabulky

Kategorie tabulky často určuje, kterou možnost zvolit pro distribuci tabulky.

| Kategorie tabulky | Doporučená možnost distribuce |
|:---------------|:--------------------|
| Fact           | Použití distribuce hash s indexem clusterovaného columnstore. Výkon se zlepší, když jsou dvě tabulky hash spojeny ve stejném sloupci distribuce. |
| Dimenze      | Replikované pro menší tabulky. Pokud jsou tabulky příliš velké pro uložení na každém výpočetním uzlu, použijte hash distribuované. |
| Příprava        | Pro pracovní tabulku použijte kruhové dotazování. Zatížení ctas je rychlé. Jakmile jsou data v pracovní tabulce, použijte INSERT... SELECT pro přesunutí dat do produkčních tabulek. |

## <a name="table-partitions"></a>Oddíly tabulky

Dělená tabulka ukládá a provádí operace na řádcích tabulky podle oblastí dat. Tabulka může být například rozdělena podle dne, měsíce nebo roku. Můžete zlepšit výkon dotazu prostřednictvím eliminace oddílu, který omezuje prohledávací dotaz na data v rámci oddílu. Data můžete také udržovat prostřednictvím přepínání oddílů. Vzhledem k tomu, že data v databázi SQL Data Warehouse je již distribuován, příliš mnoho oddílů může zpomalit výkon dotazu. Další informace naleznete v [tématu Partitioning guidance](sql-data-warehouse-tables-partition.md).  Při přepínání oddílů do oddílů tabulky, které nejsou prázdné, zvažte použití možnosti TRUNCATE_TARGET v příkazu [ALTER TABLE,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pokud mají být stávající data zkrácena. Níže uvedený kód přepne v transformovaných denních datech do SalesFact přepsání všech existujících dat.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Indexy Columnstore

Ve výchozím nastavení fond SQL ukládá tabulku jako clusterovaný index columnstore. Tato forma úložiště dat dosahuje vysoké komprese dat a výkonu dotazů na velkých tabulkách.  

Clustered columnstore index je obvykle nejlepší volbou, ale v některých případech seskupený index nebo haldy je odpovídající strukturu úložiště.  

> [!TIP]
> Tabulka haldy může být zvláště užitečná pro načítání přechodných dat, jako je například pracovní tabulka, která je transformována do konečné tabulky.

Seznam funkcí columnstore najdete v tématu [Co je nového pro indexy columnstore](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Chcete-li zlepšit výkon indexu columnstore, naleznete [v tématu Maximalizace kvality skupiny řádků pro indexy columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statistika

Optimalizátor dotazu používá statistiku na úrovni sloupců při vytváření plánu pro provádění dotazu.

Chcete-li zlepšit výkon dotazu, je důležité mít statistiky o jednotlivých sloupcích, zejména ve sloupcích používaných v připojování dotazů. [Vytváření statistik](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) probíhá automaticky.  

K aktualizaci statistiky nedojde automaticky. Aktualizovat statistiky po přidání nebo změně významného počtu řádků. Například aktualizovat statistiky po zatížení. Další informace naleznete v [pokynech statistiky](sql-data-warehouse-tables-statistics.md).

## <a name="primary-key-and-unique-key"></a>Primární klíč a jedinečný klíč

Primární klíč je podporován pouze v případě, že jsou použity neseskupené a nevynucené.  Jedinečné omezení je podporováno pouze s není vynuceno se používá.  Zkontrolujte [omezení tabulky fondu SQL](sql-data-warehouse-table-constraints.md).

## <a name="commands-for-creating-tables"></a>Příkazy pro vytváření tabulek

Tabulku můžete vytvořit jako novou prázdnou tabulku. Můžete také vytvořit a naplnit tabulku s výsledky příkazu select. Následují příkazy T-SQL pro vytvoření tabulky.

| Příkaz T-SQL | Popis |
|:----------------|:------------|
| [VYTVOŘIT TABULKU](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Vytvoří prázdnou tabulku definováním všech sloupců a možností tabulky. |
| [VYTVOŘIT EXTERNÍ TABULKU](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Vytvoří externí tabulku. Definice tabulky je uložena ve fondu SQL. Data tabulky se ukládají v úložišti objektů blob Azure nebo v Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Naplní novou tabulku výsledky příkazu select. Sloupce tabulky a datové typy jsou založeny na výsledcích příkazu select. Chcete-li importovat data, tento příkaz můžete vybrat z externí tabulky. |
| [VYTVOŘIT EXTERNÍ TABULKU JAKO VÝBĚR](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Vytvoří novou externí tabulku exportem výsledků příkazu select do externího umístění.  Umístění je úložiště objektů blob Azure nebo Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-sql-pool"></a>Zarovnání zdrojových dat s fondem SQL

Tabulky fondu SQL jsou naplněny načítáním dat z jiného zdroje dat. Chcete-li provést úspěšné načtení, počet a datové typy sloupců ve zdrojových datech musí zarovnat s definicí tabulky ve fondu SQL. Získání data zarovnat může být nejtěžší část navrhování tabulek.

Pokud data pocházejí z více úložišť dat, načtete data do fondu SQL a uložíte je do integrační tabulky. Jakmile jsou data v tabulce integrace, můžete použít výkon fondu SQL k provádění transformačních operací. Jakmile jsou data připravena, můžete je vložit do výrobních tabulek.

## <a name="unsupported-table-features"></a>Nepodporované funkce tabulky

Fond SQL podporuje mnoho, ale ne všechny funkce tabulky nabízené jinými databázemi.  V následujícím seznamu jsou uvedeny některé funkce tabulky, které nejsou ve fondu SQL podporovány:

- Cizí klíč, Kontrola [omezení tabulky](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Vypočítané sloupce](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Indexovaná zobrazení](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Řídké sloupce](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- Náhradní klíče. Implementujte s [identitou](sql-data-warehouse-tables-identity.md).
- [Synonyma](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Aktivační události](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Jedinečné indexy](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Uživatelem definované typy](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Dotazy na velikost tabulky

Jeden jednoduchý způsob, jak identifikovat prostor a řádky spotřebované tabulka v každém z 60 rozdělení, je použití [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Použití příkazů DBCC však může být docela omezující.  Zobrazení dynamické správy (DMV) zobrazují více podrobností než příkazy DBCC. Začněte vytvořením tohoto zobrazení:

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>Souhrn místa v tabulce

Tento dotaz vrátí řádky a prostor podle tabulky.  Umožňuje zobrazit, které tabulky jsou největší tabulky a zda jsou kruhové dotazování, replikovány nebo hash -distributed.  U tabulek distribuovaných hash dotaz zobrazuje sloupec distribuce.  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Místo tabulky podle typu distribuce

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Místo tabulky podle typu indexu

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Souhrn distribučního prostoru

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Další kroky

Po vytvoření tabulek pro fond SQL je dalším krokem načtení dat do tabulky.  Kurz načítání najdete v [tématu Načítání dat do fondu SQL](load-data-wideworldimportersdw.md).
