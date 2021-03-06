---
title: 'Změna nastavení ip adresy brány: PowerShell'
description: Tento článek vás provede změnou předpon IP adres pro bránu místní sítě pomocí prostředí PowerShell
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: dbaef6e0c81a9230b24aa1e85e7fdc421444047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863991"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Úprava nastavení místní síťové brány pomocí PowerShellu

Někdy se změní nastavení místní síťové brány AddressPrefix nebo GatewayIPAddress. Tento článek ukazuje, jak změnit nastavení brány místní sítě. Tato nastavení můžete také upravit jinou metodou výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Portál Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Změna předpon IP adres

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Změna IP adresy brány

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Další kroky

Můžete ověřit připojení brány. Viz [Ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).