---
title: Vytvoření virtuálního počítače s Windows ze specializovaného virtuálního pevného disku v Azure
description: Vytvořte nový virtuální modul windows připojením specializovaného spravovaného disku jako disku operačního systému pomocí modelu nasazení Správce prostředků.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: fc157c2253a718860e028fa493574cb9aa2ccdf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243364"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Vytvoření virtuálního počítače s Windows ze specializovaného disku pomocí PowerShellu

Vytvořte nový virtuální počítače připojením specializovaného spravovaného disku jako disku operačního systému. Specializovaný disk je kopie virtuálního pevného disku (VHD) z existujícího virtuálního počítače, který obsahuje uživatelské účty, aplikace a další data o stavu z původního virtuálního počítače. 

Při použití specializovaného virtuálního pevného disku k vytvoření nového virtuálního počítače, nový virtuální počítač zachová název počítače původního virtuálního počítače. Ostatní informace specifické pro počítač jsou také uchovávány a v některých případech mohou tyto duplicitní informace způsobit problémy. Při kopírování virtuálního počítače mějte na paměti, na jaké typy informací specifických pro počítač vaše aplikace spoléhají.

Máte několik možností:
* [Použijte existující spravovaný disk](#option-1-use-an-existing-disk). Tato možnost je užitečná, pokud máte virtuální hod, který nefunguje správně. Virtuální ho můžete odstranit a pak znovu použít spravovaný disk k vytvoření nového virtuálního počítače. 
* [Nahrání virtuálního pevného disku](#option-2-upload-a-specialized-vhd) 
* [Kopírování existujícího virtuálního počítače Azure pomocí snímků](#option-3-copy-an-existing-azure-vm)

Portál Azure můžete taky použít k [vytvoření nového virtuálního počítače ze specializovaného virtuálního pevného disku](create-vm-specialized-portal.md).

Tento článek ukazuje, jak používat spravované disky. Pokud máte starší nasazení, které vyžaduje použití účtu úložiště, přečtěte si témat [u tématu Vytvoření virtuálního počítače ze specializovaného virtuálního pevného disku v účtu úložiště](sa-create-vm-specialized.md).

Doporučujeme omezit počet souběžných nasazení na 20 virtuálních počítače z jednoho virtuálního pevného disku nebo snímku. 

## <a name="option-1-use-an-existing-disk"></a>Možnost 1: Použití existujícího disku

Pokud jste měli virtuální hod, který jste odstranili a chcete znovu použít disk operačního systému k vytvoření nového virtuálního počítače, použijte [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Nyní můžete připojit tento disk jako disk operačního systému k [novému virtuálnímu počítače](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Možnost 2: Nahrání specializovaného virtuálního pevného disku

Virtuální pevný disk můžete nahrát ze specializovaného virtuálního počítače vytvořeného pomocí místního virtualizačního nástroje, jako je Hyper-V, nebo virtuálního počítače exportovaného z jiného cloudu.

### <a name="prepare-the-vm"></a>Příprava virtuálního počítače
K vytvoření nového virtuálního virtuálního virtuálního virtuálního mísy použijte virtuální pevný disk. 
  
  * [Příprava virtuálního pevného disku windows pro nahrání do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nezobecnit** virtuální ho pomocí Sysprep.
  * Odeberte všechny nástroje pro virtualizaci hosta a agenty, které jsou nainstalované na virtuálním počítači (například nástroje VMware).
  * Ujistěte se, že virtuální počítače je nakonfigurovaný pro získání IP adresy a nastavení DNS z DHCP. Tím zajistíte, že server získá ip adresu v rámci virtuální sítě při spuštění. 


### <a name="upload-the-vhd"></a>Nahrání virtuálního pevného disku

Nyní můžete nahrát virtuální pevný disk přímo na spravovaný disk. Pokyny najdete [v tématu Upload a VHD to Azure using Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Možnost 3: Kopírování existujícího virtuálního počítače Azure

Můžete vytvořit kopii virtuálního počítače, který používá spravované disky tím, že snímek virtuálního počítače a pak pomocí tohoto snímku vytvořit nový spravovaný disk a nový virtuální hod.

Pokud chcete zkopírovat existující virtuální hod do jiné oblasti, můžete použít azkopii k [vytvoření kopie disku v jiné oblasti](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Pořízení snímku disku operačního systému

Můžete pořizovat snímek celého virtuálního počítače (včetně všech disků) nebo pouze jednoho disku. Následující kroky ukazují, jak pořizovat snímek pouze disk operačního systému virtuálního počítače s rutinou [New-AzSnapshot.](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) 

Nejprve nastavte některé parametry. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Získejte objekt virtuálního soudu.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Získejte název disku operačního systému.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Vytvořte konfiguraci snímku. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Pořízení snímku.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Chcete-li tento snímek použít k vytvoření virtuálního počítače, `-AccountType Premium_LRS` který musí být vysoce výkonný, přidejte parametr do příkazu New-AzSnapshotConfig. Tento parametr vytvoří snímek tak, aby byl uložen jako spravovaný disk Premium. Prémiové spravované disky jsou dražší než standardní, takže před použitím tohoto parametru budete potřebovat premium.

### <a name="create-a-new-disk-from-the-snapshot"></a>Vytvoření nového disku ze snímku

Vytvořte spravovaný disk ze snímku pomocí [aplikace New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Tento příklad používá *myOSDisk* pro název disku.

Vytvořte novou skupinu prostředků pro nový virtuální virtuální soud.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Nastavte název disku operačního systému. 

```powershell
$osDiskName = 'myOsDisk'
```

Vytvořte spravovaný disk.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Vytvoření nového virtuálního virtuálního movitého virtuálního montova 

Vytvořte sítě a další prostředky virtuálních zařízení, které bude používat nový virtuální soud.

### <a name="create-the-subnet-and-virtual-network"></a>Vytvoření podsítě a virtuální sítě

Vytvořte [virtuální síť](../../virtual-network/virtual-networks-overview.md) a podsíť pro virtuální počítač.

1. Vytvořte podsíť. Tento příklad vytvoří podsíť s názvem *mySubNet*ve skupině prostředků *myDestinationResourceGroup*a nastaví předponu adresy podsítě na *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Vytvořte virtuální síť. Tento příklad nastaví název virtuální sítě na *myVnetName*, umístění na *západ USA*a předponu adresy pro virtuální síť na *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvoření skupiny zabezpečení sítě a pravidla protokolu RDP
Abyste se mohli přihlásit k virtuálnímu počítači pomocí protokolu vzdálené plochy (RDP), musíte mít pravidlo zabezpečení, které umožňuje přístup k protokolu RDP na portu 3389. V našem příkladu virtuální pevný disk pro nový virtuální počítač byl vytvořen z existujícího specializovaného virtuálního počítače, takže můžete použít účet, který existoval ve zdrojovém virtuálním počítači pro RDP.

Tento příklad nastaví název skupiny zabezpečení sítě (NSG) na *myNsg* a název pravidla RDP na *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Další informace o koncových bodech a pravidlech nsg najdete [v tématu Otevírání portů k virtuálnímu počítači v Azure pomocí PowerShellu](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Vytvoření veřejné IP adresy a nic
Chcete-li povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou IP adresu](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

1. Vytvořte veřejnou IP adresu. V tomto příkladu je název veřejné IP adresy nastaven na *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Vytvořte nic. V tomto příkladu je název nic nastaven na *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Nastavení názvu a velikosti virtuálního počítače

Tento příklad nastaví název virtuálního počítače na *myVM* a velikost virtuálního počítače na *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Přidání nic
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Přidání disku operačního systému 

Přidejte disk operačního systému do konfigurace pomocí [programu Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). Tento příklad nastaví velikost disku na *128 GB* a připojí spravovaný disk jako disk operačního systému *Windows.*
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Dokončení virtuálního virtuálního mísy 

Vytvořte virtuální počítače pomocí [Nového AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) s konfiguracemi, které jsme právě vytvořili.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Pokud je tento příkaz úspěšný, zobrazí se výstup takto:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Ověření, že byl virtuální virtuální hotel vytvořen
Nově vytvořený virtuální počítač byste měli vidět buď na [webu Azure portal](https://portal.azure.com) v části **Procházet** > **virtuální počítače**, nebo pomocí následujících příkazů Prostředí PowerShell.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Další kroky
Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [Jak se připojit a přihlásit k virtuálnímu počítači Azure se systémem Windows](connect-logon.md).

