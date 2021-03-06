---
title: Přírůstkové kopírování více tabulek pomocí portálu Azure
description: V tomto kurzu vytvoříte kanál Azure Data Factory, který přírůstkově kopíruje rozdílová data z několika tabulek v místní databázi SQL Serveru do databáze Azure SQL.
services: data-factory
ms.author: yexu
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/20/2018
ms.openlocfilehash: 2c89b53d66b93ff38a7cff07b2889faf8eda24ce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75439300"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Přírůstkové načtení dat z více tabulek v SQL Serveru do databáze Azure SQL

V tomto kurzu vytvoříte Azure Data Factory s kanálem, který načítá rozdílová data z několika tabulek v místním SQL Serveru do databáze Azure SQL.    

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Příprava zdrojového a cílového datového úložiště
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Instalace prostředí Integration Runtime 
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření a spuštění kanálu a jeho monitorování
> * Zkontrolujte výsledky.
> * Přidání nebo aktualizace dat ve zdrojových tabulkách
> * Opakované spuštění kanálu a jeho monitorování
> * Kontrola konečných výsledků

## <a name="overview"></a>Přehled
Tady jsou důležité kroky pro vytvoření tohoto řešení: 

1. **Vyberte sloupec meze**.
    
    Ve zdrojovém úložišti dat vyberte pro každou tabulku jeden sloupec, který je možné použít k identifikaci nových nebo aktualizovaných záznamů pro každé spuštění. Data v tomto vybraném sloupci (například čas_poslední_změny nebo ID) se při vytváření nebo aktualizaci řádků obvykle zvyšují. Maximální hodnota v tomto sloupci se používá jako horní mez.

1. **Připravte úložiště dat pro uložení hodnoty meze**.   
    
    V tomto kurzu uložíte hodnotu meze do databáze SQL.

1. **Vytvořte kanál s následujícími aktivitami**: 
    
    a. Vytvořte aktivitu ForEach, která prochází seznam názvů zdrojových tabulek, který je předaný kanálu jako parametr. Pro každou zdrojovou tabulku vyvolá následující aktivity, aby pro tabulku provedl rozdílové načtení.

    b. Vytvořte dvě aktivity vyhledávání. První aktivitu vyhledávání použijte k načtení poslední hodnoty meze. Druhou aktivitu vyhledávání použijte k načtení nové hodnoty meze. Tyto hodnoty meze se předají aktivitě kopírování.

    c. Vytvořte aktivitu kopírování, která ze zdrojového úložiště dat zkopíruje řádky, které ve sloupci horní meze mají hodnotu vyšší, než je stará hodnota meze, a nižší, než je nová hodnota meze. Potom tato rozdílová data zkopíruje ze zdrojového úložiště dat do úložiště Azure Blob Storage jako nový soubor.

    d. Vytvořte aktivitu uložené procedury StoredProcedure, která aktualizuje hodnotu meze pro příští spuštění kanálu. 

    Tady je souhrnný diagram tohoto řešení: 

    ![Přírůstkové načtení dat](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="prerequisites"></a>Požadavky
* **SQL Server**. V tomto kurzu použijete místní databázi SQL Serveru jako zdrojové úložiště dat. 
* **Azure SQL Database**. Použijete databázi SQL jako úložiště dat jímky. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md), kde najdete kroky pro její vytvoření. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Vytvoření zdrojových tabulek v databázi SQL Serveru

1. Otevřete SQL Server Management Studio a připojte se k místní databázi SQL Serveru.

1. V **Průzkumníku serveru** klikněte pravým tlačítkem na databázi a zvolte **Nový dotaz**.

