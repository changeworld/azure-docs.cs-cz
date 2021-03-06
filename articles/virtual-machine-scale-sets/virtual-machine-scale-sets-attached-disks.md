---
title: Připojené datové disky škálovací sady virtuálních počítačů Azure
description: Naučte se používat připojené datové disky se škálovacími sadami virtuálních počítačů prostřednictvím obrysů konkrétních případů použití.
author: avirishuv
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 4/25/2017
ms.author: avverma
ms.openlocfilehash: 6e39a8ffb24b0cca720890e3d00a55d1e58fadc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123378"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Škálovací sady virtuálních počítačů Azure a připojené datové disky
[Škálovací sady virtuálních počítačů](/azure/virtual-machine-scale-sets/) Azure podporují instance virtuálních počítačů s připojenými datovými disky a umožňují tak rozšíření úložiště, které máte k dispozici. Datové disky můžete připojit při vytváření škálovací sady nebo k existující škálovací sadě.

> [!NOTE]
> Když vytvoříte škálovací sadu s připojenými datovými disky, před jejich použitím musíte disky připojit a naformátovat na virtuálním počítači (stejně jako u samostatných virtuálních počítačů Azure). Praktický způsob, jak to provést, je použít rozšíření vlastních skriptů, které volá skript, který všechny datové disky virtuálního počítače rozdělí do oddílů a naformátuje je. Tady najdete příklady pro [Azure CLI a ](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Vytváření a správa disků ve škálovací sadě
Podrobné informace o vytvoření škálovací sady s připojenými datovými disky a o přípravě, formátování, přidávání a odebírání datových disků najdete v následujících kurzech:

- [Azure CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

Zbývající část tohoto článku popisuje konkrétní případy použití, jako jsou například clustery Service Fabric vyžadující datové disky nebo připojení existujících datových disků s obsahem ke škálovací sadě.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Vytvoření clusteru Service Fabric s připojenými datovými disky
Každý [typ uzlu](../service-fabric/service-fabric-cluster-nodetypes.md) v clusteru [Service Fabric](/azure/service-fabric) spuštěném v Azure využívá škálovací sadu virtuálních počítačů. Pomocí šablony Azure Resource Manageru můžete připojit datové disky ke škálovacím sadám, ze kterých se skládá cluster Service Fabric. Jako výchozí bod můžete použít [existující šablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates). V šabloně do části _storageProfile_ prostředků _Microsoft.Compute/virtualMachineScaleSets_ vložte část _dataDisks_ a pak šablonu nasaďte. Následující příklad připojí 128GB datový disk:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Při nasazování clusteru můžete datové disky automaticky dělit, formátovat a připojovat. Do části _extensionProfile_ profilu _virtualMachineProfile_ škálovacích sad přidejte rozšíření vlastních skriptů.

Pokud chcete automaticky připravovat datové disky v clusteru s Windows, přidejte následující:

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Pokud chcete automaticky připravovat datové disky v clusteru s Linuxem, přidejte následující:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Přidání disků předem naplněných daty do existující škálovací sady
Datové disky zadané v modelu škálovací sady jsou vždy prázdné. Můžete ale přiřadit existující datové disky ke konkrétnímu virtuálnímu počítači ve škálovací sadě. Pokud chcete šířit data mezi všemi virtuálními počítači ve škálovací sadě, můžete duplikovat datový disk a připojit ho ke každému virtuálnímu počítači ve škálovací sadě nebo vytvořit vlastní bitovou kopii, která obsahuje data a zřídí škálovací sadu z této vlastní bitové kopie , nebo můžete použít Soubory Azure nebo podobnou nabídku úložiště dat.


## <a name="additional-notes"></a>Další poznámky
Podpora Spravovaných disků Azure a připojených datových disků škálovacích sad je dostupná v rozhraní Microsoft.Compute API verze [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) nebo novější.

Podpora portálu Azure pro připojené datové disky ve škálovacích sadách je omezená. V závislosti na požadavcích můžete ke správě připojených disků použít šablony Azure, rozhraní příkazového řádku, PowerShell, sady SDK nebo rozhraní REST API.


