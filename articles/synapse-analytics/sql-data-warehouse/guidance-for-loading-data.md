---
title: Osvědčené postupy načítání dat pro fond SYNAPse SQL
description: Doporučení a optimalizace výkonu pro načítání dat pomocí fondu Synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e170a789727fb0de36705895245cc638d30ee3d7
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745512"
---
# <a name="best-practices-for-loading-data-using-synapse-sql-pool"></a>Doporučené postupy pro načítání dat pomocí fondu Synapse SQL

V tomto článku se dozvíte doporučení a optimalizace výkonu pro načítání dat pomocí fondu SQL.

## <a name="preparing-data-in-azure-storage"></a>Příprava dat v Azure Storage

Chcete-li minimalizovat latenci, colocate vrstvy úložiště a fondu SQL.

Při exportu dat do formátu souboru ORC může dojít k chybám s nedostatkem paměti Java, pokud se zde nacházejí velké textové sloupce. Toto omezení můžete obejít tím, že importujete jen podmnožinu sloupců.

PolyBase nelze načíst řádky, které mají více než 1 000 000 bajtů dat. Když vkládáte data do textových souborů v úložišti Azure Blob nebo ve službě Azure Data Lake Store, musí tyto soubory obsahovat méně než 1 000 000 bajtů dat. Toto omezení platí bez ohledu na schéma tabulky.

Všechny formáty souborů mají jiné výkonové charakteristiky. Pokud chcete docílit nejrychlejšího načtení, použijte komprimované textové soubory s oddělovači. Rozdíl mezi výkonem kódování UTF-8 a UTF-16 je minimální.

Velké komprimované soubory rozdělte do menších komprimovaných souborů.

## <a name="running-loads-with-enough-compute"></a>Dostatečné výpočetní prostředky pro načítání dat

Největší rychlosti při načítání dosáhnete, když budete spouštět vždy jen jednu úlohu načtení dat. Pokud to není možné, spusťte minimální počet zatížení současně. Pokud očekáváte velké načítání úlohy, zvažte škálování fondu SQL před zatížením.

Pokud chcete spouštět načítání s odpovídajícími výpočetními prostředky, vytvořte uživatele načítání vyhrazené pro spouštění načítání. Přiřaďte každého uživatele načítání ke konkrétní třídě prostředků nebo skupině pracovních vytížení. Chcete-li spustit zatížení, přihlaste se jako jeden z uživatelů načítání a spusťte zatížení. Načítání se spustí s využitím třídy prostředků tohoto uživatele.  

> [!NOTE]
> Tato metoda je jednodušší než se pokoušet o změnu třídy prostředků uživatele podle aktuálních potřeb třídy prostředků.

### <a name="example-of-creating-a-loading-user"></a>Příklad vytvoření uživatele načítání

Tento příklad vytvoří uživatele načítání pro třídu prostředků staticrc20. Prvním krokem je **připojení k předloze** a vytvoření přihlášení.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Připojte se k fondu SQL a vytvořte uživatele. Následující kód předpokládá, že jste připojeni k databázi s názvem mySampleDataWarehouse. Ukazuje, jak vytvořit uživatele s názvem LoaderRC20 a dává uživatelskému ovládacímu prvku oprávnění k databázi. Potom přidá uživatele jako člen role databáze staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Chcete-li spustit zatížení s prostředky pro třídy prostředků staticRC20, přihlaste se jako LoaderRC20 a spusťte zatížení.

Spouštějte načítání v rámci statických, a ne dynamických, tříd prostředků. Použití tříd statických prostředků zaručuje stejné prostředky bez ohledu na [jednotky datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md). Pokud použijete dynamickou třídu prostředků, budou se prostředky lišit v závislosti na vaší úrovni služby.

V případě dynamických tříd znamená nižší úroveň služby, že pro vašeho uživatele načítání pravděpodobně musíte použít větší třídu prostředků.

## <a name="allowing-multiple-users-to-load"></a>Povolení načítání více uživatelům

Často je potřeba mít více uživatelů načíst data do fondu SQL. Načítání pomocí [příkazu CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) vyžaduje oprávnění control databáze.  Oprávnění CONTROL poskytuje přístup pro řízení ke všem schématům.

Pravděpodobně ale nebudete chtít, aby všichni uživatelé, kteří načítají data, měli oprávnění CONTROL pro přístup ke všem schématům. K omezení oprávnění slouží příkaz DENY CONTROL.

Představte si například schémata databáze schema_A pro oddělení A a schema_B pro oddělení B. Uživatelé databáze user_A a user_B budou uživateli pro načítání PolyBase v oddělení A, respektive oddělení B. Oba uživatelé mají k databázi udělená oprávnění CONTROL. Autoři schémat A a B nyní svá schémata uzamknou pomocí příkazu DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A a user_B jsou nyní uzamčeny ze schématu druhého oddělení.

## <a name="loading-to-a-staging-table"></a>Načítání do pracovní tabulky

Chcete-li dosáhnout nejrychlejší rychlostnačítání pro přesun dat do tabulky fondu SQL, načtěte data do pracovní tabulky.  Pracovní tabulku definujte jako haldu a jako způsob distribuce použijte kruhové dotazování (round robin).

Zvažte, že načítání je obvykle dvoustupňový proces, ve kterém nejprve načtete pracovní tabulku a potom vložíte data do produkční tabulky fondu SQL. Pokud provozní tabulka používá k distribuci algoritmus hash, může být celková doba načtení a vložení dat kratší, než když k definici pracovní tabulky použijete distribuci hash.

