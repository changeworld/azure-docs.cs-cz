---
title: 'Úvodní příručka: Vytvoření zóny Azure DNS a záznamu – Azure CLI'
titleSuffix: Azure DNS
description: Rychlý start – Naučíte se vytvořit zónu a záznam DNS v Azure DNS. Pomocí tohoto podrobného průvodce můžete vytvořit a spravovat první zónu a záznam DNS pomocí Azure CLI.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: e6904c013cf2ed897bdc7c8b32f04fe500fc31d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76937202"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Rychlý start: Vytvoření zóny Azure DNS a záznam pomocí Azure CLI

Tento článek vás provede kroky k vytvoření první zóny a záznamu DNS pomocí Azure CLI, které je dostupné pro Windows, Mac a Linux. Tyto kroky můžete provést také pomocí [portálu Azure nebo](dns-getstarted-portal.md) [Azure PowerShellu](dns-getstarted-powershell.md).

K hostování záznamů DNS pro konkrétní doménu se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Nakonec, pokud chcete zónu DNS publikovat na internetu, bude potřeba nakonfigurovat pro doménu názvové servery. Jednotlivé kroky jsou popsány níže.

Azure DNS také podporuje privátní zóny DNS. Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md). Příklad vytvoření privátní zóny DNS najdete v tématu [Začínáme s privátními zónami Azure DNS pomocí rozhraní příkazového řádku](./private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením zóny DNS vytvořte skupinu prostředků, která bude obsahovat zónu DNS.

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří příkazem `az network dns zone create`. Pokud chcete zobrazit nápovědu k tomuto příkazu, zadejte `az network dns zone create -h`.

Následující příklad vytvoří zónu DNS nazvanou *contoso.xyz* ve skupině prostředků *MyResourceGroup*. Nahraďte hodnoty vlastními a použijte tento příklad k vytvoření zóny DNS.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

K vytvoření záznamu DNS použijte příkaz `az network dns record-set [record type] add-record`. Nápovědu k záznamům A najdete v tématu `azure network dns record-set A add-record -h`.

Následující příklad vytvoří záznam s relativním názvem "www" v zóně DNS "contoso.xyz" ve skupině prostředků "MyResourceGroup". Plně kvalifikovaný název sady záznamů je "www.contoso.xyz". Typ záznamu je "A", s IP adresou "10.10.10.10" a výchozí TTL 3600 sekund (1 hodina).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Zobrazení záznamů

K výpisu záznamů DNS ve vaší zóně použijte následující příkaz:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>Testování překladu IP adres

Nyní, když máte testovací zónu DNS s testovacím záznamem "A", můžete otestovat překlad názvů pomocí nástroje s názvem *nslookup*. 

**Testování překladu názvů DNS:**

1. Spuštěním následující rutiny získáte seznam názvových serverů pro vaši zónu:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. Zkopírujte jeden z názvů názvového serveru z výstupu předchozího kroku.

1. Otevřete příkazový řádek a spusťte následující příkaz:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Například:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Měli byste vidět něco jako následující obrazovka:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Název hostitele **\.www contoso.xyz** se překládá na **10.10.10.10**, stejně jako jste ho nakonfigurovali. Tento výsledek ověří, že překlad názvů funguje správně.

## <a name="delete-all-resources"></a>Odstranění všech prostředků

Pokud už nejsou potřeba, můžete odstranit všechny prostředky vytvořené v rámci tohoto rychlého startu odstraněním skupiny prostředků:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili svoji první zónu a záznam DNS pomocí Azure CLI, můžete vytvořit záznamy pro webovou aplikaci ve vlastní doméně.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)
