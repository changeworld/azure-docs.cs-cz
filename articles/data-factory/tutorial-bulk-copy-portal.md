---
title: Hromadné kopírování dat pomocí portálu Azure Portal
description: Naučte se používat Azure Data Factory a aktivitu kopírování k hromadnému kopírování dat ze zdrojového úložiště dat do cílového úložiště dat.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/27/2020
ms.openlocfilehash: 5108335c0b5d0a51559653d51bfe6154237731f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78162532"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Hromadné kopírování několika tabulek pomocí Azure Data Factory

Tento kurz ukazuje **kopírování počtu tabulek z Azure SQL Database do Azure Synapse Analytics (dříve SQL DW).** Stejný vzor můžete využít i u dalších scénářů kopírování. Například kopírování tabulek z SQL Server/Oracle do Azure SQL Database/Azure Synapse Analytics (dříve SQL DW) /Azure Blob, kopírování různých cest z blob do tabulky Azure SQL Database.

> [!NOTE]
> - Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

Tento kurz zahrnuje následující základní kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte Azure SQL Database, Azure Synapse Analytics (dříve SQL DW) a propojené služby Azure Storage.
> * Vytvořte datové sady Azure SQL Database a Azure Synapse Analytics (dříve SQL DW).
> * Vytvoření kanálu pro vyhledání tabulek ke zkopírování a dalšího kanálu pro provedení vlastní operace kopírování 
> * Zahajte spuštění kanálu.
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá Azure Portal. Další informace o vytvoření datové továrny pomocí jiných nástrojů nebo sad SDK najdete v tématu [Šablony Rychlý start](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup
V tomto scénáři máte několik tabulek v Azure SQL Database, které chcete zkopírovat do Azure Synapse Analytics (dříve SQL DW). Tady je logická posloupnost kroků tohoto pracovního postupu, které se provádějí v kanálech:

![Pracovní postup](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* První kanál vyhledá seznam tabulek, které je potřeba zkopírovat do úložišť dat jímky.  Další možností je udržovat tabulku metadat se seznamem všech tabulek, které je potřeba zkopírovat do úložišť dat jímky. Kanál potom aktivuje jiný kanál, který postupně prochází všechny tabulky v databázi a provádí operaci kopírování dat.
* Tento druhý kanál provádí vlastní kopírování. Jako parametr používá seznam tabulek. Pro každou tabulku v seznamu zkopírujte konkrétní tabulku v Azure SQL Database do odpovídající tabulky v Azure Synapse Analytics (dříve SQL DW) pomocí [fázované kopie prostřednictvím úložiště objektů Blob a PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) pro nejlepší výkon. V tomto příkladu první kanál předá seznam tabulek jako hodnotu parametru. 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky
* **Účet Azure Storage**. Účet Azure Storage se v operaci hromadného kopírování používá jako pracovní úložiště objektů blob. 
* **Azure SQL Database**. Tato databáze obsahuje zdrojová data. 
* **Azure Synapse Analytics (dříve SQL DW)**. Tento datový sklad obsahuje data zkopírovaná z SQL Database. 

### <a name="prepare-sql-database-and-azure-synapse-analytics-formerly-sql-dw"></a>Příprava databáze SQL a Azure Synapse Analytics (dříve SQL DW)

**Příprava zdrojové databáze Azure SQL Database**:

Podle postupu v článku [Vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md) vytvořte Azure SQL Database s ukázkovými daty Adventure Works LT. Tento kurz zkopíruje všechny tabulky z této ukázkové databáze do Azure Synapse Analytics (dříve SQL DW).

**Příprava jímky Azure Synapse Analytics (dříve SQL DW)**:

1. Pokud nemáte Azure Synapse Analytics (dříve SQL DW), najdete v článku [Vytvořit SQL datový sklad](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) pro kroky k jeho vytvoření.

1. Vytvořte odpovídající schémata tabulek v Azure Synapse Analytics (dříve SQL DW). K migraci/kopírování dat v pozdějším kroku můžete použít Azure Data Factory.

## <a name="azure-services-to-access-sql-server"></a>Služby Azure pro přístup k SQL serveru

Pro SQL Database a Azure Synapse Analytics (dříve SQL DW) povolte službám Azure přístup k SERVERU SQL. Ujistěte se, že **povolit služby Azure a prostředky pro přístup k tomuto** nastavení serveru je **zapnuta** pro váš server Azure SQL. Toto nastavení umožňuje službě Data Factory číst data z databáze Azure SQL a zapisovat data do azure synapse analytics (dříve SQL DW). 

Chcete-li toto nastavení ověřit a zapnout, přejděte na server Azure SQL server > brány firewall > pro zabezpečení a virtuální sítě > nastavit **možnost Povolit službám a prostředkům Azure pro přístup k tomuto serveru** **na ZAPNUTO**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. Přejděte na [portál Azure](https://portal.azure.com). 
1. Na levé straně nabídky portálu Azure vyberte Vytvořit**datovou továrnu****pro analýzu** >  **prostředků** > . 
   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na stránce **Nová továrna dat** zadejte **název ADFTutorialBulkCopyDF** **.** 
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba pole názvu, změňte název datové továrny (například na vaše_jméno_ADFTutorialBulkCopyDF). Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
1. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
1. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit nový**a zadejte název skupiny prostředků.   
         
     Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
1. Jako **verzi** vyberte **V2**.
1. Vyberte **umístění** pro objekt pro vytváření dat. Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:**[Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.
1. Klikněte na **Vytvořit**.
1. Po dokončení vytváření vyberte **Přejít na prostředek** a přejděte na stránku **Data Factory.** 
   
1. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě aplikaci uživatelského rozhraní služby Data Factory.
1. Na stránce **Začínáme** přepněte na kartu **Autor** v levém panelu, jak je znázorněno na následujícím obrázku:

     ![Stránka Začínáme](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb
Vytvoříte propojené služby, které propojí vaše úložiště dat a výpočetní prostředí s datovou továrnou. Propojená služba obsahuje informace o připojení, které služba Data Factory používá pro připojení k úložišti dat za běhu. 

V tomto kurzu propojíte azure SQL database, Azure Synapse Analytics (dříve SQL DW) a úložiště dat azure blob storage s vaší továrně dat. Azure SQL Database je zdrojové úložiště dat. Azure Synapse Analytics (dříve SQL DW) je úložiště dat jímky a cíle. Azure Blob Storage je fáze data před načtením dat do Azure Synapse Analytics (dříve SQL DW) pomocí PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database pro zdroj
V tomto kroku vytvoříte propojenou službu, která propojí vaši databázi Azure SQL s datovou továrnou. 

1. V dolní části okna klikněte na **Připojení** a na panelu nástrojů klepněte na **tlačítko + Nový** ( Tlačítko**Připojení** se nachází v dolní části levého sloupce v části **Zdroje výroby**). 

1. V okně **Nová propojená služba** vyberte **Azure SQL Database** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba (Azure SQL Database)** proveďte následující kroky: 

    a. Jako **Název** zadejte **AzureSqlDatabaseLinkedService**.
    
    b. Jako **Název serveru** vyberte váš server SQL Azure.
    
    c. Jako **Název databáze** vyberte vaši databázi Azure SQL. 
    
    d. Zadejte **jméno uživatele** pro připojení k databázi Azure SQL. 
    
    e. Zadejte **heslo** pro tohoto uživatele. 

    f. Pokud chcete otestovat připojení k databázi Azure SQL s použitím zadaných informací, klikněte na **Test připojení**.
  
    g. Kliknutím na **Vytvořit** uložte propojenou službu.


### <a name="create-the-sink-azure-synapse-analytics-formerly-sql-dw-linked-service"></a>Vytvoření propojené služby Azure Synapse Analytics (dříve SQL DW)

1. Na kartě **Připojení** znovu klikněte na **+ Nové** na panelu nástrojů. 
1. V okně **Nová propojená služba** vyberte **Azure Synapse Analytics (dříve SQL DW)** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba (Azure Synapse Analytics (dříve SQL DW))** proveďte následující kroky: 
   
    a. Jako **Název** zadejte **AzureSqlDWLinkedService**.
     
    b. Jako **Název serveru** vyberte váš server SQL Azure.
     
    c. Jako **Název databáze** vyberte vaši databázi Azure SQL. 
     
    d. Zadejte **Uživatelské jméno** pro připojení k databázi Azure SQL. 
     
    e. Zadejte **heslo** pro uživatele. 
     
    f. Pokud chcete otestovat připojení k databázi Azure SQL s použitím zadaných informací, klikněte na **Test připojení**.
     
    g. Klikněte na **Vytvořit**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Vytvoření pracovní propojené služby Azure Storage
V tomto kurzu použijete Azure Blob Storage jako dočasné pracovní oblast, abyste zajistili lepší výkon kopírování pro funkci PolyBase.

1. Na kartě **Připojení** znovu klikněte na **+ Nové** na panelu nástrojů. 
1. V okně **Nová propojená služba** vyberte **Azure Blob Storage** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba (Azure Blob Storage)** proveďte následující kroky: 

    a. Jako **Název** zadejte **AzureStorageLinkedService**.                                                 
    b. Jako **Název účtu úložiště** vyberte svůj **účet služby Azure Storage**.
    
    c. Klikněte na **Vytvořit**.


## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kurzu vytvoříte zdrojovou datovou sadu a datovou sadu jímky, které určují umístění pro uložení dat. 

Vstupní datová sada **AzureSqlDatabaseDataset** odkazuje na službu **AzureSqlDatabaseLinkedService**. Propojená služba určuje připojovací řetězec pro připojení k databázi. Datová sada určuje název databáze a tabulky obsahující zdrojová data. 

Výstupní datová sada **AzureSqlDWDataset** odkazuje na službu **AzureSqlDWLinkedService**. Propojená služba určuje připojovací řetězec pro připojení k Azure Synapse Analytics (dříve SQL DW). Datová sada určuje databázi a tabulku, do kterých se data zkopírují. 

V tomto kurzu nejsou zdrojová a cílová tabulka SQL pevně zakódované v definicích datových sad. Místo toho aktivita ForEach předává název tabulky do aktivity kopírování za běhu. 

### <a name="create-a-dataset-for-source-sql-database"></a>Vytvoření datové sady pro zdrojovou databázi SQL Database

1. V levém podokně klepněte na **+ (plus)** a potom klepněte na **položku Datová sada**. 

    ![Nabídka Nová datová sada](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. V okně **Nová datová sada** vyberte **Azure SQL Database**a klikněte na **Pokračovat**. 
    
1. V okně **Nastavit vlastnosti** v části **Name**zadejte **AzureSqlDatabaseDataset**. V části **Propojená služba**vyberte **AzureSqlDatabaseLinkedService**. Pak klikněte na **OK**.

1. Přepněte na kartu **Připojení,** vyberte libovolnou tabulku **tabulky**. Tato tabulka je fiktivní. Při vytváření kanálu zadáte dotaz na zdrojovou datovou sadu. Tento dotaz se použije k extrakci dat z databáze Azure SQL. Případně můžete kliknout na **zaškrtávací** políčko Upravit a jako název tabulky zadat **dbo.dummyName.** 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics-formerly-sql-dw"></a>Vytvoření datové sady pro jímky Azure Synapse Analytics (dříve SQL DW)

1. Klikněte na symbol **+ (plus)** v levém podokně a pak klikněte na **Datová sada**. 
1. V okně **Nová datová sada** vyberte **Azure Synapse Analytics (dříve SQL DW)** a klikněte na **Pokračovat**.
1. V okně **Set vlastnosti,** v části **Název**, zadejte **AzureSqlDWDataset**. V části **Propojené služby**vyberte **AzureSqlDWLinkedService**. Pak klikněte na **OK**.
1. Přepněte na kartu **Parametry**, klikněte na **+Nové** a jako název parametru zadejte **DWTableName**. Pokud zkopírujete nebo vložíte tento název ze stránky, ujistěte se, že na konci **DWTableName**není žádný **koncový znak mezery** .
1. Přepněte na kartu **Připojení**. 

    a. V **tabulce**zaškrtněte volbu **Upravit.** Zadejte **dbo** do prvního vstupního pole s názvem tabulky. A pak vyberte do druhého vstupního pole a klikněte na odkaz **Přidat dynamický obsah** níže. 

    ![Název tabulky připojení datové sady](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    b. Na stránce **Přidat dynamický obsah** klikněte na **položku DWTAbleName** v části `@dataset().DWTableName` **Parametry**, které automaticky naplní textové pole horního výrazu a potom klepne na tlačítko **Dokončit**. Vlastnost **tableName** datové sady je nastavená na hodnotu předávanou jako argument parametru **DWTableName**. Aktivita ForEach iteruje seznam tabulek a jednu po druhé je předává aktivitě kopírování. 

    ![Tvůrce parametru datové sady](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Vytvoření kanálů
V tomto kurzy vytvoříte dva kanály: **IterateAndCopySQLTables** a **GetTableListAndTriggerCopyData**. 

Kanál **GetTableListAndTriggerCopyData** provádí dvě akce:

* Vyhledá systémové tabulky Azure SQL Database a získá seznam tabulek, které se mají zkopírovat.
* Aktivuje kanál **IterateAndCopySQLTables**, který provede vlastní kopírování dat.

**IterateAndCopySQLTables** kanálu trvá seznam tabulek jako parametr. Pro každou tabulku v seznamu zkopíruje data z tabulky v Azure SQL Database do Azure Synapse Analytics (dříve SQL DW) pomocí fázované kopie a PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Vytvoření kanálu IterateAndCopySQLTables

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Kanál**.

    ![Nabídka Nový kanál](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Na kartě **Obecné** zadejte název **IterateAndCopySQLTables**. 

1. Přepněte na kartu **Parametry** a proveďte následující akce: 

    a. Klikněte na **+ Nový**. 
    
    b. Zadejte **tableList** pro parametr **Název**.
    
    c. Jako **Typ** vyberte **Pole**.

1. Na panelu nástrojů **Aktivity** rozbalte **Iterace a podmínky** a přetáhněte aktivitu **ForEach** na plochu návrháře kanálu. Na panelu nástrojů **Aktivity** můžete aktivity také vyhledávat. 

    a. Na kartě **Obecné** dole zadejte **IterateSQLTables** jako **Název**. 

    b. Přepněte na kartu **Nastavení,** klikněte na vstupní pole **položky**a potom klikněte na odkaz **Přidat dynamický obsah** níže. 

    c. Na stránce **Přidat dynamický obsah** sbalte části Systémové **proměnné** a **funkce** a klikněte na **tabulku Seznam** v části **Parametry**, která automaticky naplní textové pole horního výrazu jako `@pipeline().parameter.tableList`. Klikněte na **Dokončit**. 

    ![Tvůrce parametru ForEach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Přepněte na kartu **Aktivity,** kliknutím na **ikonu tužky** přidejte podřízenou aktivitu do aktivity **ForEach.**
    ![Tvůrce aktivit Foreach](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. V panelu nástrojů **Aktivity** rozbalte **možnost Přesunout & přenos**a přetáhněte položku Kopírovat **datovou** aktivitu do povrchu návrháře kanálu. Všimněte si nabídky navigace s popisem cesty v horní části. **IterateAndCopySQLTable** je název kanálu a **IterateSQLTables** je ForEach název aktivity. Návrhář je v oboru aktivity. Chcete-li přepnout zpět do editoru kanálu z editoru ForEach, můžete klepnout na odkaz v nabídce s popisem cesty. 

    ![Kopírování v aktivitě ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Přepněte na kartu **Zdroj** a proveďte následující kroky:

    1. Jako **Zdrojová datová sada** vyberte **AzureSqlDatabaseDataset**. 
    1. Vyberte **možnost Dotaz** pro **použít dotaz**. 
    1. Klikněte na vstupní pole **Dotaz** -> vyberte dole **Přidat dynamický obsah** -> zadejte následující výraz jako **Dotaz** -> vyberte **Dokončit**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Přepněte na kartu **Jímka** a proveďte následující kroky: 

    1. Jako **Datová sada jímky** vyberte **AzureSqlDWDataset**.
    1. Klepněte na vstupní pole parametru VALUE of DWTableName -> `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` vyberte následující možnost Přidat dynamický **obsah,** zadejte výraz jako skript, -> vyberte **Dokončit**.
    1. V části Copy metoda vyberte **možnost PolyBase**. 
    1. Zrušte zaškrtnutí **možnosti Použít výchozí** typ. 
    1. Klikněte na vstupní pole **Skript před kopírováním**, vyberte dole **Přidat dynamický obsah**, zadejte následující výraz jako skript a vyberte **Dokončit**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Nastavení jímky kopírování](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Přepněte na kartu **Nastavení** a proveďte následující kroky: 

    1. Zaškrtněte políčko **Povolit pracovní dobu**.
    1. Jako **Propojená služba účtu úložiště** zadejte **AzureStorageLinkedService**.

1. Pokud chcete ověřit nastavení kanálu, klikněte na **Ověřit** na horním panelu nástrojů kanálu. Ujistěte se, že není žádná chyba ověření. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Vytvoření kanálu GetTableListAndTriggerCopyData

Tento kanál provádí dvě akce:

* Vyhledá systémové tabulky Azure SQL Database a získá seznam tabulek, které se mají zkopírovat.
* Aktivuje kanál IterateAndCopySQLTables, který provede vlastní kopírování dat.

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Kanál**.
1. Na kartě **Obecné** změňte název kanálu na **GetTableListAndTriggerCopyData**. 

1. V panelu nástrojů **Aktivity** rozbalte **položku Obecné**a přetáhněte aktivitu **vyhledávání** na povrch návrháře kanálu a proveďte následující kroky:

    1. Jako **Název** zadejte **LookupTableList**. 
    1. Jako **Popis** zadejte **Načtení seznamu tabulek z databáze Azure SQL**.

1. Přepněte na kartu **Nastavení** a proveďte následující kroky:

    1. Jako **Zdrojová datová sada** vyberte **AzureSqlDatabaseDataset**. 
    1. Vyberte **dotaz** **pro použití dotazu**. 
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Zrušte zaškrtnutí pole **Pouze první řádek**.

        ![Aktivita vyhledávání – stránka Nastavení](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Přetáhněte **aktivitu Spustit kanál** z panelu nástrojů Aktivity na povrch návrháře kanálu a nastavte název **triggercopy**.

1. Chcete-li **připojit** aktivitu **vyhledávání** k aktivitě **Spustit kanál,** přetáhněte **zelené pole** připojené k aktivitě vyhledávání vlevo od aktivity Spustit kanál.

    ![Projení aktivit vyhledávání a spuštění kanálu](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Přepněte na kartu **Nastavení** aktivity **Spustit kanál** a proveďte následující kroky: 

    1. Jako **Vyvolaný kanál** vyberte **IterateAndCopySQLTables**. 
    1. Rozbalte oddíl **Upřesnit** a zrušte zaškrtnutí políčka **Čekání po dokončení**.
    1. Klikněte na **+ Nový** v části **Parametry**. 
    1. Zadejte **seznam tabulek** pro parametr **Název**.
    1. Klikněte na vstupní pole HODNOTY -> vyberte dole **Přidat dynamický obsah** -> zadejte `@activity('LookupTableList').output.value` jako hodnotu názvu tabulky -> vyberte **Dokončit**. Nastavujete seznam výsledků z aktivity vyhledávání jako vstup do druhého kanálu. Seznam výsledků obsahuje seznam tabulek, jejichž data se musí zkopírovat do cíle. 

        ![Aktivita spuštění kanálu – stránka Nastavení](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Pokud chcete kanál ověřit, klikněte na **Ověřit** na panelu nástrojů. Ověřte, že se nezobrazí žádné chyby ověření. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

1. Pokud chcete publikovat entity (datové sady, kanály atd.) do služby Data Factory, klikněte na **Publikovat vše** v horní části okna. Počkejte na úspěšné dokončení publikování. 

## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu

1. Přejděte na kanál **GetTableListAndTriggerCopyData**, klepněte na tlačítko **Přidat aktivační událost** na horním panelu nástrojů kanálu a potom klepněte na tlačítko Spustit **nyní**. 

1. Potvrďte spuštění na stránce **Spuštění kanálu** a pak vyberte **Dokončit**.

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Přepněte na kartu **Refresh** **Monitor.** Pokračujte v aktualizacích seznamu, dokud se nezobrazí stav **Úspěch**. 

1. Chcete-li zobrazit spuštění aktivity přidružená k kanálu **GetTableListAndTriggerCopyData,** klepněte na odkaz názvu kanálu. Pro toto spuštění kanálu by se měla zobrazit dvě spuštění aktivit. 
    ![Spuštění kanálu monitoru](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Chcete-li zobrazit výstup aktivity **vyhledávání,** klikněte na odkaz **Výstup** vedle aktivity ve sloupci **NÁZEV AKTIVITY.** Okno **Výstup** můžete maximalizovat a obnovit. Po kontrole kliknutím na **X** zavřete okno **Výstup**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Pokud chcete přepnout zpět do zobrazení **Potrubní běhy,** klikněte v horní části nabídky s popisem cesty na odkaz **Všechny kanály.** Chcete-li zobrazit spuštění aktivity kanálu, klepněte na tlačítko **IterateAndCopySQLTables** link (ve sloupci **NÁZEV KANÁLU).** Všimněte si, že je jedna **aktivita kopírování** spuštěna pro každou tabulku ve výstupu **aktivity vyhledávání.** 

1. Zkontrolujte, že data byla zkopírována do cílové Azure Synapse Analytics (dříve SQL DW), který jste použili v tomto kurzu. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte Azure SQL Database, Azure Synapse Analytics (dříve SQL DW) a propojené služby Azure Storage.
> * Vytvořte datové sady Azure SQL Database a Azure Synapse Analytics (dříve SQL DW).
> * Vytvoření kanálu pro vyhledání tabulek ke zkopírování a dalšího kanálu pro provedení vlastní operace kopírování 
> * Zahajte spuštění kanálu.
> * Monitorování spuštění aktivit a kanálu

Pokud se chcete dozvědět víc o přírůstkovém kopírování ze zdroje do cíle, přejděte k následujícímu kurzu:
> [!div class="nextstepaction"]
>[Přírůstkové kopírování dat](tutorial-incremental-copy-portal.md)
