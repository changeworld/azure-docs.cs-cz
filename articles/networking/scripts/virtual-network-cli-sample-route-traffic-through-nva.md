---
title: Ukázka skriptu Azure CLI – směrování provozu prostřednictvím síťového virtuálního zařízení
description: Ukázkový skript Azure CLI – Směrování provozu přes síťové virtuální zařízení brány firewall
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 05581114ce54ed8e92c6457c95f73b20304e419e
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521515"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Směrování provozu přes síťové virtuální zařízení

Tento ukázkový skript vytvoří virtuální síť s front-endovou a back-endovou podsítí. Vytvoří také virtuální počítač s povoleným předáváním IP pro směrování provozu mezi těmito dvěma podsítěmi. Po spuštění skriptu budete na virtuální počítač moct nasadit síťový software, například aplikaci brány firewall.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Ukázkový skript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuální sítě a skupin zabezpečení sítě používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](/cli/azure/network/vnet) | Vytvoří virtuální síť Azure a front-endovou podsíť. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Vytvoří back-endovou podsíť a podsíť DMZ. |
| [az network public-ip create](/cli/azure/network/public-ip) | Vytvoří veřejnou IP adresu pro přístup k virtuálnímu virtuálnímu počítačům z Internetu. |
| [az network nic create](/cli/azure/network/nic) | Vytvoří virtuální síťové rozhraní a povolí pro něj předávání IP. |
| [az network nsg create](/cli/azure/network/nsg) | Vytvoří skupinu zabezpečení sítě (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Vytvoří pravidla NSG, která povolí příchozí provoz na portech HTTP a HTTPS do virtuálního počítače. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet)| Přidruží k podsítím skupiny zabezpečení sítě a směrovací tabulky. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Vytvoří směrovací tabulku pro všechny trasy. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Vytvoří trasy pro směrování provozu mezi podsítěmi a Internetem prostřednictvím virtuálního soudu. |
| [az vm create](/cli/azure/vm) | Vytvoří virtuální počítač a připojí k němu síťovou kartu. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu. |
| [az group delete](/cli/azure/group) | Odstraní skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptů příkazového příkazu k síti najdete v [dokumentaci k přehledu sítí Azure](../cli-samples.md)
