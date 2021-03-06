---
title: Kopírování dat pomocí nástroje Azure Copy Data
description: Vytvořte datovou továrnu Azure a pak pomocí nástroje Kopírování dat zkopírujte data z jednoho umístění v úložišti objektů blob v Azure do jiného.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 03/18/2020
ms.openlocfilehash: 26169755fbe252a4be2626dae50d40c005c7c6db
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130951"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Úvodní příručka: Kopírování dat pomocí nástroje Kopírovat data

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](quickstart-create-data-factory-copy-data-tool.md)

V tomto rychlém startu použijete Azure Portal k vytvoření datové továrny. Pak pomocí nástroje pro kopírování dat vytvoříte kanál, který kopíruje data z jedné složky v úložišti objektů blob v Azure do jiné. 

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](data-factory-introduction.md), než s tímto rychlým startem začnete. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. Přejděte na [portál Azure](https://portal.azure.com). 
1. V nabídce Portál Azure vyberte Vytvořit**továrnu na data****analýzy** >  **prostředků** > :

    ![Vytvoření nové datové továrny](./media/doc-common-process/new-azure-data-factory-menu.png)

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

1. Vyberte **Vytvořit**.

1. Po dokončení vytváření se zobrazí stránka **Data Factory.** Kliknutím na dlaždici **Vytvořit a monitorovat** spusťte na samostatné kartě aplikaci uživatelského rozhraní služby Azure Data Factory.
   
   ![Domovská stránka datové továrny s dlaždici Author & Monitor (Vytvořit a sledovat)](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Spuštění nástroje pro kopírování dat

1. Na stránce **Let‘s get started** (Začínáme) vyberte dlaždici **Copy Data** (Kopírovat data). Spustí se nástroj pro kopírování dat. 

   ![Dlaždice Copy Data (Kopírovat data)](./media/doc-common-process/get-started-page.png)

1. Na stránce **Properties** (Vlastnosti) nástroje Kopírování dat můžete zadat název kanálu a jeho popis. Pak vyberte **Next** (Další). 

   ![Stránka Properties (Vlastnosti)](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Chcete-li přidat připojení, klepněte na tlačítko **+ Vytvořit nové připojení.**

    b. Vyberte typ propojené služby, který chcete vytvořit pro zdrojové připojení. V tomto kurzu používáme **Azure Blob Storage**. Vyberte ji z galerie a pak vyberte **Pokračovat**.
    
    ![Vybrat objekt blob](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Na stránce **Nová propojená služba (Azure Blob Storage)** zadejte název propojené služby. Vyberte účet úložiště ze seznamu **název účtu úložiště,** otestujte připojení a pak vyberte **Vytvořit**. 

    ![Konfigurace účtu služby Azure Blob Storage](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Vyberte nově vytvořenou propojenou službu jako zdroj a klepněte na tlačítko **Další**.


1. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:

   a. Klepnutím na **tlačítko Procházet** přejděte do složky **adftutorial/input,** vyberte soubor **emp.txt** a klepněte na tlačítko **Vybrat**. 

   d. Zaškrtněte políčko **Binární kopírování,** chcete-li zkopírovat soubor tak, jak je, a pak vyberte **další**. 

   ![Stránka Choose the input file or folder (Zvolit vstupní soubor nebo složku)](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Na stránce **Cílové úložiště dat** vyberte propojenou službu Azure **Blob Storage,** kterou jste vytvořili, a pak vyberte **Další**. 

1. Na stránce **Vybrat výstupní soubor nebo složku** zadejte **adftutorial/output** pro cestu ke složce a pak vyberte **Další**. 

   ![Stránka Choose the output file or folder (Zvolit výstupní soubor nebo složku)](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Na stránce **Settings** (Nastavení) vyberte **Next** (Další), aby se použila výchozí konfigurace. 

1. Na stránce **Summary** (Souhrn) zkontrolujte všechna nastavení a vyberte **Next** (Další). 

1. Na stránce **Deployment complete** (Nasazení dokončeno) vyberte **Monitor** (Sledovat) a začněte sledovat vytvořený kanál. 

    ![Stránka Deployment complete (Nasazení dokončeno)](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Aplikace se přepne na kartu **Monitor.** Na této kartě se zobrazí stav **Refresh** kanálu. Kliknutím na odkaz v části **NÁZEV KANÁLU** zobrazíte podrobnosti spuštění aktivity nebo znovu spusťte kanál. 
   
    ![Aktualizovat kanál](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. Na stránce Aktivita se spustí vyberte odkaz **Podrobnosti** (ikona brýlí) ve **sloupci NÁZEV AKTIVITY,** kde najdou další podrobnosti o operaci kopírování. Podrobnosti o vlastnostech najdete v tématu [Přehled aktivity kopírování](copy-activity-overview.md). 

1. Chcete-li se vrátit do zobrazení Spuštění kanálu, vyberte odkaz **Vše, co je spuštěno,** v nabídce s popisem cesty. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**. 

1. Ověřte, že se ve složce **output** kontejneru **adftutorial** vytvořil soubor **emp.txt**. Pokud výstupní složka neexistuje, služba Data Factory ji automaticky vytvoří. 

1. Nad kartou **Monitorování** v levém panelu přepněte na kartu **Vytvořit**, kde můžete upravovat propojené služby, datové sady a kanály. Další informace o jejich úpravách v uživatelském rozhraní služby Data Factory najdete v tématu [Vytvoření datové továrny pomocí webu Azure Portal](quickstart-create-data-factory-portal.md).

    ![Vybrat kartu Autor](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Pokud se chcete naučit používat službu Data Factory ve více scénářích, projděte si příslušné [kurzy](tutorial-copy-data-portal.md). 
