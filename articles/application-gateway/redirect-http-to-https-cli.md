---
title: Přesměrování protokolu HTTP na https pomocí funkce CLI
titleSuffix: Azure Application Gateway
description: Zjistěte, jak vytvořit aplikační bránu a přidat certifikát pro ukončení TLS pomocí azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/15/2019
ms.author: victorh
ms.openlocfilehash: 6bf8f3b7bfb446db78f0c97a246977fec6cd54cb
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312144"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Vytvoření aplikační brány s přesměrováním HTTP na HTTPS pomocí azure cli

Pomocí azure cli můžete vytvořit [aplikační bránu](overview.md) s certifikátem pro ukončení TLS/SSL. Pravidlo směrování se používá k přesměrování přenosu HTTP na port HTTPS v aplikační bráně. V tomto příkladu také vytvoříte [škálovací sadu virtuálních strojů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pro back-endový fond aplikační brány, který obsahuje dvě instance virtuálního počítače.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
> * Nastavit síť
> * Vytvořit aplikační bránu s certifikátem
> * Přidání naslouchací proces a pravidlo přesměrování
> * Vytvořit škálovací sadu virtuálních počítačů s výchozím back-endovým fondem

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

Pro použití v produkčním prostředí byste měli importovat platný certifikát podepsaný důvěryhodným poskytovatelem. Pro účely tohoto kurzu vytvoříte certifikát podepsaný svým držitelem (self-signed certificate) a soubor pfx pomocí příkazu openssl.

```console
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Zadejte požadované hodnoty certifikátu. Můžete také použít výchozí hodnoty.

```console
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Zadejte heslo pro certifikát. V tomto příkladu se používá *Azure123456!* .

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pomocí příkazu [az group create](/cli/azure/group) vytvořte skupinu prostředků.

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Pomocí příkazu [az network vnet create](/cli/azure/network/vnet) vytvořte virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet*. Potom můžete přidat podsíť s názvem *myBackendSubnet*, kterou potřebují back-endové servery. Použijte k tomu příkaz [az network vnet subnet create](/cli/azure/network/vnet/subnet). Pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip) vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress*.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

K vytvoření aplikační brány s názvem *myAppGateway* použijte příkaz [az network application-gateway create](/cli/azure/network/application-gateway#az-network-application-gateway-create). Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. 

Aplikační brána je přiřazena k již vytvořené podsíti *myAGSubnet* a adrese *myAGPublicIPAddress*. Při vytváření aplikační brány v tomto příkladu přidružíte certifikát, který jste vytvořili, a heslo. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Vytvoření aplikační brány může trvat několik minut. Po vytvoření aplikační brány se zobrazí tyto její nové funkce:

- *appGatewayBackendPool* – aplikační brána musí mít aspoň jeden back-endový fond adres.
- *appGatewayBackendHttpSettings* – určuje, že se ke komunikaci používá port 80 a protokol HTTP.
- *appGatewayHttpListener* – výchozí naslouchací proces přidružený k *appGatewayBackendPool*.
- *appGatewayFrontendIP* – přiřadí adresu *myAGPublicIPAddress* naslouchacímu procesu *appGatewayHttpListener*.
- *rule1* – výchozí pravidlo směrování přidružené k naslouchacímu procesu *appGatewayHttpListener*.

## <a name="add-a-listener-and-redirection-rule"></a>Přidání naslouchací proces a pravidlo přesměrování

### <a name="add-the-http-port"></a>Přidání portu HTTP

K přidání portu HTTP do aplikační brány můžete použít [vytvoření front-portu síťové aplikace az.](/cli/azure/network/application-gateway/frontend-port#az-network-application-gateway-frontend-port-create)

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>Přidání naslouchací proces HTTP

Můžete použít [az síťové aplikace-gateway http-listener vytvořit](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) přidat naslouchací proces s názvem *myListener* do aplikační brány.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Přidání konfigurace přesměrování

Přidejte konfiguraci přesměrování PROTOKOLU HTTP do služby PŘEsměrovávací systém HTTPS do brány aplikace pomocí [vytvoření přesměrování az network application-gateway .](/cli/azure/network/application-gateway/redirect-config#az-network-application-gateway-redirect-config-create)

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>Přidání pravidla směrování

Přidejte pravidlo směrování s názvem *rule2* s konfigurací přesměrování na aplikační bránu pomocí [vytvoření pravidla brány sítě az](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

V tomto příkladu vytvoříte škálovací sadu virtuálních strojů s názvem *myvmss,* která poskytuje servery pro back-endový fond v bráně aplikace. Virtuální počítače jsou ve škálovací sadě přidruženy k podsíti *myBackendSubnet* a back-endovému fondu *appGatewayBackendPool*. K vytvoření škálovací sady použijte příkaz [az vmss create](/cli/azure/vmss#az-vmss-create).

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Instalace serveru NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

K získání veřejné IP adresy aplikační brány můžete použít příkaz [az network public-ip show](/cli/azure/network/public-ip). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Zabezpečené upozornění](./media/redirect-http-to-https-cli/application-gateway-secure.png)

Pokud chcete přijímat upozornění zabezpečení v případě použití certifikátu podepsaného svým držitelem (self-signed certificate), vyberte **Podrobnosti** a potom **Pokračovat na web**. Následně se zobrazí váš zabezpečený web NGINX, jak je znázorněno v následujícím příkladu:

![Testování základní adresy URL v aplikační bráně](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
> * Nastavit síť
> * Vytvořit aplikační bránu s certifikátem
> * Přidání naslouchací proces a pravidlo přesměrování
> * Vytvořit škálovací sadu virtuálních počítačů s výchozím back-endovým fondem


