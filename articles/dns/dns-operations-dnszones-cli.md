---
title: Správa zón DNS v Azure DNS – Azure CLI | Dokumenty společnosti Microsoft
description: Zóny DNS můžete spravovat pomocí azure cli. Tento článek ukazuje, jak aktualizovat, odstranit a vytvořit zóny DNS na Azure DNS.
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: rohink
ms.openlocfilehash: 413c2ab3ee04249c2bb52bf42ca6a31a58fb9082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936930"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Jak spravovat zóny DNS v Azure DNS pomocí azure cli

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)


Tato příručka ukazuje, jak spravovat zóny DNS pomocí rozhraní API Azure napříč platformami, které je dostupné pro Windows, Mac a Linux. Zóny DNS můžete taky spravovat pomocí [Azure PowerShellu](dns-operations-dnszones.md) nebo portálu Azure.

Tato příručka se konkrétně zabývá veřejnými zónami DNS. Informace o použití azure cli ke správě privátních zón ve Službě Azure DNS najdete v [tématu Začínáme s privátními zónami Azure DNS pomocí azure cli](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Úvod

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Nastavení rozhraní příkazového řádku Azure pro Azure DNS

### <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, zda máte následující.

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Nainstalujte nejnovější verzi rozhraní příkazového řádku Azure, která je dostupná pro Windows, Linux a Mac. Další informace najdete v tématu [Instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Přihlášení k účtu Azure

Otevřete okno konzoly a proveďte ověření pomocí svých přihlašovacích údajů. Další informace najdete v tématu [Přihlášení do Azure z azure cli](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```
az login
```

### <a name="select-the-subscription"></a>Výběr předplatného

Zkontrolujte předplatná pro příslušný účet.

```
az account list
```

Zvolte předplatné Azure, které chcete použít.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature"></a>Volitelné: Instalace a použití funkce Privátní zóny Azure DNS
Funkce Azure DNS Private Zone je dostupná prostřednictvím rozšíření o rozhraní příkazového příkazu Azure. Nainstalujte rozšíření Azure CLI dns. 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. To slouží jako výchozí umístění pro prostředky v příslušné skupině prostředků. Všechny prostředky DNS jsou ale globální, a ne místní, takže volba umístění skupiny prostředků nemá na Azure DNS žádný vliv.

Pokud používáte některou ze stávajících skupin prostředků, můžete tento krok přeskočit.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Získání nápovědy

Všechny příkazy Azure CLI vztahující se `az network dns`k Azure DNS začínají . Nápověda je k dispozici `--help` pro každý `-h`příkaz pomocí možnosti (krátký formulář).  Například:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří příkazem `az network dns zone create`. Nápovědu získáte příkazem `az network dns zone create -h`.

Následující příklad vytvoří zónu DNS nazvanou *contoso.com* ve skupině prostředků s názvem *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Vytvoření zóny DNS se značkami

Následující příklad ukazuje, jak vytvořit zónu DNS se [dvěma značkami Azure Resource Manager](dns-zones-records.md#tags), project = *demo* a *env = test*pomocí parametru `--tags` (krátký tvar `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Získání zóny DNS

Chcete-li načíst `az network dns zone show`zónu DNS, použijte . Nápovědu získáte příkazem `az network dns zone show --help`.

Následující příklad vrátí zónu DNS *contoso.com* a její přidružená data ze skupiny prostředků *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

Dalším příkladem je tato odpověď.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Všimněte si, že příkaz `az network dns zone show` nevrací záznamy DNS. Pokud chcete vypsat záznamy DNS, použijte příkaz `az network dns record-set list`.


## <a name="list-dns-zones"></a>Výpis zón DNS

Pokud chcete zobrazit výčet zón DNS, použijte příkaz `az network dns zone list`. Nápovědu získáte příkazem `az network dns zone list --help`.

Zadáním skupiny prostředků můžete vypsat pouze zóny v rámci této skupiny prostředků:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Pokud skupinu prostředků vynecháte, vypíší se všechny zóny v předplatném:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Aktualizace zóny DNS

Změny prostředku zóny DNS je možné provádět pomocí příkazu `az network dns zone update`. Nápovědu získáte příkazem `az network dns zone update --help`.

Tento příkaz neaktualizuje žádné sady záznamů DNS v rámci zóny (viz [Správa záznamů DNS](dns-operations-recordsets-cli.md)). Slouží pouze k aktualizaci vlastností samotného prostředku zóny. Tyto vlastnosti jsou aktuálně omezeny na ["značky" Azure Resource Manager](dns-zones-records.md#tags) pro prostředek zóny.

Následující příklad ukazuje, jak aktualizovat značky v zóně DNS. Existující značky jsou nahrazeny zadanou hodnotou.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Odstranění zóny DNS

Zóny DNS je možné odstranit pomocí příkazu `az network dns zone delete`. Nápovědu získáte příkazem `az network dns zone delete --help`.

> [!NOTE]
> Odstraněním zóny DNS dojde také k odstranění všech záznamů DNS v rámci dané zóny. Tato operace se nedá vrátit zpět. Pokud se zóna DNS používá, služby využívající tuto zónu při jejím odstranění selžou.
>
>Informace o ochraně před náhodným odstraněním zóny najdete v tématu [Ochrana záznamů a zón DNS](dns-protect-zones-recordsets.md).

Tento příkaz zobrazí výzvu k potvrzení. Volitelný přepínač `--yes` tuto výzvu potlačí.

Následující příklad ukazuje, jak odstranit *contoso.com* zóny ze skupiny prostředků *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [spravovat sady záznamů a záznamy](dns-getstarted-create-recordset-cli.md) v zóně DNS.

Přečtěte si, jak [delegovat doménu na Azure DNS](dns-domain-delegation.md).