Načítání do pracovní tabulky trvá déle, ale druhý krok, který spočívá ve vkládání řádků do provozní tabulky, nepřesouvá data prostřednictvím distribuce.

## <a name="loading-to-a-columnstore-index"></a>Načítání do indexu columnstore

Indexy columnstore vyžadují hodně paměti, aby mohly komprimovat data do vysoce kvalitních skupin řádků. Kvůli zajištění co nejlepší účinnosti komprese a indexování musí index columnstore do každé skupiny řádků zkomprimovat maximální počet 1 048 576 řádků.

V případě nedostatku paměti nemusí index columnstore dosahovat maximální míry komprese. Tento scénář, podle pořadí efekty výkonu dotazu. Podrobné informace najdete v tématu [Optimalizace paměti pro columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Pokud chcete zajistit, aby měl nahrávající uživatel dostatek paměti pro dosažení maximální míry komprese, použijte uživatele načítání, kteří jsou členy střední nebo velké třídy prostředků.
- Načtěte dostatek dat pro úplně naplnění nových skupin řádků. Při hromadném načítání dat se každých 1 048 576 řádků zkomprimuje přímo do indexu columnstore jako kompletní skupina řádků. Při načítání méně než 102 400 řádků se řádky odesílají do tabulky deltastore, kde se řádky uchovávají v indexu B-stromu.

> [!NOTE]
> Pokud načtete příliš málo řádků, mohou všechny trasy do deltastore a není získat komprimované okamžitě do formátu columnstore.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Zvětšení velikosti dávky při použití rozhraní SqLBulkCopy API nebo bcp

Načítání s PolyBase bude poskytovat nejvyšší propustnost s fondem SQL. Pokud nemůžete použít PolyBase k načtení a musí používat [SqLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) nebo [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), měli byste zvážit zvýšení velikosti dávky pro lepší propustnost.

> [!TIP]
> Velikost dávky mezi řádky 100 K až 1M je doporučeným základem pro určení optimální kapacity velikosti dávky.

## <a name="handling-loading-failures"></a>Zpracování chyb načítání

Načtení s použitím externí tabulky může selhat s chybou *Query aborted-- the maximum reject threshold was reached while reading from an external source* (Dotaz byl přerušen – při čtení z externího zdroje byla dosažena maximální prahová hodnota pro odmítnutí). Tato zpráva znamená, že vaše externí data obsahují nezapsané záznamy.

Datový záznam se považuje za nečistý, pokud splňuje některou z následujících podmínek:

- Datové typy a počet sloupců neodpovídají definicím sloupců externí tabulky.
- Data neodpovídají zadanému formátu externího souboru.

Pokud chcete nezapsané záznamy opravit, ujistěte se, že jsou definice formátů externí tabulky a externího souboru správné a že externí data těmto definicím odpovídají.

Pokud je podmnožina externích datových záznamů znečištěná, můžete tyto záznamy pro dotazy odmítnout pomocí možností odmítnutí v části [CREATE EXTERNAL TABLE (Transact-SQL).](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="inserting-data-into-a-production-table"></a>Vložení dat do provozní tabulky

Při jednorázovém načtení malé tabulky [příkazem INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) nebo i při pravidelně se opakujícím načítání funkcí look-up pravděpodobně vystačíte s následujícím příkazem: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Vkládání jednotlivých záznamů ale není tak účinné jako hromadné načtení.

Pokud máte za den tisíce nebo více samostatných vložení, vytvořte z nich dávku, abyste je mohli načíst hromadně.  Vyvíjejte své procesy tak, aby samostatná vkládání připojovaly do souboru, a pak vytvořte další proces, který tento soubor bude pravidelně načítat.

## <a name="creating-statistics-after-the-load"></a>Vytvoření statistiky po načtení

Pro zlepšení výkonu dotazů je důležité vytvořit statistiku pro všechny sloupce všech tabulek po prvním načtení, nebo když v datech dojde k zásadnějším změnám. Vytváření statistik lze provést ručně nebo můžete povolit [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

Podrobné vysvětlení statistiky najdete v tématu [Statistika](sql-data-warehouse-tables-statistics.md). Následující příklad ukazuje, jak ručně vytvořit statistiku v pěti sloupcích tabulky Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Obměna klíčů úložiště

Osvědčeným postupem zabezpečení je pravidelně měnit přístupový klíč k úložišti objektů blob. Ke svému účtu úložiště objektů Blob máte dva klíče úložiště, abyste je mohli střídat.

Obměna klíčů účtu služby Azure Storage:

Pro každý účet úložiště, jehož klíč se změnil, vydejte příkaz [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Příklad:

Původní klíč je vytvořený.

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Proveďte otočení klíče 1 do klíče 2.

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

V příslušných externích zdrojích dat se nevyžadují žádné další změny.

## <a name="next-steps"></a>Další kroky

- Další informace o PolyBase a návrhu procesu ELT (extrakce, načítání a transformace) najdete v tématu [Návrh ELT pro službu SQL Data Warehouse](design-elt-data-loading.md).
- Kurz načítání najdete v tématu [Použití PolyBase k načítání dat z úložiště objektů blob v Azure do služby Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).
- Informace o monitorování datové zátěže najdete v tématu [Monitorování úlohy pomocí zobrazení dynamické správy](sql-data-warehouse-manage-monitor.md).
