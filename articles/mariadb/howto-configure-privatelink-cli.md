---
title: Privátní propojení – Azure CLI – databáze Azure pro MariaDB
description: Zjistěte, jak nakonfigurovat privátní propojení pro Azure Database pro MariaDB z azure CLI
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: c28c5494c1cff2c198a94ea6b92003ae74ee2c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371694"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>Vytvoření a správa privátního propojení pro Azure Database pro MariaDB pomocí příkazového příkazového příkazu

Privátní koncový bod je základní stavební blok pro privátní propojení v Azure. Umožňuje prostředky Azure, jako jsou virtuální počítače (VM), soukromě komunikovat s prostředky privátní ho propojení. V tomto článku se dozvíte, jak pomocí azure cli vytvořit virtuální počítač ve virtuální síti Azure a Azure database for MariaDB server s azure privátní koncový bod.

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for MariaDB podporuje cenové úrovně optimalizované pro obecné účely a paměť.

## <a name="prerequisites"></a>Požadavky

Chcete-li projít tento návod, co potřebujete:

- [Databáze Azure pro server MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a použít Azure CLI místně místo, tento rychlý start vyžaduje použití Azure CLI verze 2.0.28 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte program `az --version`. Informace o instalaci nebo upgradu [najdete v tématu Instalace příkazového příkazového](/cli/azure/install-azure-cli) příkazu Konzumu Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením libovolného prostředku je třeba vytvořit skupinu prostředků pro hostování virtuální sítě. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *westeurope:*

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť s [vytvořením virtuální sítě AZ](/cli/azure/network/vnet). Tento příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* s jednou podsítí s názvem *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Zakázání zásad soukromého koncového bodu podsítě 
Azure nasazuje prostředky do podsítě v rámci virtuální sítě, takže je potřeba vytvořit nebo aktualizovat podsíť, abyste zakázali zásady privátní koncové sítě. Aktualizace konfigurace podsítě s názvem *mySubnet* s [aktualizací podsítě sítě AZ](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Vytvořte virtuální počítač. 
Vytvořte virtuální hosti s vytvořením virtuálního va. Po zobrazení výzvy zadejte heslo, které se má použít jako přihlašovací údaje pro virtuální hod. Tento příklad vytvoří virtuální hod s názvem *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Všimněte si veřejné IP adresy virtuálního soudu. Tuto adresu použijete k připojení k virtuálnímu virtuálnímu zařízení z internetu v dalším kroku.

## <a name="create-an-azure-database-for-mariadb-server"></a>Vytvoření serveru Azure Database for MariaDB 
Vytvořte databázi Azure pro MariaDB pomocí příkazu az mariadb server create. Nezapomeňte, že název vašeho serveru MariaDB musí být v azure jedinečný, proto zástupnou hodnotu v závorkách nahraďte vlastní jedinečnou hodnotou: 

```azurecli-interactive
# Create a logical server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Poznámka: ID serveru MariaDB ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMariaDB/servers/servername.``` je podobné Budete používat MariaDB ID serveru v dalším kroku. 

## <a name="create-the-private-endpoint"></a>Vytvoření soukromého koncového bodu 
Vytvořte privátní koncový bod pro server MariaDB ve virtuální síti: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MariaDB Server ID>" \  
    --group-ids mariadbServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurace privátní zóny DNS 
Vytvořte privátní zónu DNS pro doménu serveru MariDB a vytvořte přidružení propojení s virtuální sítí. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Hlavní název domény v nastavení DNS zákazníka se nepřekládá na nakonfigurovanou privátní IP adresu. Budete muset nastavit zónu DNS pro nakonfigurovaný program ReQDN, jak je znázorněno [zde](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k *myvm* virtuálního zařízení z internetu takto:

1. Na vyhledávacím panelu portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **Připojit** se otevře **možnost Připojit k virtuálnímu počítači.**

1. Vyberte **stáhnout soubor RDP**. Azure vytvoří soubor rdp (Remote Desktop Protocol *)* a stáhne ho do počítače.

1. Otevřete *soubor Downloaded.rdp.*

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby:** > K zadání pověření, která jste zadali při vytváření virtuálního vztahu,**můžete zadat jiný účet**.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji a vraťte se na místní plochu.  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Přístup k serveru MariaDB soukromě z virtuálního mísa

1. Na vzdálené ploše *myVM*otevřete PowerShell.

2. Zadejte  `nslookup mydemoserver.privatelink.mariadb.database.azure.com`. 

    Zobrazí se zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Otestujte připojení privátního propojení pro server MariaDB pomocí libovolného dostupného klienta. V níže uvedeném příkladu jsem použil [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) k operaci.

4. V **novém připojení**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název připojení| Vyberte název připojení podle vašeho výběru.|
    | Název hostitele | Vybrat *mydemoserver.privatelink.mariadb.database.azure.com* |
    | Uživatelské jméno | Zadejte uživatelské *username@servername* jméno, které je k dispozici při vytváření serveru MariaDB. |
    | Heslo | Zadejte heslo poskytnuté během vytváření serveru MariaDB. |
    ||

5. Vyberte **testovat připojení** nebo **OK**.

6. (Volitelně) Procházení databází z levé nabídky a vytvoření nebo dotazování informací z databáze MariaDB

8. Zavřete připojení ke vzdálené ploše myVm.

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Pokud již není potřeba, můžete pomocí odstranění skupiny AZ odebrat skupinu prostředků a všechny prostředky, které má: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Další kroky
Další informace o [tom, co je privátní koncový bod Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
