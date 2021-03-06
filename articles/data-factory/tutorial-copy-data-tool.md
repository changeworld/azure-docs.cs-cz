---
title: Kopírování dat z úložiště objektů blob Azure do SQL pomocí nástroje Kopírovat data
description: Vytvořte azure továrně dat a pak pomocí nástroje Kopírovat data ke kopírování dat z úložiště objektů Blob Azure do databáze SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 52ed43277eef84de826d2f4fa41ba860211a1531
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78969914"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopírování dat z úložiště objektů blob Azure do databáze SQL pomocí nástroje Kopírovat data

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](tutorial-copy-data-tool.md)

V tomto kurzu pomocí webu Azure Portal vytvoříte datovou továrnu. Potom pomocí nástroje Kopírovat data vytvořte kanál, který kopíruje data z úložiště objektů Blob Azure do databáze SQL.

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:
> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet Azure Storage:** Jako _zdrojové_ úložiště dat použijte úložiště objektů blob. Pokud nemáte účet Azure Storage, přečtěte si pokyny v [tématu Vytvoření účtu úložiště](../storage/common/storage-account-create.md).
* **Azure SQL Database:** Jako úložiště dat _jímky_ použijte databázi SQL. Pokud nemáte databázi SQL, přečtěte si pokyny v [tématu Vytvoření databáze SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Připravte úložiště objektů blob a databázi SQL pro kurz provedením těchto kroků.

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

1. Spuštění **poznámkový blok**. Zkopírujte následující text a uložte ho na disk do souboru **inputEmp.txt**:

    ```
    FirstName|LastName
    John|Doe
    Jane|Doe
    ```

1. Vytvořte kontejner **adfv2tutorial** a nahrajte do něj soubor inputEmp.txt. K provádění těchto úloh můžete použít portál Azure nebo různé nástroje, jako je [Azure Storage Explorer.](https://storageexplorer.com/)

#### <a name="create-a-sink-sql-table"></a>Vytvoření tabulky SQL jímky

1. Pomocí následujícího skriptu SQL vytvořte tabulku s názvem **dbo.emp** v databázi SQL:

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

2. Povolte službám Azure přístup k SQL Serveru. Ověřte, zda je pro váš server se spuštěnou databází SQL povoleno nastavení **Povolit službám a prostředkům Azure pro přístup k tomuto serveru.** Toto nastavení umožní službě Data Factory zapisovat data do vaší instance databáze. Chcete-li toto nastavení ověřit a zapnout, přejděte na Azure SQL server > zabezpečení > firewally a virtuální sítě > nastavit možnost **Povolit služby Azure a prostředky pro přístup k tomuto serveru** **na ZAPNUTO**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V levé nabídce vyberte Vytvořit**datovou továrnu****Analytics** >  **:** > 

    ![Vytvoření nové datové továrny](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

    Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:

    ![Nová datová továrna – chybová zpráva](./media/doc-common-process/name-not-available-error.png)

    Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
1. Vyberte **předplatné** Azure, v rámci kterého se má nová datová továrna vytvořit.
1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **Vytvořit nový**a zadejte název skupiny prostředků.
    
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).

1. V části **Verze** vyberte **V2**.
1. V části **umístění**vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních a oblastech.
1. Vyberte **Vytvořit**.

1. Po vytvoření se zobrazí domovská stránka **Datová továrna**.

    ![Domovská stránka objektu pro vytváření dat](./media/doc-common-process/data-factory-home-page.png)
1. Pokud chcete na samostatné kartě otevřít uživatelské rozhraní služby Azure Data Factory, vyberte dlaždici **Vytvořit a monitorovat**.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data**. Spustí se nástroj pro kopírování dat.

    ![Dlaždice nástroje pro kopírování dat](./media/doc-common-process/get-started-page.png)
1. Na stránce **Vlastnosti** v části **Název úlohy** zadejte **CopyFromBlobToSqlPipeline**. Pak vyberte **Další**. Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy.
    ![Vytvoření kanálu](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Kliknutím na **+ Create new connection** (+ Vytvořit nové připojení) přidejte připojení.

    b. V galerii vyberte **Azure Blob Storage** a pak vyberte **Pokračovat**.

    c. Na stránce **Nová propojená služba** vyberte předplatné Azure a vyberte svůj účet úložiště ze seznamu **názvů účtů úložiště.** Otestujte připojení a pak vyberte **Vytvořit**.

    d. Vyberte nově vytvořenou propojenou službu jako zdroj a pak klikněte na **Next** (Další).

    ![Výběr zdrojové propojené služby](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:

    a. Klikněte na **Browse** (Procházet), přejděte do složky **adfv2tutorial/input**, vyberte soubor **inputEmp.txt** a pak klikněte na **Choose** (Zvolit).

    b. Kliknutím na **Next** (Další) přejděte k dalšímu kroku.

1. Na stránce **Nastavení formátu souboru** povolte zaškrtávací políčko *Pro první řádek jako záhlaví*. Všimněte si, že nástroj automaticky detekuje oddělovače sloupců a řádků. Vyberte **další**. Můžete také zobrazit náhled dat a zobrazit schéma vstupních dat na této stránce.

    ![Nastavení formátu souboru](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Na stránce **Destination data store** (Cílové úložiště dat) proveďte následující kroky:

    a. Kliknutím na **+ Create new connection** (+ Vytvořit nové připojení) přidejte připojení.

    b. V galerii vyberte **Azure SQL Database** a pak vyberte **Pokračovat**.

    c. Na stránce **Nová propojená služba** vyberte název serveru a název db v rozevíracím seznamu a zadejte uživatelské jméno a heslo a pak vyberte **Vytvořit**.

    ![Konfigurace Azure SQL DB](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Vyberte nově vytvořenou propojenou službu jako jímku a klikněte na **Next** (Další).

1. Na stránce **Mapování tabulek** vyberte tabulku **[dbo].[emp]** a pak vyberte **Další**.

1. Na stránce **Mapování sloupců** si všimněte, že druhý a třetí sloupec ve vstupním souboru jsou mapovány na sloupce **FirstName** a **LastName** tabulky **emp.** Upravte mapování, abyste se ujistili, že nedošlo k žádné chybě, a pak vyberte **Další**.

    ![Stránka mapování sloupců](./media/tutorial-copy-data-tool/column-mapping.png)

1. Na stránce **Settings** (Nastavení) vyberte **Next** (Další).
1. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.
1. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).
 
    ![Monitorování kanálu](./media/tutorial-copy-data-tool/monitor-pipeline.png)

1. Na stránce Spuštění kanálu vyberte **Aktualizovat,** chcete-li seznam aktualizovat. Kliknutím na odkaz v části **NÁZEV KANÁLU** zobrazíte podrobnosti spuštění aktivity nebo znovu spusťte kanál. 
    ![Spuštění kanálu](./media/tutorial-copy-data-tool/pipeline-run.png)

1. Na stránce Aktivita se spustí vyberte odkaz **Podrobnosti** (ikona brýlí) ve **sloupci NÁZEV AKTIVITY,** kde najdou další podrobnosti o operaci kopírování. Chcete-li se vrátit do zobrazení Spuštění kanálu, vyberte odkaz **Vše, co je spuštěno,** v nabídce s popisem cesty. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.

    ![Monitorování spuštění aktivit](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Ověřte, zda jsou data vložena do tabulky **dbo.emp** v databázi SQL.


1. Výběrem karty **Autor** na levé straně přepněte do režimu úprav. Pomocí editoru můžete aktualizovat propojené služby, datové sady a kanály vytvořené nástrojem. Podrobnosti o úpravách těchto entit v uživatelském rozhraní služby Data Factory najdete ve [verzi tohoto kurzu pro Azure Portal](tutorial-copy-data-portal.md).

    ![Vybrat kartu Autor](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce zkopíruje data z úložiště objektů Blob do databáze SQL. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

Pokud se chcete dozvědět, jak kopírovat data z místního prostředí do cloudu, přejděte k následujícímu kurzu:

>[!div class="nextstepaction"]
>[Kopírování dat z místního prostředí do cloudu](tutorial-hybrid-copy-data-tool.md)