1. Spusťte na databázi následující příkaz SQL, aby se vytvořily tabulky s názvem `customer_table` a `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Vytvoření cílových tabulek v databázi Azure SQL
1. Otevřete aplikaci SQL Server Management Studio a připojte se ke své databázi Azure SQL.

1. V **Průzkumníku serveru** klikněte pravým tlačítkem na databázi a zvolte **Nový dotaz**.

1. Spusťte následující příkaz SQL v databázi `customer_table` `project_table`Azure SQL a vytvořte tabulky s názvem a :  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Vytvoření další tabulky v databázi Azure SQL pro ukládání hodnoty horní meze
1. Spusťte následující příkaz SQL proti databázi `watermarktable` Azure SQL a vytvořte tabulku s názvem pro uložení hodnoty vodoznaku: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Do tabulky mezí vložte hodnoty počátečních mezí pro obě zdrojové tabulky.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Vytvoření uložené procedury v databázi Azure SQL 

Spuštěním následujícího příkazu ve vaší databázi Azure SQL vytvořte uloženou proceduru. Tato uložená procedura aktualizuje hodnotu meze po každém spuštění kanálu. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Vytvoření datových typů a dalších uložených procedur v databázi Azure SQL
Spusťte následující dotaz a vytvořte dvě uložené procedury a dva datové typy v databázi Azure SQL. Slouží ke slučování dat ze zdrojových tabulek do cílových tabulek.

Aby bylo možné cestu snadno začít, my přímo použít tyto uložené procedury předávání delta data v prostřednictvím proměnné tabulky a pak je sloučit do cílového úložiště. Buďte opatrní, že neočekává "velký" počet delta řádků (více než 100) které mají být uloženy v proměnné tabulky.  

Pokud potřebujete sloučit velký počet řádků delta do cílového úložiště, doporučujeme použít aktivitu kopírování ke zkopírování všech dat delta do dočasné "pracovní" tabulky v cílovém úložišti a potom vytvořit vlastní uloženou proceduru bez použití tabulky proměnnou, která je sloučí z "pracovní" tabulky do "finálové" tabulky. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. V levé nabídce vyberte Vytvořit**datovou továrnu****Analytics** >  **:** >  
   
   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na stránce **Nová datová továrna** jako **název** zadejte **ADFMultiIncCopyTutorialDF**. 
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí červený vykřičník s následující chybou, změňte název datové továrny (například na vaše_jméno_ADFIncCopyTutorialDF) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
   `Data factory name "ADFIncCopyTutorialDF" is not available`

4. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
5. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
    - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
    - Vyberte **Vytvořit nový**a zadejte název skupiny prostředků.   
    Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
6. Jako **verzi** vyberte **V2**.
7. Vyberte **umístění** pro objekt pro vytváření dat. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.
8. Klikněte na **Vytvořit**.      
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
   ![Domovská stránka objektu pro vytváření dat](./media/doc-common-process/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Azure Data Factory.

## <a name="create-self-hosted-integration-runtime"></a>Vytvoření místního prostředí Integration Runtime
Vzhledem k tomu, že přesouváte data z úložiště dat v privátní síti (v místním prostředí) do úložiště dat Azure, nainstalujte do svého místního prostředí místní prostředí Integration Runtime (IR). Místní prostředí IR přesouvá data mezi privátní sítí a Azure. 

1. Klikněte na **Připojení** v dolní části levého podokna a v okně **Připojení** přepněte na kartu **Prostředí Integration Runtime**. 

1. Na kartě **Prostředí Integration Runtime** klikněte na **+ Nové**. 

1. V okně **Nastavení prostředí Integration Runtime** vyberte **Provést přesun dat a odeslání aktivit do externích výpočetních prostředků**a klepněte na tlačítko **Pokračovat**. 

1. Vyberte **Možnost Vlastní hostované**a klepněte na tlačítko **Pokračovat**. 
1. Do pole Název zadejte **položku MySelfHostedIR** a klepněte na tlačítko **Vytvořit**. **Name** 

1. Klikněte na text **Kliknutím sem spustíte expresní instalaci pro tento počítač** v části **Možnost 1: Expresní instalace**. 

   ![Kliknutí na odkaz na expresní instalaci](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. V okně **Expresní instalace Integration Runtime (v místním prostředí)** klikněte na **Zavřít**. 

   ![Instalace prostředí Integration Runtime – úspěch](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. V okně **Instalace prostředí Integration Runtime** ve webovém prohlížeči klikněte na **Dokončit**. 

 
1. Zkontrolujte, že se v seznamu prostředí Integration Runtime zobrazuje **MySelfHostedIR**.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V této části vytvoříte propojené služby pro místní databázi SQL Serveru a databázi Azure SQL. 

### <a name="create-the-sql-server-linked-service"></a>Vytvoření propojené služby SQL Serveru
V tomto kroku s datovou továrnou propojíte místní databázi SQL Serveru.

1. V okně **Připojení** přepněte z karty **Prostředí Integration Runtime** na kartu **Propojené služby** a klikněte na **+ Nová**.

1. V okně **Nová propojená služba** vyberte **SQL Server** a klikněte na **Pokračovat**. 

1. V okně **Nová propojená služba** proveďte následující kroky:

    1. Jako **Název** zadejte **SqlServerLinkedService**. 
    1. V části **Připojit prostřednictvím prostředí Integration Runtime** zadejte **MySelfHostedIR**. To je **důležitý** krok. Výchozí prostředí Integration Runtime se nemůže připojit k místnímu úložišti dat. Použijte místní prostředí Integration Runtime, které jste vytvořili dříve. 
    1. Jako **Název serveru** zadejte název vašeho počítače, který obsahuje databázi SQL Serveru.
    1. Jako **Název databáze** zadejte název databáze ve vašem SQL Serveru, která obsahuje zdrojová data. Tabulku jste vytvořili a do této databáze jste vložili data jako součást požadavků. 
    1. Jako **Typ ověřování** vyberte **typ ověřování**, který chcete použít pro připojení k databázi. 
    1. Jako **Uživatelské jméno** zadejte jméno uživatele, který má přístup k této databázi SQL Serveru. Pokud v názvu uživatelského účtu nebo serveru potřebujete použít znak lomítko (`\`), použijte řídicí znak (`\`). Příklad: `mydomain\\myuser`.
    1. Jako **Heslo** zadejte **heslo** pro tohoto uživatele. 
    1. Pokud chcete otestovat, jestli se služba Data Factory může připojit k vaší databázi SQL Serveru, klikněte na **Test připojení**. Opravte všechny chyby, dokud připojení nebude úspěšné. 
    1. Chcete-li propojenou službu uložit, klepněte na tlačítko **Dokončit**.

### <a name="create-the-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
V posledním kroku vytvoříte propojenou službu, která propojí vaši zdrojovou databázi SQL Serveru s datovou továrnou. V tomto kroku s datovou továrnou propojíte cílovou databázi Azure SQL nebo databázi Azure SQL jímky. 

1. V okně **Připojení** přepněte z karty **Prostředí Integration Runtime** na kartu **Propojené služby** a klikněte na **+ Nová**.
1. V okně **Nová propojená služba** vyberte **Azure SQL Database** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba** proveďte následující kroky:

    1. Jako **Název** zadejte **AzureSqlDatabaseLinkedService**. 
    1. Jako **Název serveru** vyberte z rozevíracího seznamu název vašeho serveru SQL Azure. 
    1. Jako **Název databáze** vyberte databázi Azure SQL, ve které jste jako součást požadavků vytvořili tabulky customer_table a project_table. 
    1. Jako **Uživatelské jméno** zadejte jméno uživatele, který má přístup k této databázi Azure SQL. 
    1. Jako **Heslo** zadejte **heslo** pro tohoto uživatele. 
    1. Pokud chcete otestovat, jestli se služba Data Factory může připojit k vaší databázi SQL Serveru, klikněte na **Test připojení**. Opravte všechny chyby, dokud připojení nebude úspěšné. 
    1. Chcete-li propojenou službu uložit, klepněte na tlačítko **Dokončit**.

1. Zkontrolujte, že se v seznamu zobrazují dvě propojené služby. 
   
    ![Dvě propojené služby](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které představují zdroj dat, cíl dat a místo pro uložení hodnoty meze.

### <a name="create-a-source-dataset"></a>Vytvoření zdrojové datové sady

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**.

1. V okně **Nová datová sada** vyberte **položku SQL Server**, klepněte na tlačítko **Pokračovat**. 

1. Ve webovém prohlížeči se otevře nová karta, na které můžete datovou sadu konfigurovat. Ve stromovém zobrazení se také zobrazí datová sada. Na kartě **Obecné** v dolní části okna Vlastnosti jako **Název** zadejte **SourceDataset**. 

1. V okně Vlastnosti přepněte na kartu **Připojení** a jako **Propojená služba** vyberte **SqlServerLinkedService**. Tabulku tady nevybíráte. Aktivita kopírování v kanálu používá místo načtení celé tabulky dotaz SQL pro načtení dat.

   ![Zdrojová sada dat – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Vytvoření datové sady jímky
1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**.

1. V okně **Nová datová sada** vyberte **Azure SQL Database**a klikněte na **Pokračovat**. 

1. Ve webovém prohlížeči se otevře nová karta, na které můžete datovou sadu konfigurovat. Ve stromovém zobrazení se také zobrazí datová sada. Na kartě **Obecné** v dolní části okna Vlastnosti jako **Název** zadejte **SinkDataset**.

1. V okně Vlastnosti přepněte na kartu **Parametry** a proveďte následující kroky: 

    1. V části **Vytvořit nebo aktualizovat parametry** klikněte na **Nový**. 
    1. Jako **název** zadejte **SinkTableName** a jako **typ** zadejte **Řetězec**. Tato datová sada jako parametr přijímá **SinkTableName**. Parametr SinkTableName nastavuje kanál dynamicky za běhu. Aktivita ForEach v kanálu prochází seznam názvů tabulek a při každé iteraci předává název tabulky této datové sadě.
   
    ![Datová sada jímky – vlastnosti](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. Přepněte na kartu **Připojení** v okně Vlastnosti a vyberte **AzureSqlDatabaseLinkedService** for **Linked service**. U vlastnosti **Tabulka** klikněte na **Přidat dynamický obsah**.   
    
1. V okně **Přidat dynamický obsah** vyberte v části **Parametry** **položku SinkTableName.** 
 
1. Po **Finish**klepnutí na@datasettlačítko Dokončit se zobrazí " (). SinkTableName" jako název tabulky.

   ![Datová sada jímky – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Vytvoření datové sady pro mez
V tomto kroku vytvoříte datovou sadu pro uložení hodnoty horní meze. 

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Datová sada**.

1. V okně **Nová datová sada** vyberte **Azure SQL Database**a klikněte na **Pokračovat**. 

1. Na kartě **Obecné** v dolní části okna Vlastnosti jako **Název** zadejte **WatermarkDataset**.
1. Přepněte na kartu **Připojení** a proveďte následující kroky: 

    1. Jako **Propojená služba** vyberte **AzureSqlDatabaseLinkedService**.
    1. Jako **Tabulka** vyberte **[dbo].[watermarktable]**.

    ![Datová sada meze – připojení](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Vytvoření kanálu
Tento kanál dostává jako parametr seznam tabulek. Aktivita ForEach prochází seznam názvů tabulek a provádí následující operace: 

1. Použije aktivitu vyhledávání k načtení původní hodnoty meze (počáteční hodnota nebo hodnota použitá v poslední iteraci).

1. Použije aktivitu vyhledávání k načtení nové hodnoty meze (maximální hodnota sloupce mezí ve zdrojové tabulce).

1. Použije aktivitu kopírování ke kopírování dat, která leží mezi těmito dvěma hodnotami mezí, ze zdrojové databáze do cílové databáze.

1. Použije aktivitu uložené procedury StoredProcedure k aktualizaci staré hodnoty meze, která se má použít v prvním kroku další iterace. 

### <a name="create-the-pipeline"></a>Vytvoření kanálu

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Kanál**.

1. Na kartě **Obecné** zadejte **incrementalcopypipeline** pro **název**. 

1. Na kartě **Parametry** postupujte takto: 

    1. Klikněte na **+ Nový**. 
    1. Jano **název parametru** zadejte **tableList**. 
    1. Vyberte **pole** pro **typ**parametru .

1. V sadě nástrojů **Aktivity** rozbalte **Iterace a podmíněné výrazy** a přetáhněte aktivitu **ForEach** na plochu návrháře kanálu. Na kartě **Obecné** v okně **Vlastnosti** jako název zadejte **IterateSQLTables**. 

1. Přepněte na kartu **Nastavení** a jako **Položky** zadejte `@pipeline().parameters.tableList`. Aktivita ForEach prochází seznam tabulek a provádí operaci přírůstkového kopírování. 

    ![Aktivita ForEach – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)

1. Pokud ještě není vybraná, vyberte v kanálu aktivitu **ForEach**. Klikněte na tlačítko **Upravit (ikona tužky)**.

1. V sadě nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte aktivitu **Vyhledávání** na plochu návrháře kanálu. Pak jako **Název** zadejte **LookupOldWaterMarkActivity**.

1. V okně **Vlastnosti** přepněte na kartu **Nastavení** a proveďte následující kroky: 

    1. Jako **Zdrojová datová sada** vyberte **WatermarkDataset**.
    1. Jako **Použít dotaz** vyberte **Dotaz**. 
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![První aktivita vyhledávání – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Z panelu nástrojů **Aktivity** přetáhněte aktivitu **Vyhledávání** a jako **Název** zadejte **LookupNewWaterMarkActivity**.
        
1. Přepněte na kartu **Nastavení**.

    1. Jako **Zdrojová datová sada** vyberte **SourceDataset**. 
    1. Jako **Použít dotaz** vyberte **Dotaz**.
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Druhá aktivita vyhledávání – nastavení](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Z panelu nástrojů **Aktivity** přetáhněte aktivitu **Kopírování** a jako **Název** zadejte **IncrementalCopyActivity**. 

1. Jednu po druhé propojte aktivity **vyhledávání** s aktivitou **kopírování**. Propojte je tak, že začnete přetahovat **zelené** pole připojené k aktivitě **vyhledávání** a přemístíte ho na aktivitu **kopírování**. Jakmile se barva ohraničení aktivity kopírování změní na **modrou**, uvolněte tlačítko myši.

    ![Propojení aktivit vyhledávání s aktivitou kopírování](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. Vyberte v kanálu aktivitu **kopírování**. V okně **Vlastnosti** přepněte na kartu **Zdroj**. 

    1. Jako **Zdrojová datová sada** vyberte **SourceDataset**. 
    1. Jako **Použít dotaz** vyberte **Dotaz**. 
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Aktivita kopírování – nastavení zdroje](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Přepněte na kartu **Jímka** a jako **Datová sada jímky** vyberte **SinkDataset**. 
        
1. Postupujte takto:

    1. Ve **vlastnostech datové sady**zadejte `@{item().TABLE_NAME}`pro parametr **SinkTableName** .
    1. Pro vlastnost Název uložené `@{item().StoredProcedureNameForMergeOperation}` **procedury** zadejte .
    1. Do pole Vlastnost `@{item().TableType}`Typ **tabulky** zadejte .
    1. Do **pole Název parametru typu tabulky**zadejte `@{item().TABLE_NAME}`.

    ![Aktivita kopírování – parametry](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Přetáhněte aktivitu **Uložená procedura** z panelu nástrojů **Aktivity** na plochu návrháře kanálu. Propojte aktivitu **kopírování** s aktivitou **Uložená procedura**. 

1. Vyberte v kanálu aktivitu **Uložená procedura** a na kartě **Obecné** v okně **Vlastnosti** jako **Název** zadejte **StoredProceduretoWriteWatermarkActivity**. 

1. Přepněte na kartu **Účet SQL** a vyberte **AzureSqlDatabaseLinkedService** for **Linked Service**.

    ![Aktivita Uložená procedura – účet SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Přepněte na kartu **Uložená procedura** a proveďte následující kroky:

    1. Jako **Název uložené procedury** vyberte `[dbo].[usp_write_watermark]`. 
    1. Vyberte **Importovat parametr**. 
    1. Zadejte následující hodnoty parametrů: 

        | Name (Název) | Typ | Hodnota | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Řetězec | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Aktivita Uložená procedura – nastavení uložené procedury](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. Vyberte **Publikovat vše,** chcete-li publikovat entity, které jste vytvořili, do služby Data Factory. 

1. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Pokud chcete zobrazit oznámení, klikněte na odkaz **Zobrazit oznámení**. Zavřete okno oznámení kliknutím na **X**.

 
## <a name="run-the-pipeline"></a>Spuštění kanálu

1. Na panelu nástrojů pro kanál klikněte na **Přidat aktivační událost**a klikněte na **Aktivovat .**     

1. V okně **Spuštění kanálu** zadejte následující hodnotu parametru **tableList** a klikněte na **Dokončit**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![Argumenty spuštění kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se **ručně aktivované** spuštění kanálu. Kliknutím na tlačítko **Aktualizovat** seznam aktualizujte. Pomocí odkazů ve sloupci **Akce** můžete zobrazit spuštění aktivit související se spuštěním kanálu nebo spustit kanál znovu. 

    ![Spuštění kanálu](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se spuštění aktivit související s vybraným spuštěním kanálu. 

## <a name="review-the-results"></a>Kontrola výsledků
V SQL Server Management Studiu spusťte následující dotazy na cílovou databázi SQL a ověřte, že data byla ze zdrojových tabulek zkopírována do cílových tabulek: 

**Dotazu** 
```sql
select * from customer_table
```

**Výstup**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Dotazu**

```sql
select * from project_table
```

**Výstup**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Dotazu**

```sql
select * from watermarktable
```

**Výstup**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Všimněte si, že hodnoty mezí pro obě tabulky byly aktualizovány. 

## <a name="add-more-data-to-the-source-tables"></a>Přidání dalších dat do zdrojových tabulek

Spusťte následující dotaz na zdrojovou databázi SQL Serveru, aby se aktualizoval stávající řádek v tabulce customer_table. Vložte nový řádek do tabulky project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Opětovné spuštění kanálu
1. V levé části okna webového prohlížeče přepněte na kartu **Upravit**. 
1. Na panelu nástrojů pro kanál klikněte na **Přidat aktivační událost**a klikněte na **Aktivovat .**   
1. V okně **Spuštění kanálu** zadejte následující hodnotu parametru **tableList** a klikněte na **Dokončit**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>Opětovné monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se **ručně aktivované** spuštění kanálu. Kliknutím na tlačítko **Aktualizovat** seznam aktualizujte. Pomocí odkazů ve sloupci **Akce** můžete zobrazit spuštění aktivit související se spuštěním kanálu nebo spustit kanál znovu. 

1. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se spuštění aktivit související s vybraným spuštěním kanálu. 

## <a name="review-the-final-results"></a>Kontrola konečných výsledků
V sql server management studio, spusťte následující dotazy proti cílové databázi SQL k ověření, že aktualizovaná/nová data byla zkopírována ze zdrojových tabulek do cílových tabulek. 

**Dotazu** 
```sql
select * from customer_table
```

**Výstup**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Všimněte si nových hodnot položek **Name** a **LastModifytime** pro **PersonID** pro číslo 3. 

**Dotazu**

```sql
select * from project_table
```

**Výstup**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Všimněte si, že do tabulky project_table byla přidána položka **NewProject**. 

**Dotazu**

```sql
select * from watermarktable
```

**Výstup**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Všimněte si, že hodnoty mezí pro obě tabulky byly aktualizovány.
     
## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Příprava zdrojového a cílového datového úložiště
> * Vytvoření datové továrny
> * Vytvoření místního prostředí Integration Runtime (IR)
> * Instalace prostředí Integration Runtime
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření a spuštění kanálu a jeho monitorování
> * Zkontrolujte výsledky.
> * Přidání nebo aktualizace dat ve zdrojových tabulkách
> * Opakované spuštění kanálu a jeho monitorování
> * Kontrola konečných výsledků

Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Přírůstkové načtení dat ze služby Azure SQL Database do úložiště Azure Blob Storage pomocí technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-portal.md)


