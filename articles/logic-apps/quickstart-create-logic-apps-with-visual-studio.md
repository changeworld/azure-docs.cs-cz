---
title: Automatizace pracovních postupů úloh v sadě Visual Studio
description: Vytváření, plánování a spouštění opakovaných pracovních postupů pro podnikovou integraci pomocí aplikací Azure Logic Apps a Visual Studia
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.openlocfilehash: 693b2fd8ac7440b67f53de0aedb9a8268a90de76
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241651"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Úvodní příručka: Vytváření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a sady Visual Studio můžete vytvářet pracovní postupy automatizující úlohy a procesy pro integraci aplikací, dat, systémů a služeb napříč podniky a organizacemi. Tento rychlý start ukazuje, jak můžete navrhovat a vytvářet tyto pracovní postupy vytvořením aplikací logiky ve Visual Studiu a nasazením těchto aplikací do Azure. I když tyto úkoly můžete provádět na webu Azure Portal, Visual Studio umožňuje přidat aplikace logiky do správy zdrojového kódu, publikovat různé verze a vytvářet šablony Azure Resource Manager pro různá prostředí nasazení.

Pokud jste s Azure Logic Apps teprve nováčky a chcete jen základní koncepty, vyzkoušejte [rychlý start pro vytvoření aplikace logiky na webu Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). Návrhář aplikace logiky funguje podobně v portálu Azure i ve Visual Studiu.

V tomto rychlém startu vytvoříte stejnou aplikaci logiky s Visual Studio jako rychlý start portálu Azure. Tato aplikace logiky monitoruje informační kanál RSS webu a odesílá e-maily pro každou novou položku v tomto kanálu. Dokončená aplikace logiky vypadá jako tento pracovní postup na vysoké úrovni:

