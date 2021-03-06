---
title: Vytvoření azure datové továrny pomocí uzuliny Azure Data Factory
description: Vytvořte datovou továrnu s kanálem, který kopíruje data z jednoho umístění v úložišti objektů blob v Azure do jiného.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 02/25/2020
ms.author: jingwang
ms.openlocfilehash: 0afb0ddb65a4f27463e2bb5c1b9441d248c34415
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240755"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Úvodní příručka: Vytvoření datové továrny pomocí uzuliny Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](quickstart-create-data-factory-portal.md)

Tento rychlý start popisuje vytvoření a monitorování datové továrny pomocí uživatelského rozhraní služby Azure Data Factory. Kanál, který vytvoříte v této datové továrně, *kopíruje* data z jedné složky do jiné složky v úložišti objektů blob v Azure. Kurz předvádějící způsoby *transformace* dat pomocí služby Azure Data Factory najdete v tématu [Kurz: Transformace dat pomocí Sparku](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](data-factory-introduction.md), než s tímto rychlým startem začnete. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Toto video vám pomůže seznámit se s uživatelským rozhraním služby Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. Přejděte na [portál Azure](https://portal.azure.com). 
1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.
   
   ![Vyberte Vytvořit prostředek z nabídky portálu Azure.](./media/doc-common-process/create-a-resource.png)
1. Vyberte **Analytics**a pak vyberte **Data Factory**. 
   
   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na stránce **Nová datová továrna** jako **Název** zadejte **ADFTutorialDataFactory**. 
 
   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny (například ** &lt;název&gt;ADFTutorialDataFactory**) a zkuste vytvořit znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
   ![Chyba: název není k dispozici](./media/doc-common-process/name-not-available-error.png)
1. Jako **Předplatné** vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
1. Pro položku **Skupina prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a ze seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit nový**a zadejte název skupiny prostředků.   
         
   Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
1. V poli **Verze** vyberte **V2**.
1. V poli **Umístění** vyberte umístění pro datovou továrnu.

   Seznam obsahuje jenom umístění podporovaná službou Data Factory, do kterých se budou ukládat vaše metadata Azure Data Factory. Přidružená úložiště dat (jako je Azure Storage a Azure SQL Database) a výpočetní prostředky (jako například Azure HDInsight), které Používá Data Factory můžete spustit v jiných oblastech.

1. Vyberte **Vytvořit**. Po dokončení vytváření vyberte **Přejít na prostředek** a přejděte na stránku **Data Factory.** 

1. Kliknutím na dlaždici **Vytvořit a monitorovat** spusťte na samostatné kartě aplikaci uživatelského rozhraní služby Azure Data Factory.
   
   ![Domovská stránka datové továrny s dlaždici Author & Monitor (Vytvořit a sledovat)](./media/doc-common-process/data-factory-home-page.png)
   
   > [!NOTE]
   > Pokud zjistíte, že webový prohlížeč uvízl na "Autorizaci", zrušte zaškrtnutí políčka **Blokovat soubory cookie třetích stran a data webu.** Nebo si ji ponechte vybranou, vytvořte výjimku pro **login.microsoftonline.com**a zkuste aplikaci znovu otevřít.
   
1. Na stránce **Začínáme** přepněte na levém panelu na kartu **Vytvořit**. 

    ![Stránka Začínáme](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-a-linked-service"></a>Vytvoření propojené služby
V tomto postupu vytvoříte propojenou službu, která propojí váš účet Azure Storage s toto datovou továrnou. Tato propojená služba má informace o připojení, které služba Data Factory používá pro připojení za běhu.

1. Vyberte **Připojení**a pak vyberte tlačítko **Nový** na panelu nástrojů (**Tlačítko Připojení** se nachází v dolní části levého sloupce v části **Zdroje výroby**). 

1. Na stránce **Nová propojená služba** vyberte **Azure Blob Storage** a pak vyberte **Pokračovat**. 

1. Na stránce Nová propojená služba (Azure Blob Storage) proveďte následující kroky: 

   a. Jako **Název** zadejte **AzureStorageLinkedService**.

   b. V **části Název účtu úložiště**vyberte název svého účtu Azure Storage.

   c. Vyberte **Test připojení** a potvrďte, že se služba Data Factory dokáže připojit k účtu úložiště. 

   d. Vyberte **Vytvořit,** chcete-li propojenou službu uložit. 

      ![Nová propojená služba](./media/quickstart-create-data-factory-portal/linked-service.png)


## <a name="create-datasets"></a>Vytvoření datových sad
V tomto postupu vytvoříte dvě datové sady:**InputDataset** a **OutputDataset**. Tyto datové sady jsou typu **AzureBlob**. Odkazují na propojenou službu Azure Storage, kterou jste vytvořili v předchozí části. 

Vstupní datová sada představuje zdrojová data ve vstupní složce. V definici vstupní datové sady určíte kontejner objektů blob (**adftutorial**), složku (**input**) a soubor (**emp.txt**) obsahující zdrojová data. 

Výstupní datová sada představuje data kopírovaná do cíle. V definici výstupní datové sady určíte kontejner objektů blob (**adftutorial**), složku (**output**) a soubor, do kterého se data kopírují. Ke každému spuštění kanálu je přiřazené jedinečné ID. Přístup k tomuto ID můžete získat pomocí systémové proměnné **RunId**. Název výstupního souboru se dynamicky vyhodnocuje na základě ID spuštění kanálu.   

V nastavení propojené služby jste zadali účet Azure Storage, který obsahuje zdrojová data. V nastavení zdrojové datové sady určíte, kde přesně se data nacházejí (kontejner objektů blob, složka a soubor). V nastavení datové sady jímky určíte, kam se data kopírují (kontejner objektů blob, složka a soubor). 
 
1. Vyberte **+** tlačítko (plus) a pak vyberte **Datová sada**.

   ![Nabídka pro vytvoření datové sady](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. Na stránce **Nová datová sada** vyberte **Azure Blob Storage**a pak vyberte **Pokračovat**. 

1. Na stránce **Vybrat formát** zvolte typ formátu dat a pak vyberte **Pokračovat**. V takovém případě vyberte **binární,** pokud zkopírujte soubory tak, jak jsou bez analýzy obsahu.

   ![Vybrat formát](./media/quickstart-create-data-factory-portal/select-format.png)
   
1. Na stránce **Nastavit vlastnosti** proveďte následující kroky:

    a. V části **Název**zadejte **InputDataset**. 

    b. Jako **Propojená služba** vyberte **AzureStorageLinkedService**.

    c. V části **Cesta k souboru** vyberte tlačítko **Procházet**.

    d. V okně **Vybrat soubor nebo složku** přejděte do **vstupní** složky v kontejneru **adftutorial,** vyberte soubor **emp.txt** a pak vyberte **OK**.
    
    e. Vyberte **OK**.   

    ![Nastavit vlastnosti pro InputDataset](./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png)
1. Pomocí stejného postupu vytvořte výstupní datovou sadu:  

    a. Vyberte **+** tlačítko (plus) a pak vyberte **Datová sada**.

    b. Na stránce **Nová datová sada** vyberte **Azure Blob Storage**a pak vyberte **Pokračovat**.

    c. Na stránce **Vybrat formát** zvolte typ formátu dat a pak vyberte **Pokračovat**.

    d. Na stránce **Nastavit vlastnosti** zadejte pro název **sadu OutputDataset.** Vyberte **AzureStorageLinkedService** jako propojenou službu.

    e. V části **Cesta k souboru**zadejte **adftutorial/output**. Pokud **výstupní** složka neexistuje, aktivita kopírování ji vytvoří za běhu.

    f. Vyberte **OK**.   

    ![Nastavit vlastnosti pro OutputDataset](./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png)
## <a name="create-a-pipeline"></a>Vytvoření kanálu 
V tomto postupu vytvoříte a ověříte kanál s aktivitou kopírování, která používá vstupní a výstupní datovou sadu. Aktivita kopírování kopíruje data ze souboru zadaného v nastavení vstupní datové sady do souboru zadaného v nastavení výstupní datové sady. Pokud vstupní datová sada určuje pouze složku (bez názvu souboru), aktivita kopírování zkopíruje do cíle všechny soubory ve zdrojové složce. 

1. Vyberte **+** tlačítko (plus) a pak vyberte **Pipeline**. 

1. Na kartě **Obecné** jako **Název** zadejte **CopyPipeline**. 

1. Na panelu nástrojů **Aktivity** rozbalte **Přesunout a transformovat**. Přetáhněte aktivitu **Kopírovat data** z panelu nástrojů **Aktivity** na povrch návrháře kanálu. Na panelu nástrojů **Aktivity** můžete aktivity také vyhledávat. Jako **Název** zadejte **CopyFromBlobToBlob**.
   ![Vytvoření aktivity kopírování dat](./media/quickstart-create-data-factory-portal/copy-activity.png)

1. V nastavení aktivity kopírování přepněte na kartu **Zdroj** a jako **Zdrojová datová sada** vyberte **InputDataset**.

1. V nastavení aktivity kopírování přepněte na kartu **Jímka** a jako **Datová sada jímky** vyberte **OutputDataset**.

1. Kliknutím na **Ověřit** na panelu nástrojů kanálu nad plátnem ověřte nastavení kanálu. Ověřte úspěšné ověření kanálu. Ověřovací výstup zavřete vyberte tlačítko **>>** (šipka vpravo). 
   ![Ověření kanálu](./media/quickstart-create-data-factory-portal/pipeline-validate.png)

## <a name="debug-the-pipeline"></a>Ladění kanálu
V tomto kroku provedete ladění kanálu před tím, než ho nasadíte do služby Data Factory. 

1. Kliknutím na **Ladit** na panelu nástrojů kanálu nad plátnem aktivujte testovací běh. 
    
1. Ověřte, že se na kartě **Výstup** v nastavení kanálu v dolní části zobrazí stav spuštění kanálu. 
 
    ![Výstup spuštění kanálu](./media/quickstart-create-data-factory-portal/pipeline-output.png)

1. Ověřte, že složka **output** kontejneru **adftutorial** obsahuje výstupní soubor. Pokud výstupní složka neexistuje, služba Data Factory ji automaticky vytvoří. 

## <a name="trigger-the-pipeline-manually"></a>Ruční aktivace kanálu
V tomto postupu nasadíte entity (propojené služby, datové sady a kanály) do služby Azure Data Factory. Pak ručně aktivujete spuštění kanálu. 

1. Před aktivací kanálu je nutné publikovat entity do služby Data Factory. Chcete-li publikovat, vyberte **Publikovat vše** nahoře. 
    ![Publikovat vše](./media/quickstart-create-data-factory-portal/publish-all.png)

1. Chcete-li kanál spustit ručně, vyberte přidat **aktivační událost** na panelu nástrojů kanálu a pak vyberte **Možnost Aktivovat nyní**. Na stránce **Pipeline run** vyberte **Dokončit**.

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Pomocí tlačítka **Aktualizovat** seznam aktualizujte.

   ![Karta pro monitorování spuštění kanálu](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Vyberte odkaz **CopyPipeline,** zobrazí se stav aktivity kopírování spuštěné na této stránce. 

1. Chcete-li zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (obrázek brýlí). Podrobnosti o vlastnostech najdete v tématu [Přehled aktivity kopírování](copy-activity-overview.md). 

   ![Podrobnosti o aktivitě kopírování](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Ověřte, že složka **output** obsahuje nový soubor. 
1. Můžete přepnout zpět do zobrazení **Pipeline runs** ze zobrazení **Aktivita spustí** výběrem all pipeline **runs** link. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Aktivace kanálu podle plánu
Tento postup je v tomto kurzu volitelný. Můžete vytvořit *aktivační událost plánovače* a naplánovat pravidelné spouštění kanálu (každou hodinu, každý den atd.). V tomto postupu vytvoříte aktivační událost, která se bude spouštět každou minutu až do koncového data a času, které zadáte. 

1. Přepněte na kartu **Vytvořit**. 

1. Přejděte do kanálu, na panelu nástrojů kanálu vyberte **Přidat aktivační událost** a pak vyberte **Nový/Upravit**. 

1. Na stránce **Přidat aktivační události** vyberte **Zvolit aktivační událost** a pak vyberte **Nová**. 

1. Na stránce **Nová aktivační událost** v části **Konec**vyberte **Datum**, určete čas ukončení několik minut po aktuálním čase a pak vyberte **OK**. 

   Každé spuštění kanálu je zpoplatněno, proto zadejte koncový čas pouze pár minut po času zahájení. Ujistěte se, že se jedná o stejný den. Ujistěte se však, že je dostatek času pro kanál spustit mezi čas publikování a čas ukončení. Aktivační událost nabývá účinnosti po publikování řešení do služby Data Factory, a ne při uložení aktivační události v uživatelském rozhraní. 

1. Na stránce **Nová aktivační událost** zaškrtněte **políčko Aktivovat** a pak vyberte **OK**. 

   ![Nové nastavení aktivační události](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Zkontrolujte varovnou zprávu a vyberte **OK**.

1. Vyberte **Publikovat vše,** chcete-li publikovat změny v Datové továrně. 

1. Vlevo přepněte na kartu **Monitorování**. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. Uvidíte, že se kanál spustí každou minutu od času publikování až do koncového času. 

   Všimněte si hodnot ve **sloupci TRIGGERED BY.** Ruční spuštění aktivační události proběhlo v kroku, který jste provedli dříve (**Aktivovat**). 

1. Přepněte do zobrazení **spuštění aktivační události.** 

1. Ověřte, že se ve složce **output** vytvořil výstupní soubor pro každé spuštění kanálu až do zadaného koncového data a času. 

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Pokud se chcete naučit používat službu Data Factory ve více scénářích, projděte si příslušné [kurzy](tutorial-copy-data-portal.md). 
