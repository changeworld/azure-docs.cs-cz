---
title: Kurz – vytvoření clusteru Kubernetes se službou Azure Kubernetes Service (AKS) pomocí Terraform
description: V tomto kurzu vytvoříte cluster Kubernetes se službou Azure Kubernetes service a Terraform
keywords: azure devops terraform aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: b7a84d7562e99e53ff7be75b7d40795cd3f9e203
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618925"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Kurz: Vytvoření clusteru Kubernetes se službou Azure Kubernetes pomocí Terraform

[Služba Azure Kubernetes Service (AKS)](/azure/aks/) spravuje vaše hostované prostředí Kubernetes. AKS umožňuje nasadit a spravovat kontejnerizované aplikace bez odborných znalostí orchestrace kontejnerů. AKS také umožňuje provést mnoho běžných operací údržby bez přepnete aplikaci do režimu offline. Tyto operace zahrnují zřizování, inovace a škálování prostředků na vyžádání.

V tomto kurzu se dozvíte, jak provést následující úkoly:

> [!div class="checklist"]
> * Použití HCL (Jazyk společnosti HashiCorp) k definování clusteru Kubernetes
> * Použití Terraformu a AKS k vytvoření clusteru Kubernetes
> * Použití nástroje kubectl k otestování dostupnosti clusteru Kubernetes

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Nakonfigurovaný nástroj Terraform**: Postupujte podle pokynů v článku o [instalaci Terraformu a konfiguraci přístupu k Azure](terraform-install-configure.md).

