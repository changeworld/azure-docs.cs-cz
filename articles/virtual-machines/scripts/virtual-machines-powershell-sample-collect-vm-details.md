---
title: Shromažďování podrobností o všech virtuálních aplikacích v předplatném pomocí PowerShellu
description: Shromažďování podrobností o všech virtuálních aplikacích v předplatném pomocí PowerShellu
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: dcscontentpm
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: 237081380445f2b2e4168ee3afe9a3ed7544fc89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900207"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>Shromažďování podrobností o všech virtuálních aplikacích v předplatném pomocí PowerShellu

Tento skript vytvoří csv, který obsahuje název virtuálního počítače, název skupiny prostředků, oblast, virtuální síť, podsíť, privátní IP adresu, typ operačního serveru a veřejnou IP adresu virtuálních počítačů v zadaném předplatném.

Pokud ještě nemáte [předplatné Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/powershell](https://shell.azure.com/powershell)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="sample-script"></a>Ukázkový skript

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

#Provide the name of the csv file to be exported
$reportName = "myReport.csv"

Select-AzSubscription $subscriptionId
$report = @()
$vms = Get-AzVM
$publicIps = Get-AzPublicIpAddress 
$nics = Get-AzNetworkInterface | ?{ $_.VirtualMachine -NE $null} 
foreach ($nic in $nics) { 
    $info = "" | Select VmName, ResourceGroupName, Region, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
    $vm = $vms | ? -Property Id -eq $nic.VirtualMachine.id 
    foreach($publicIp in $publicIps) { 
        if($nic.IpConfigurations.id -eq $publicIp.ipconfiguration.Id) {
            $info.PublicIPAddress = $publicIp.ipaddress
            } 
        } 
        $info.OsType = $vm.StorageProfile.OsDisk.OsType 
        $info.VMName = $vm.Name 
        $info.ResourceGroupName = $vm.ResourceGroupName 
        $info.Region = $vm.Location 
        $info.VirturalNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá následující příkazy k vytvoření csv export podrobností o virtuálních počítačích v předplatném. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

|Příkaz|Poznámky|
|-|-|
|[Select-AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Set-AzContext)|Nastaví klienta, předplatné a prostředí pro rutiny pro použití v aktuální relaci.|
|[Získat-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM)|Získá vlastnosti virtuálního počítače.|
|[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzPublicIpAddress)|Získá veřejnou IP adresu.|
|[Rozhraní Get-AzNetwork](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzNetworkInterface)|Získá síťové rozhraní.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/powershell-samples?toc=/azure/virtual-machines/windows/toc.json).


