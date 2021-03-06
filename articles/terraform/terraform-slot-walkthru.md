---
title: Kurz – Zřízení infrastruktury s sloty pro nasazení Azure pomocí Terraform
description: V tomto kurzu používáte Terraform s sloty pro nasazení zprostředkovatele Azure
keywords: sloty pro nasazení azure devops terraform
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: ddd4d84ee8bf4ab1e90dd68da185cdd9075fe1e0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78943491"
---
# <a name="tutorial-provision-infrastructure-with-azure-deployment-slots-using-terraform"></a>Kurz: Zřízení infrastruktury s sloty pro nasazení Azure pomocí Terraform

[Sloty nasazení Azure](/azure/app-service/deploy-staging-slots) můžete použít k přepínání mezi různými verzemi aplikace. Tato možnost vám pomůže minimalizovat dopad přerušených nasazení. 

Tento článek vám použití slotů nasazení ukáže na nasazením dvou aplikací prostřednictvím GitHubu a Azure. Jedna aplikace je hostovaná v produkčním slotu. Druhá aplikace je hostovaná v přípravném slotu. (Názvy "výroba" a "staging" jsou libovolné. Mohou být, co je vhodné pro váš scénář.) Po konfiguraci slotů nasazení použijete Terraform k přepnutí mezi dvěma sloty podle potřeby.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Účet GitHub**: účet [GitHub](https://www.github.com) potřebujete k vytvoření forku a použití testovacího úložiště GitHub.

## <a name="create-and-apply-the-terraform-plan"></a>Vytvoření a použití plánu Terraformu

1. Přejděte na [portál Azure](https://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview). Pokud jste prostředí ještě nevybrali, vyberte prostředí **Bash**.

    ![Příkazový řádek Cloud Shellu](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Vytvořte adresář s názvem `deploy`.

    ```bash
    mkdir deploy
    ```

1. Vytvořte adresář s názvem `swap`.

    ```bash
    mkdir swap
    ```

1. Použijte příkaz Bash `ls`, abyste ověřili, že se oba adresáře úspěšně vytvořily.

    ![Cloud Shell po vytvoření adresářů](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Přejděte do adresáře `deploy`.

    ```bash
    cd deploy
    ```

1. Ve službě Cloud Shell vytvořte soubor s názvem `deploy.tf`.

    ```bash
    code deploy.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
        app_service_name    = azurerm_app_service.slotDemo.name
    }
    ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

1. Když máte soubor vytvořený, ověřte jeho obsah.

    ```bash
    cat deploy.tf
    ```

1. Inicializujte Terraform.

    ```bash
    terraform init
    ```

1. Vytvořte plán Terraformu.

    ```bash
    terraform plan
    ```

1. Zřiďte prostředky definované v konfiguračním souboru `deploy.tf`. (Akci potvrďte zadáním `yes` po vyzvání.)

    ```bash
    terraform apply
    ```

1. Zavřete okno Cloud Shellu.

1. Na webu Azure Portal vyberte v hlavní nabídce možnost **Skupiny prostředků**.

    ![Výběr skupin prostředků na webu Azure Portal](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Na kartě **Skupiny prostředků** vyberte **slotDemoResourceGroup**.

    ![Skupina prostředků vytvořená nástrojem Terraform](./media/terraform-slot-walkthru/resource-group.png)

Teď uvidíte všechny prostředky vytvořené nástrojem Terraform.

![Prostředky vytvořené nástrojem Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Vytvoření forku projektu testů

Než budete moct otestovat, co jste vytvořili, a prohazovat sloty nasazení, musíte na GitHubu vytvořit fork projektu testů.

1. Přejděte do [úložiště awesome-terraform na GitHubu](https://github.com/Azure/awesome-terraform).

1. Vytvořte fork úložiště **awesome-terraform**.

    ![Vytvoření forku úložiště awesome-terraform](./media/terraform-slot-walkthru/fork-repo.png)

1. Postupujte podle výzev pro vytvoření forku do vašeho prostředí.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Nasazení z GitHubu do slotů nasazení

Po vytvoření forku úložiště projektu testů nakonfigurujte sloty nasazení následujícím způsobem:

1. Na webu Azure Portal vyberte v hlavní nabídce možnost **Skupiny prostředků**.

1. Vyberte **slotDemoResourceGroup**.

1. Vyberte **slotAppService**.

1. Vyberte **Možnosti nasazení**.

    ![Možnosti nasazení pro prostředky App Service](./media/terraform-slot-walkthru/deployment-options.png)

1. Na kartě **Možnost nasazení** zvolte možnost **Vyberte zdroj** a pak vyberte **GitHub**.

    ![Výběr zdroje nasazení](./media/terraform-slot-walkthru/select-source.png)

1. Jakmile Azure naváže spojení a zobrazí všechny možnosti, vyberte možnost **Autorizace**.

1. Na kartě **Autorizace** vyberte **Autorizovat** a zadejte přihlašovací údaje, které Azure potřebuje pro přístup k účtu GitHub. 

1. Jakmile Azure přihlašovací údaje GitHubu ověří, zobrazí se zpráva potvrzující, že se autorizace dokončila. Vyberte **OK** a kartu **Autorizace** zavřete.

1. Vyberte **Zvolte si organizaci** a vyberte svou organizaci.

1. Vyberte **Zvolte projekt**.

1. Na kartě **Zvolte projekt** vyberte projekt **awesome-terraform**.

    ![Volba projektu awesome-terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Vyberte **Zvolte větev**.

1. Na kartě **Zvolte větev** vyberte **master**.

    ![Volba větve master](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Na kartě **Možnosti nasazení** vyberte **OK**.

V tomto okamžiku jste nasadili produkční slot. Chcete-li nasadit pracovní slot, proveďte předchozí kroky s následujícími úpravami:

- V kroku 3 vyberte prostředek **slotAppServiceSlotOne**.

- V kroku 13 vyberte místo větve master pracovní větev.

    ![Volba pracovní větve](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Test nasazení aplikací

V předchozích částech jste nastavili dva sloty – **slotAppService** a **slotAppServiceSlotOne** – které se nasadí z různých větví na GitHubu. Pojďme si zobrazit náhled webových aplikací, abychom si ověřili, že se úspěšně nasadily.

1. Na webu Azure Portal vyberte v hlavní nabídce možnost **Skupiny prostředků**.

1. Vyberte **slotDemoResourceGroup**.

1. Vyberte buď **slotAppService**, nebo **slotAppServiceSlotOne**.

1. Na stránce přehledu vyberte **URL**.

    ![Výběr URL na kartě přehledu pro vykreslení aplikace](./media/terraform-slot-walkthru/resource-url.png)

1. V závislosti na vybrané aplikaci se zobrazí následující výsledky:
    - **slotAppService** webapp - modrá stránka s názvem stránky **Slot Demo App 1**. 
    - **slotAppServiceSlotOne** webová aplikace - Zelená stránka s názvem stránky **Slot Demo App 2**.

    ![Náhled aplikací pro otestování správného nasazení](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Prohození dvou slotů nasazení

Chcete-li otestovat výměnu dvou slotů nasazení, postupujte takto:
 
1. Přepněte se na kartu prohlížeče, na které běží **slotAppService** (aplikace s modrou stránkou). 

1. Na jiné kartě se vraťte na web Azure Portal.

1. Otevřete Cloud Shell.

1. Přejděte do adresáře **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. Ve službě Cloud Shell vytvořte soubor s názvem `swap.tf`.

    ```bash
    code swap.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

1. Inicializujte Terraform.

    ```bash
    terraform init
    ```

1. Vytvořte plán Terraformu.

    ```bash
    terraform plan
    ```

1. Zřiďte prostředky definované v konfiguračním souboru `swap.tf`. (Akci potvrďte zadáním `yes` po vyzvání.)

    ```bash
    terraform apply
    ```

1. Poté, co Terraform vyměnil sloty, vraťte se do prohlížeče. Aktualizujte stránku. 

Webová aplikace v přípravném slotu **slotAppServiceSlotOne** se nyní prohodila s aplikací v produkčním slotu a je nyní vykreslená zeleně. 

![Sloty nasazení se prohodily](./media/terraform-slot-walkthru/slots-swapped.png)

Pokud se chcete vrátit k původní produkční verzi aplikace, použijte znovu plán Terraformu, který jste vytvořili z konfiguračního souboru `swap.tf`.

```bash
terraform apply
```

Po prohození aplikace uvidíte původní konfiguraci.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Další informace o používání Terraform v Azure](/azure/terraform)