- **Instanční objekt Azure**: Postupujte podle pokynů v části **Vytvořit instanční objekt** v článku Vytvoření [instančního objektu Azure pomocí azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Poznamenejte si hodnoty appId, displayName, password a tenant.

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře

Prvním krokem je vytvoření adresáře s konfiguračními soubory Terraformu pro toto cvičení.

1. Přejděte na [portál Azure](https://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview). Pokud jste prostředí ještě nevybrali, vyberte prostředí **Bash**.

    ![Příkazový řádek Cloud Shellu](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Vytvořte adresář s názvem `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Přejděte do nového adresáře:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Deklarování zprostředkovatele Azure

Vytvořte konfigurační soubor Terraformu, který deklaruje zprostředkovatele Azure.

1. Ve službě Cloud Shell vytvořte soubor s názvem `main.tf`.

    ```bash
    code main.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
    provider "azurerm" {
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

## <a name="define-a-kubernetes-cluster"></a>Definování clusteru Kubernetes

Vytvořte konfigurační soubor Terraformu, který deklaruje prostředky pro cluster Kubernetes.

1. Ve službě Cloud Shell vytvořte soubor s názvem `k8s.tf`.

    ```bash
    code k8s.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        default_node_pool {
            name            = "agentpool"
            node_count      = var.agent_count
            vm_size         = "Standard_DS1_v2"
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    Předchozí kód nastaví název clusteru, umístění a název skupiny prostředků. Je také nastavena předpona plně kvalifikovaného názvu domény (FQDN). K přístupu ke clusteru se používá vícenežvizovaný název.

    Záznam `linux_profile` umožňuje konfigurovat nastavení, která umožňují přihlášení do pracovních uzlů pomocí SSH.

    Se službou AKS platíte jenom za pracovní uzly. Záznam `default_node_pool` konfiguruje podrobnosti pro tyto pracovní uzly. Obsahuje `default_node_pool record` počet pracovních uzlů, které mají být vytesány, a typ pracovních uzlů. Pokud potřebujete vertikálně navýšit kapacitu nebo vertikálně navýšit kapacitu clusteru v budoucnu, můžete upravit hodnotu `count` v tomto záznamu.

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

## <a name="declare-the-variables"></a>Deklarování proměnných

1. Ve službě Cloud Shell vytvořte soubor s názvem `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

## <a name="create-a-terraform-output-file"></a>Vytvoření výstupního souboru Terraformu

[Výstupy Terraformu](https://www.terraform.io/docs/configuration/outputs.html) vám umožňují definovat hodnoty, které se uživateli zvýrazní, když Terraform použije plán a bude možné se na něj dotázat příkazem `terraform output`. V této části vytvoříte výstupní soubor, který pomocí [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) umožňuje přístup ke clusteru.

1. Ve službě Cloud Shell vytvořte soubor s názvem `output.tf`.

    ```bash
    code output.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }
    ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Nastavení služby Azure Storage k uložení stavu Terraformu

Terraform sleduje stav místně prostřednictvím souboru `terraform.tfstate`. Tento model funguje dobře v prostředí s jednou osobou. V prostředí pro více osob se [úložiště Azure](/azure/storage/) používá ke sledování stavu.

V této části se zobrazí následující úkoly:
- Načtení informací o účtu úložiště (název účtu a klíč účtu)
- Vytvořte kontejner úložiště, do kterého budou uloženy informace o stavu Terraform.

1. Na webu Azure Portal vyberte v levé nabídce **Všechny služby**.

1. Vyberte **Účty úložiště**.

1. Na kartě **Účty úložiště** vyberte název účtu úložiště, do kterého má Terraform ukládat stav. Můžete například použít účet úložiště, který se vytvoří při prvním otevření služby Cloud Shell.  Název účtu úložiště vytvořeného službou Cloud Shell obvykle začíná na `cs` a následuje ho řetězec náhodných čísel a písmen. Poznamenejte si účet úložiště, který vyberete. Tato hodnota je potřeba později.

1. Na kartě účtu úložiště vyberte **Přístupové klíče**.

    ![Nabídka účtu úložiště](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Poznamenejte si hodnotu **klíče** **key1.** (Výběr ikony napravo od klíče zkopíruje příslušnou hodnotu do schránky.)

    ![Přístupové klíče účtu úložiště](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. V Cloud Shellu vytvořte kontejner ve svém účtu úložiště Azure. Nahraďte zástupné symboly odpovídajícími hodnotami pro vaše prostředí.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes

V této části se zobrazí `terraform init` způsob, jak pomocí příkazu vytvořit prostředky definované v konfiguračních souborech, které jste vytvořili v předchozích částech.

1. V prostředí Cloud Shell inicializujte Terraform. Nahraďte zástupné symboly odpovídajícími hodnotami pro vaše prostředí.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    Příkaz `terraform init` zobrazuje úspěch inicializace modulu plug-in-in-in-

    ![Příklad výsledků příkazu „terraform init“](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exportujte své přihlašovací údaje instančního objektu. Nahraďte zástupné symboly příslušnými hodnotami z instančního objektu.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. Spuštěním příkazu `terraform plan` vytvořte plán Terraformu, který definuje prvky infrastruktury. 

    ```bash
    terraform plan -out out.plan
    ```

    Příkaz `terraform plan` zobrazí prostředky, které se po spuštění příkazu `terraform apply` vytvoří:

    ![Příklad výsledků příkazu „terraform plan“](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Spuštěním příkazu `terraform apply` použijte plán a vytvořte cluster Kubernetes. Proces vytvoření clusteru Kubernetes může trvat několik minut, což má za následek vypršení časového limitu prostředí cloudu. Pokud časový čas relace prostředí Cloud, můžete postupovat podle kroků v části "Obnovení z časového úseku cloudového prostředí", abyste mohli dokončit kurz.

    ```bash
    terraform apply out.plan
    ```

    Příkaz `terraform apply` zobrazí výsledky vytvoření prostředků definovaných v konfiguračních souborech:

    ![Příklad výsledků příkazu „terraform apply“](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Na webu Azure Portal vyberte **Všechny prostředky** v levé nabídce, abyste viděli prostředky vytvořené pro váš nový cluster Kubernetes.

    ![Všechny prostředky na webu Azure Portal](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Zotavení z vypršení relace služby Cloud Shell

Pokud časový nebo časový čas relace prostředí Cloud, můžete provést následující kroky k obnovení:

1. Spusťte relaci služby Cloud Shell.

1. Přejděte do adresáře, který obsahuje konfigurační soubory Terraformu.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Spusťte následující příkaz:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Test clusteru Kubernetes

Nově vytvořený cluster můžete pomocí nástrojů Kubernetes ověřit.

1. Získejte konfiguraci Kubernetes ze stavu Terraformu a uložte ji do souboru, který dokáže nástroj kubectl přečíst.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Nastavte proměnnou prostředí, aby kubectl převzal správnou konfiguraci.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Ověřte stav clusteru.

    ```bash
    kubectl get nodes
    ```

    Měly by se zobrazit podrobnosti o pracovních uzlech a všechny uzly by měli být ve stavu **Ready** (Připravené), jak je znázorněno na následujícím obrázku:

    ![Nástroj kubectl vám umožňuje ověřit stav clusteru Kubernetes.](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Monitorování stavu a protokolů

Při vytvoření clusteru AKS se povolilo monitorování za účelem zachycování metrik stavu podů i uzlů clusteru. Tyto metriky stavu jsou k dispozici na webu Azure Portal. Další informace o monitorování stavu clusteru najdete v tématu [Monitorování stavu služby Azure Kubernetes Service](/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Další informace o používání Terraform v Azure](/azure/terraform)