![Hotová aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje:

  * [Visual Studio 2019, 2017 nebo 2015 – verze komunity nebo vyšší](https://aka.ms/download-visual-studio). Tento rychlý start používá Visual Studio Community 2017.

    > [!IMPORTANT]
    > Při instalaci Visual Studia 2019 nebo 2017 se ujistěte, že vyberete vývojové úlohy **Azure.**

  * [Sada Microsoft Azure SDK pro rozhraní .NET (2.9.1 nebo novější)](https://azure.microsoft.com/downloads/). Další informace o sadě [Azure SDK pro .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Nejnovější nástroje Azure Logic Apps pro rozšíření Visual Studio pro požadovanou verzi:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Azure Logic Apps Tools můžete stáhnout a nainstalovat přímo z Visual Studio Marketplace nebo si můžete přečíst, [jak toto rozšíření nainstalovat v rámci sady Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Po dokončení instalace nezapomeňte sadu Visual Studio restartovat.

* Přístup k webu při používání vloženého Návrháře aplikace logiky

  Návrhář potřebuje připojení k internetu k vytváření prostředků v Azure a ke čtení vlastností a dat z konektorů v aplikaci logiky. Například u připojení aplikace Dynamics CRM Online návrhář zkontroluje výchozí a vlastní vlastnosti instance aplikace CRM.

* E-mailový účet podporovaný v Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Další zprostředkovatelé naleznete v [seznamu konektorů zde](https://docs.microsoft.com/connectors/). Tento příklad používá Office 365 Outlook. Pokud použijete jiného poskytovatele, celkový postup bude stejný, ale vaše uživatelské rozhraní se může mírně lišit.

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Vytvoření projektu skupiny prostředků Azure

Začněte vytvořením [projektu skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Další informace o [skupinách prostředků a prostředcích Azure](../azure-resource-manager/management/overview.md).

1. Spusťte Visual Studio. Přihlaste se pomocí svého účtu Azure.

1. V nabídce **Soubor** vyberte **Nový** > **Projekt**. (Klávesnice: Ctrl + Shift + N)

   ![Výběr možnosti Nový > Projekt v nabídce Soubor](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. V části **Nainstalováno**, vyberte **Visual C#** nebo **Visual Basic**. Vyberte **Cloud** > **Oválná skupina prostředků .** Pojmenujte svůj projekt, například:

   ![Vytvoření projektu skupiny prostředků Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Názvy skupin zdrojů mohou obsahovat pouze`.`písmena,`_`číslice,`-`tečky ( ),`(`podtržítka ( ), pomlčky ( ) a závorky ( , `)`), ale nemohou *končit* tečkami (`.`).
   >
   > Pokud **se cloud** nebo Skupina prostředků **Azure** nezobrazí, ujistěte se, že nainstalujete Azure SDK pro Visual Studio.

   Pokud používáte Visual Studio 2019, postupujte takto:

   1. V poli **Vytvořit nový projekt** vyberte projekt **Skupiny prostředků Azure** pro Visual C# nebo Visual Basic. Vyberte **další**.

   1. Zadejte název skupiny prostředků Azure, kterou chcete použít, a další informace o projektu. Vyberte **Vytvořit**.

1. Ze seznamu šablon vyberte šablonu **aplikace Logika.** Vyberte **OK**.

   ![Výběr šablony Aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Jakmile sada Visual Studio vytvoří projekt, otevře se Průzkumník řešení, ve kterém se zobrazí vaše řešení. Ve vašem řešení soubor **LogicApp.json** nejen ukládá definici aplikace logiky, ale je také šablona Azure Resource Manager, které můžete použít pro nasazení.

   ![Průzkumník řešení se zobrazeným novým řešením aplikace logiky a souborem nasazení](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Vytvoření prázdné aplikace logiky

Když máte projekt skupiny prostředků Azure, vytvořte aplikaci logiky pomocí šablony **Blank Logic App.**

1. V Průzkumníku řešení otevřete místní nabídku souboru **LogicApp.json.** Vyberte **Otevřít pomocí Návrháře aplikace logiky**. (Klávesnice: Ctrl + L)

   ![Otevření souboru .json aplikace logiky pomocí Návrháře aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz nemáte v Sadě Visual Studio 2019, zkontrolujte, zda máte nejnovější aktualizace pro Visual Studio.

   Visual Studio vás vyzve k odběru Azure a skupině prostředků Azure pro vytváření a nasazování prostředků pro vaši aplikaci logiky a připojení.

1. V **případě předplatného**vyberte předplatné Azure. Ve **skupině prostředků**vyberte Vytvořit **nový** a vytvořte další skupinu prostředků Azure.

   ![Výběr předplatného Azure, skupiny prostředků a umístění prostředků](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Nastavení | Příklad hodnoty | Popis |
   | ------- | ------------- | ----------- |
   | Uživatelský účet | Fabrikam <br> sophia-owen@fabrikam.com | Účet, který jste použili při přihlášení k sadě Visual Studio |
   | **Předplatné** | Průběžné platby <br> (sophia-owen@fabrikam.com) | Název vašeho předplatného Azure a přidruženého účtu |
   | **Skupina prostředků** | MyLogicApp-RG <br> (USA – západ) | Skupina prostředků Azure a umístění pro ukládání a nasazování prostředků aplikace logiky |
   | **Umístění** | **Stejné jako skupina prostředků** | Typ umístění a konkrétní umístění pro nasazení aplikace logiky. Typ umístění je oblast Azure nebo existující [prostředí služby integrace (ISE).](connect-virtual-network-vnet-isolated-environment.md) <p>Pro tento rychlý start ponechejte typ umístění nastavený na **oblast** a umístění **nastavené**na Stejné jako skupina prostředků . <p>**Poznámka:** Po vytvoření projektu skupiny zdrojů můžete [změnit typ umístění a umístění](manage-logic-apps-with-visual-studio.md#change-location), ale jiný typ umístění ovlivňuje aplikaci logiky různými způsoby. |
   ||||

1. Návrhář aplikací logiky otevře stránku, která zobrazuje úvodní video a běžně používané aktivační události. Posuňte se dolů za video a aktivuje teze **na šablony**a vyberte Blank **Logic App**.

   ![Výběr možnosti Prázdná aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Sestavení pracovního postupu aplikace logiky

Dále přidejte [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) RSS, která se aktivuje, když se zobrazí nová položka informačního kanálu. Každá aplikace logiky začíná aktivační událostí, která se aktivuje při splnění konkrétních kritérií. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu.

1. V Návrháři aplikací logiky vyberte pod vyhledávacím polem **možnost Vše**. Do vyhledávacího pole zadejte "rss". Ze seznamu aktivačních událostí vyberte tuto aktivační událost: **Při publikování položky informačního kanálu**

   ![Sestavení aplikace logiky přidáním triggeru a akcí](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Po aktivační událost se zobrazí v návrháři, dokončení vytváření aplikace logiky podle kroků pracovního postupu v [rychlém startu portálu Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)a pak se vraťte k tomuto článku. Jakmile budete hotovi, vaše aplikace logiky bude vypadat jako v tomto příkladu:

   ![Hotová aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Uložte řešení sady Visual Studio. (Klávesová zkratka: Ctrl + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Nasazení aplikace logiky do Azure

Než budete moci spustit a otestovat aplikaci logiky, nasaďte aplikaci do Azure z Visual Studia.

1. V Průzkumníku řešení vyberte v místní nabídce projektu **možnost Nasadit** > **nový**. Pokud se zobrazí výzva, přihlaste se pomocí svého účtu Azure.

   ![Vytvoření nasazení aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Pro toto nasazení zachovat výchozí předplatné Azure, skupinu prostředků a další nastavení. Vyberte **Nasadit**.

   ![Nasazení aplikace logiky do skupiny prostředků Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Pokud se zobrazí pole **Upravit parametry,** zadejte název prostředku pro aplikaci logiky. Uložte nastavení.

   ![Zadání názvu nasazení pro aplikaci logiky](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Po zahájení nasazování se v okně **Výstup** sady Visual Studio zobrazí stav nasazení vaší aplikace. Pokud se stav nezobrazí, otevřete seznam **Zobrazit výstup z** a vyberte svou skupinu prostředků Azure.

   ![Výstup stavu nasazení](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Pokud vybrané konektory potřebují vstup od vás, otevře se na pozadí okno PowerShellu a zobrazí výzvu k zadání potřebných hesel nebo tajných klíčů. Po zadání těchto informací bude nasazení pokračovat.

   ![Okno PowerShellu](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Po dokončení nasazení je aplikace logiky aktivní na webu Azure Portal a běží podle zadaného plánu (každou minutu). Pokud aktivační událost najde nové položky informačního kanálu, aktivační událost se aktivuje, což vytvoří instanci pracovního postupu, která spouští akce aplikace logiky. Vaše aplikace logiky odesílá e-maily pro každou novou položku. V opačném případě, pokud aktivační událost nenajde nové položky, aktivační událost se neaktivuje a "přeskočí" vytvoření instance pracovního postupu. Aplikace logiky čeká na další interval před kontrolou.

   Tady jsou ukázkové e-maily, které tato aplikace logiky odesílá. Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanými e-maily.

   ![Outlook odešle e-mail za každou novou položku RRSS](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gratulujeme, úspěšně jste vytvořili a nasadili aplikaci logiky s Visual Studio. Informace o správě aplikace logiky a kontrole historie jejího spuštění najdete v tématu [Správa aplikací logiky s využitím sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Přidání nové aplikace logiky

Pokud máte existující projekt skupiny prostředků Azure, můžete do něj přidat novou prázdnou aplikaci logiky pomocí okna Osnova JSON.

1. V Průzkumníku řešení `<logic-app-name>.json` otevřete soubor.

1. V nabídce **Zobrazení** vyberte **položku Další osnova** > **JSON systému**Windows .

1. Pokud chcete přidat prostředek do souboru šablony, vyberte **Přidat prostředek** v horní části okna Osnova JSON. Nebo v okně Osnova JSON otevřete místní nabídku **zdrojů** a vyberte **Přidat nový zdroj**.

   ![Okno Osnova JSON](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. V dialogovém okně **Přidat prostředek** ve `logic app`vyhledávacím poli vyhledejte a vyberte **aplikaci Logika**. Pojmenujte aplikaci logiky a vyberte **Přidat**.

   ![Přidání zdroje](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi s aplikací logiky, odstraňte skupinu prostředků, která obsahuje aplikaci logiky a související prostředky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí stejného účtu, který jste použili k vytvoření své aplikace logiky.

1. V nabídce Portál Azure vyberte **skupiny prostředků**nebo vyhledejte a vyberte **skupiny prostředků** z libovolné stránky. Vyberte skupinu prostředků aplikace logiky.

1. Na stránce **Přehled** vyberte **Odstranit skupinu prostředků**. Jako potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.

   ![Skupiny prostředků > Přehled > Odstranit skupinu prostředků](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Odstraňte řešení sady Visual Studio ze svého místního počítače.

## <a name="next-steps"></a>Další kroky

V tomto článku jste pomocí sady Visual Studio sestavili, nasadili a spustili aplikaci logiky. Další informace o správě a provádění pokročilého nasazení pro aplikace logiky pomocí Sady Visual Studio najdete v těchto článcích:

> [!div class="nextstepaction"]
> [Správa aplikací logiky s využitím sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
