---
title: Nasazení runtime integrace Azure SSIS pomocí Prostředí PowerShell
description: Tento skript prostředí PowerShell vytvoří runtime integrace Azure-SSIS, který může spouštět balíčky SSIS v cloudu.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: sawinark
author: swinarko
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c6d9a9299fa25013a440ee6ac45f5eae407225b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929807"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>Skript PowerShellu – nasazení za běhu integrace Azure-SSIS

Tento ukázkový skript PowerShellu vytvoří runtime integrace Azure-SSIS, který může spouštět balíčky SSIS v Azure.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní spojené prostředky:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Chcete-li odebrat objekt pro vytváření dat ze skupiny prostředků, spusťte následující příkaz: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Sada-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Vytvoření datové továrny |
| [Set-AzDataFactoryV2IntegrationDoba běhu](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Vytvoří runtime integrace Azure-SSIS, který může spouštět balíčky SSIS v cloudu. |
| [Start-AzDataFactoryV2IntegrationDoba běhu](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Spustí runtime integrace Azure-SSIS. |
| [Get-AzDataFactoryV2IntegrationDoba běhu](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Získá informace o prostředí integrace Azure-SSIS. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Factory PowerShell unavují [ukázky prostředí Azure Data Factory.](../samples-powershell.md)
