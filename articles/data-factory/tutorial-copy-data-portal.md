---
title: Vytvoření kanálu datové továrny pomocí portálu Azure
description: Tento kurz obsahuje podrobné pokyny k vytvoření datové továrny s kanálem pomocí portálu Azure Portal. Kanál používá aktivitu kopírování ke kopírování dat z úložiště objektů Blob Azure do databáze Azure SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 9e81efd826c78bfa1af5624272ae9b190cb54348
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313870"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Kopírování dat z úložiště Azure Blob Storage do databáze SQL Database pomocí služby Azure Data Factory
V tomto kurzu vytvoříte datovou továrnu pomocí uživatelského rozhraní služby Azure Data Factory. Kanál v této datové továrně kopíruje data z úložiště objektů blob Azure do databáze Azure SQL. Schéma konfigurace v tomto kurzu se vztahuje na kopírování z úložiště dat založeného na souborech do relačního úložiště dat. Seznam úložišť dat, která jsou podporovaná jako zdroje a jímky, najdete v tabulce [podporovaných úložišť dat](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Pokud se službou Data Factory teprve začínáte, přečtěte si téma [Úvod do Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu s aktivitou kopírování
> * Testovací spuštění kanálu
> * Ruční aktivace kanálu
> * Aktivace kanálu podle plánu
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure**. Blob Storage použijete jako *zdrojové* úložiště dat. Pokud účet úložiště nemáte, přečtěte si téma [Vytvoření účtu služby Azure Storage](../storage/common/storage-account-create.md), kde najdete postup jeho vytvoření.
* **Azure SQL Database**. Tuto databázi použijete jako úložiště dat *jímky*. Pokud nemáte databázi Azure SQL, najdete [v tématu vytvoření databáze SQL](../sql-database/sql-database-get-started-portal.md) pro kroky k vytvoření.

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Teď si připravte úložiště Blob Storage a databázi SQL Database pro tento kurz, a to podle těchto kroků.

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte si ho na disk jako soubor **emp.txt**:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. V úložišti Blob Storage vytvořte kontejner s názvem **adftutorial**. V tomto kontejneru vytvořte složku **input**. Pak do složky **input** nahrajte soubor **emp.txt**. K provedení těchto úloh použijte Azure Portal nebo nástroj, jako je například [Průzkumník služby Azure Storage](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Vytvoření tabulky SQL jímky

1. Použitím následujícího skriptu SQL si v databázi SQL Database vytvořte tabulku **emp**:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Povolte službám Azure přístup k SQL Serveru. Zkontrolujte, že je nastavení **Povolit přístup ke službám Azure** pro váš SQL Server **ZAPNUTÉ**, aby mohla služba Data Factory na tento SQL Server zapisovat data. Chcete-li toto nastavení ověřit a zapnout, přejděte na téma Azure SQL server > Přehled > Nastavit serverovou bránu firewall> nastavit možnost **Povolit přístup ke službám Azure** **na ZAPNUTO**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
V tomto kroku vytvoříte datovou továrnu a spustíte uživatelské rozhraní služby Data Factory, ve kterém v této datové továrně vytvoříte kanál.

1. Otevřete **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. V levé nabídce vyberte Vytvořit**datovou továrnu****analytics** >  **zdrojů** > .
3. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. (například yournameADFTutorialDataFactory). Pravidla pro pojmenovávání artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).

     ![Nová datová továrna](./media/doc-common-process/name-not-available-error.png)
4. Vyberte **předplatné** Azure, v rámci kterého chcete datovou továrnu vytvořit.
5. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **Vytvořit nový**a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md). 
6. Jako **Verzi** vyberte **V2**.
7. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používaná datovou továrnou můžou být v jiných oblastech.
8. Vyberte **Vytvořit**.
9. Po dokončení vytváření se zobrazí oznámení v centru oznámení. Chcete-li přejít **na zdroj,** přejděte na stránku Datové toky.
10. Vyberte **Vytvořit a monitorovat**. Na samostatné kartě se spustí uživatelské rozhraní služby Data Factory.


## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte v datové továrně kanál s aktivitou kopírování. Aktivita kopírování kopíruje data z úložiště Blob Storage do databáze SQL Database. V [kurzu Rychlý start](quickstart-create-data-factory-portal.md) jste vytvořili kanál pomocí tohoto postupu:

1. Vytvoření propojené služby
1. Vytvoření vstupní a výstupní datové sady
1. Vytvoření kanálu

V tomto kurzu začnete vytvořením kanálu. Potom vytvoříte propojené služby a datové sady, které budete potřebovat ke konfiguraci kanálu.

1. Na stránce **Začínáme** vyberte **Vytvořit kanál**.

   ![Vytvoření kanálu](./media/doc-common-process/get-started-page.png)
1. Na kartě kanálu **Obecné** zadejte **CopyPipeline** jako **Název** kanálu.

1. V poli **Nástroje Aktivity** rozbalte kategorii **Přesunout a Transformovat** a přetáhněte aktivitu Kopírovat **data** z panelu nástrojů na povrch návrháře kanálu. Jako **Název** zadejte **CopyFromBlobToSql**.

    ![Aktivita kopírování](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurace zdroje

>[!TIP]
>V tomto kurzu použijete *klíč account* jako typ ověřování pro úložiště zdrojových dat, ale v případě potřeby můžete zvolit jiné podporované metody ověřování: Identifikátor *URI SAS*,*Instanční objekt* a *Spravovaná identita.* Podrobnosti naleznete v odpovídajících částech [tohoto článku.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties)
>Chcete-li bezpečně ukládat tajné klíče pro úložiště dat, doporučujeme také použít trezor klíčů Azure. Podrobné ilustrace naleznete v [tomto článku.](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)

1. Přejděte na kartu **+ New** **Zdroj.**

1. V dialogovém okně **Nová datová sada** vyberte **Azure Blob Storage**a pak vyberte **Pokračovat**. Zdrojová data jsou v úložišti Blob Storage, takže jako zdrojovou datovou sadu vyberete **Azure Blob Storage**.

1. V dialogovém okně **Vybrat formát** zvolte typ formátu dat a pak vyberte **Pokračovat**.

1. V dialogovém okně **Nastavit vlastnosti** zadejte **SourceBlobDataset** pro Název. Zaškrtněte políčko **Pro první řádek jako záhlaví**. V textovém poli **Propojená služba** vyberte **+ Nový**.

1. V dialogovém **okně Nová propojená služba (Azure Blob Storage)** zadejte jako název **Službu AzureStorageLinkedService** a vyberte účet úložiště ze seznamu **názvů účtů úložiště.** Otestujte připojení, vyberte **Vytvořit** pro nasazení propojené služby.

1. Po vytvoření propojené služby se přejde zpět na stránku **Nastavit vlastnosti.** Vedle pole **Cesta k souboru** vyberte **Procházet**.

1. Přejděte do složky **adftutorial/input,** vyberte soubor **emp.txt** a pak vyberte **OK**.

1. Vyberte **OK**. Automaticky přejde na stránku kanálu. Na kartě **Zdroj** zkontrolujte, zda je vybraná volba **SourceBlobDataset.** Pokud se na této stránce chcete podívat na náhled dat, vyberte **Náhled dat**.

    ![Zdrojová datová sada](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Konfigurace jímky
>[!TIP]
>V tomto kurzu použijete *ověřování SQL* jako typ ověřování pro úložiště dat jímky, ale v případě potřeby můžete zvolit jiné podporované metody ověřování: *Instanční objekt* a *Spravovanou identitu.* Podrobnosti naleznete v odpovídajících částech [tohoto článku.](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties)
>Chcete-li bezpečně ukládat tajné klíče pro úložiště dat, doporučujeme také použít trezor klíčů Azure. Podrobné ilustrace naleznete v [tomto článku.](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)

1. Přejděte na kartu **Jímka**, vyberte **+ Nová** a vytvořte datovou sadu jímky.

1. V dialogovém okně **Nová datová sada** zadejte do vyhledávacího pole text SQL, vyberte **Azure SQL Database**a pak vyberte **Pokračovat**. V tomto kurzu zkopírujte data do databáze SQL Database.

1. V dialogovém okně **Nastavit vlastnosti** zadejte **příkaz OutputSqlDataset** pro Název. V rozevíracím seznamu **Propojené služby** vyberte **+ Nový**. Datová sada musí být přidružená k propojené službě. Propojená služba obsahuje připojovací řetězec, který služba Data Factory používá pro připojení k databázi SQL Database za běhu. Datová sada určuje kontejner, složku a soubor (volitelné), do kterého se data kopírují.

1. V dialogovém **okně Nová propojená služba (Azure SQL Database)** proveďte následující kroky:

    a. Do pole **Název** zadejte **AzureSqlDatabaseLinkedService**.

    b. V rozevíracím seznamu **Název serveru** vyberte příslušný název instance SQL Serveru.

    c. V rozevíracím seznamu **Název databáze** vyberte svoji databázi SQL Database.

    d. Do pole **Uživatelské jméno** zadejte jméno uživatele.

    e. Do pole **Heslo** zadejte heslo pro tohoto uživatele.

    f. Vyberte **Otestovat připojení** a připojení otestujte.

    g. Vyberte **Vytvořit,** chcete-li nasadit propojenou službu.

    ![Uložení nové propojené služby](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Automaticky přejde do dialogového okna **Nastavit vlastnosti.** V části **Tabulka** vyberte **[dbo].[emp]**. Pak vyberte **OK**.

1. Přejděte na kartu s kanálem a zkontrolujte, že je v rozevíracím seznamu **Datová sada jímky** vybraná sada **OutputSqlDataset**.

    ![Karta Kanál](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Volitelně můžete namapovat schéma zdroje na odpovídající schéma cíle podle následujícího [mapování schématu v aktivitě kopírování](copy-activity-schema-and-type-mapping.md).

## <a name="validate-the-pipeline"></a>Ověření kanálu
Vyberte **Ověřit** z panelu nástrojů a kanál ověřte.

Kód JSON přidružený k kanálu můžete zobrazit kliknutím na **kód** v pravém horním části.

## <a name="debug-and-publish-the-pipeline"></a>Ladění a publikování kanálu
Před publikováním artefaktů (propojených služeb, datových sad a kanálu) do služby Data Factory nebo vlastního úložiště Gitu Azure Repos můžete kanál odladit.

1. K ladění kanálu vyberte na panelu nástrojů **Ladit**. Na kartě **Výstup** v dolní části okna se zobrazí stav spuštění kanálu.

1. Jakmile bude kanál úspěšně spuštěn, vyberte v horním panelu nástrojů **možnost Publikovat vše**. Touto akcí publikujete vytvořené entity (datové sady a kanály) do služby Data Factory.

1. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Pokud chcete zobrazit zprávy oznámení, klikněte vpravo nahoře (tlačítko zvonečku) na **Zobrazit oznámení**.

## <a name="trigger-the-pipeline-manually"></a>Ruční aktivace kanálu
V tomto kroku ručně aktivujete kanál, který jste publikovali v minulém kroku.

1. Vyberte na panelu nástrojů **Aktivační událost** a potom vyberte **Aktivovat**. Na stránce **Pipeline Run** vyberte **OK**.  

1. Vlevo přejděte na kartu **Monitorování**. Zobrazí se stav ručně aktivovaného spuštění kanálu. Pomocí odkazů ve sloupci **NÁZEV kanálu** můžete zobrazit podrobnosti o aktivitě a znovu spustit kanál.

    [![Spuštění kanálu monitoru](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. Chcete-li zobrazit spuštění aktivity přidružená ke spuštění kanálu, vyberte odkaz **CopyPipeline** ve sloupci **NÁZEV KANÁLU.** V tomto příkladu je pouze jedna aktivita, takže se v seznamu zobrazí pouze jedna položka. Podrobnosti o operaci kopírování vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **NÁZEV AKTIVITY.** Vyberte **Všechny kanál běží** v horní části se vrátit do zobrazení Spuštění kanálu. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.

    [![Monitorování spuštění aktivit](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Ověřte, že se do tabulky **emp** v databázi SQL Database přidaly další dva řádky.

## <a name="trigger-the-pipeline-on-a-schedule"></a>Aktivace kanálu podle plánu
V tomto kroku vytvoříte pro kanál aktivační událost plánovače. Tato aktivační událost spouští kanál podle zadaného plánu (například každou hodinu nebo každý den). Zde nastavíte spuštění aktivační události každou minutu až do zadaného koncového data.

1. Vlevo nad kartou monitorování přejděte na kartu **Autor**.

1. Přejděte na kanál, na panelu nástrojů klikněte na **Aktivační událost** a vyberte **Nové/upravit**.

1. V dialogovém okně **Přidat aktivační události** vyberte + **Nový** pro **oblast Zvolte aktivační událost.**

1. V okně **Nová aktivační událost** proveďte následující kroky:

    a. Do pole **Název** zadejte **RunEveryMinute**.

    b. V části **Konec** vyberte **Dne**.

    c. Klikněte na šipku rozevíracího seznamu **Konec dne**.

    d. Vyberte **aktuální den**. Ve výchozím nastavení je koncový den nastavený na další den.

    e. Aktualizujte část **Koncový čas** tak, aby byla několik minut po aktuálním časovém čase. Aktivační událost se aktivuje pouze po publikování změn. Pokud ji nastavíte na pouhých pár minut od sebe a do té doby ji nepublikujete, spuštění aktivační události se nezobrazí.

    f. Vyberte **OK**.

    g. V **možnosti Aktivovat** vyberte **možnost Ano**.

    h. Vyberte **OK**.

    > [!IMPORTANT]
    > S každým spuštěním kanálu jsou spojené určité náklady, takže nastavte koncové datum správně.

1. Na stránce **Upravit aktivační událost** zkontrolujte upozornění a pak vyberte **Uložit**. Kanál v tomto příkladu nepoužívá žádné parametry.

1. Kliknutím na **Publikovat vše** publikujte změnu.

1. Vlevo přejděte na kartu **Monitorování**, kde uvidíte aktivovaná spuštění kanálu.

    [![Aktivovaná spuštění kanálu](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. Chcete-li přepnout ze zobrazení **Spuštění kanálu** do zobrazení Spuštění **aktivační události,** vyberte **možnost Spuštění aktivační události** na levé straně okna.

1. V seznamu se zobrazí spuštění aktivační události.

1. Ověřte, že se až do uplynutí zadaného koncového času budou do tabulky **emp** vkládat dva řádky za minutu (pro každé spuštění kanálu).

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti Blob Storage. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu s aktivitou kopírování
> * Testovací spuštění kanálu
> * Ruční aktivace kanálu
> * Aktivace kanálu podle plánu
> * Monitorování spuštění aktivit a kanálu


Pokud se chcete dozvědět, jak kopírovat data z místního prostředí do cloudu, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Kopírování dat z místního prostředí do cloudu](tutorial-hybrid-copy-portal.md)
