---
title: Aktualizace uživatelského jména a hesla RDP v Prostředí PowerShell
description: Ukázkový skript Azure PowerShellu – Aktualizace uživatelského jména a hesla protokolu RDP pro všechny uzly clusteru Service Fabric konkrétního typu
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: b7f229162ed745408121d898f5af516d22a0c039
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984925"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Aktualizace uživatelského jména a hesla správce virtuálních počítačů v clusteru

Každý [typ uzlu](../service-fabric-cluster-nodetypes.md) v clusteru Service Fabric je škálovací sada virtuálních počítačů. Tento ukázkový skript aktualizuje uživatelské jméno a heslo správce virtuálních počítačů v clusteru na konkrétním typu uzlu.  Přidejte do škálovací sady rozšíření VMAccessAgent, protože heslo správce není upravitelná vlastnost škálovací sady.  Změny uživatelského jména a hesla se použijí pro všechny uzly ve škálovací sadě. Podle potřeby upravte parametry.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Získat-AzVmss](/powershell/module/az.compute/get-azvmss) | Získá vlastnosti typu uzlu clusteru (škálovací sada virtuálních počítačů).   |
| [Přidat-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Přidá do škálovací sady virtuálních počítačů rozšíření.|
| [Aktualizace-AzVmss](/powershell/module/az.compute/update-azvmss)|Aktualizuje stav škálovací sady virtuálních počítačů na stav místního objektu škálovací sady virtuálních počítačů.|

## <a name="duration"></a>Doba trvání

Jeden typ uzlu s pěti uzly, například má dobu trvání 45 až 60 minut změnit uživatelské jméno nebo heslo. 

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
