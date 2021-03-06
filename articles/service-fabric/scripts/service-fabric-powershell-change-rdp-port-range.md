---
title: Ukázkový skript Azure PowerShellu – Změna rozsahu portů RDP | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Změna rozsahu portů RDP u nasazeného clusteru
services: service-fabric
tags: azure-service-management
author: athinanthny
ms.author: atsenthi
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.custom: sfrev
ms.openlocfilehash: b3d922cb5a7cdf34e63731ab32f21c94d7b6bca0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76025017"
---
# <a name="update-the-rdp-port-range-values"></a>Aktualizace hodnot rozsahu portů RDP

Tento ukázkový skript po nasazení clusteru změní hodnoty rozsahu portů RDP na virtuálních počítačích uzlu clusteru.  Aby se základní virtuální počítače nezacyklily, používá se Azure PowerShell.  Skript získá `Microsoft.Network/loadBalancers` prostředek ve skupině prostředků clusteru `inboundNatPools.frontendPortRangeStart` a `inboundNatPools.frontendPortRangeEnd` aktualizuje hodnoty a. Podle potřeby upravte parametry.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview).

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzZdroj](/powershell/module/az.resources/get-azresource) | Získá prostředek `Microsoft.Network/loadBalancers`. |
|[Zdroj set-az](/powershell/module/az.resources/set-azresource)|Aktualizuje prostředek `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
