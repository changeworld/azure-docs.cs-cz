---
title: Kurz – vytváření a správa virtuálních sítí Azure pro virtuální počítače s Windows
description: V tomto kurzu zjistíte, jak pomocí Azure PowerShellu vytvářet a spravovat virtuální sítě Azure pro virtuální počítače s Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 67cfb04f67e3454bde25969b634116f2871cbeb5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238339"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Kurz: Vytváření a správa virtuálních sítí Azure pro virtuální počítače s Windows pomocí Azure PowerShellu

Virtuální počítače Azure používají pro interní i externí síťovou komunikaci sítě Azure. Tento kurz vás provede nasazením dvou virtuálních počítačů a konfigurací sítě Azure pro tyto virtuální počítače. Příklady v tomto kurzu předpokládají, že virtuální servery jsou hostitelem webové aplikace s back-endem databáze, ale aplikace není nasazena v kurzu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě a podsítě
> * Vytvoření veřejné IP adresy
> * Vytvoření front-endového virtuálního počítače
> * Zabezpečení provozu sítě
> * Vytvoření back-endového virtuálního počítače


## <a name="vm-networking-overview"></a>Přehled sítí virtuálních počítačů

Virtuální sítě Azure umožňují zabezpečená síťová připojení mezi virtuálními počítači, internetem a dalšími službami Azure, jako je databáze Azure SQL. Virtuální sítě se skládají z logických segmentů zvaných podsítě. Podsítě se používají k řízení toku sítě a jako hranice zabezpečení. Když nasazujete virtuální počítač, obvykle zahrnuje virtuální síťové rozhraní, které je připojené k podsíti.

Při práci na tomto kurzu vytvoříte tyto prostředky:

![Virtuální síť se dvěma podsítěmi](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – virtuální síť, kterou virtuální počítače používají ke komunikaci mezi sebou a internetem.
- *myFrontendSubnet* – podsíť ve virtuální síti *myVNet*, kterou používají front-endové prostředky.
- *myPublicIPAddress* – veřejná IP adresa, pomocí které se můžete z internetu připojit k virtuálnímu počítači *myFrontendVM*.
- *myFrontendNic* – síťové rozhraní, které virtuální počítač *myFrontendVM* používá ke komunikaci s virtuálním počítačem *myBackendVM*.
- *myFrontendVM* – virtuální počítač, pomocí kterého můžete komunikovat mezi internetem a virtuálním počítačem *myBackendVM*.
- *myBackendNSG* – skupina zabezpečení sítě, která řídí komunikaci mezi virtuálními počítači *myFrontendVM* a *myBackendVM*.
- *myBackendSubnet* – podsíť přidružená ke skupině zabezpečení sítě *myBackendNSG*, kterou používají back-endové prostředky.
- *myBackendNic* – síťové rozhraní, které virtuální počítač *myBackendVM* používá ke komunikaci s virtuálním počítačem *myFrontendVM*.
- *myBackendVM* – virtuální počítač, který používá port 1433 ke komunikaci s virtuálním počítačem *myFrontendVM*.


## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/powershell](https://shell.azure.com/powershell)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.


## <a name="create-subnet"></a>Vytvoření podsítě 

V tomto kurzu vytvoříte virtuální síť se dvěma podsítěmi. Front-endovou podsíť k hostování webové aplikace a back-endovou podsíť pro hostování databázového serveru.

Před vytvořením virtuální sítě vytvořte skupinu prostředků pomocí [skupiny New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků *myRGNetwork* v umístění *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Vytvořte konfiguraci podsítě s názvem *myFrontendSubnet* pomocí [nástroje New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Vytvořte také konfiguraci podsítě *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s názvem *myVNet* pomocí *myFrontendSubnet* a *myBackendSubnet* pomocí [new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Nyní jste vytvořili síť rozdělenou do dvou podsítí – jedné pro front-endové služby a druhé pro back-endové služby. V další části vytvoříme virtuální počítače a připojíme je k těmto podsítím.

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Veřejná IP adresa umožňuje připojení k prostředkům Azure z internetu. Metodu přidělování veřejné IP adresy je možné nakonfigurovat dynamicky nebo staticky. Ve výchozím nastavení se veřejná IP adresa přiděluje dynamicky. Dynamické IP adresy se vydávají při zrušení přidělení virtuálního počítače. Toto chování znamená, že se IP adresa změní při libovolné operaci, která zahrnuje zrušení přidělení virtuálního počítače.

Metodu přidělení lze nastavit na statickou, což zajišťuje, že ip adresa zůstane přiřazena k virtuálnímu virtuálnímu soudu, a to i během vyrovnaný stav. Pokud používáte statickou ADRESU IP, nelze zadat samotnou adresu IP. Místo toho je přidělena z fondu dostupných adres.

Vytvořte veřejnou IP adresu s názvem *myPublicIPAddress* pomocí [new-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress):

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Pokud chcete přiřadit statickou veřejnou IP adresu, můžete změnit parametr -AllocationMethod na hodnotu `Static`.

## <a name="create-a-front-end-vm"></a>Vytvoření front-endového virtuálního počítače

Virtuální počítač ke komunikaci ve virtuální síti potřebuje virtuální síťové rozhraní. Vytvoření síťové síťové rozhraní pomocí [rozhraní New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Nastavte uživatelské jméno a heslo potřebné pro účet správce na virtuálním počítači pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Tyto přihlašovací údaje použijete k připojení k virtuálnímu počítači v dalších krocích:

```azurepowershell-interactive
$cred = Get-Credential
```

Vytvořte virtuální společnosti pomocí [nového AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Zabezpečení provozu sítě

Skupina zabezpečení sítě (NSG) obsahuje seznam pravidel zabezpečení, která prostředkům připojeným k virtuálním sítím Azure povolují nebo odpírají síťový provoz. Skupinu zabezpečení sítě můžete přidružit k podsítím nebo jednotlivým síťovým rozhraním. Skupina zabezpečení sítě je přidružena k síťovému rozhraní, které se vztahuje pouze na přidružený virtuální modul. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k příslušné podsíti.

### <a name="network-security-group-rules"></a>Pravidla skupiny zabezpečení sítě

Pravidla skupiny zabezpečení sítě definují síťové porty, které provoz buď povolují, nebo zakazují. Pravidla mohou zahrnovat zdrojové a cílové rozsahy IP adres, aby se provoz řídil mezi určitými systémy nebo podsítěmi. Pravidla skupiny zabezpečení sítě také obsahují prioritu (1–4096). Pravidla se vyhodnocují v pořadí podle priority. Pravidlo s prioritou 100 se vyhodnotí před pravidlem s prioritou 200.

Všechny skupiny NSG obsahují sadu výchozích pravidel. Výchozí pravidla nelze odstranit, ale protože jsou přiřazena nejnižší priorita, mohou být přepsána pravidly, která vytvoříte.

- **Virtuální síť** – provoz směřující z virtuální sítě a do ní je povolený v příchozím i odchozím směru.
- **Internet** – odchozí provoz je povolený, ale příchozí provoz se blokuje.
- **Nástroj pro vyrovnávání zatížení** – umožňuje nástroji pro vyrovnávání zatížení Azure testovat stav virtuálních počítačů a instancí rolí. Pokud sadu s vyrovnáváním zatížení nepoužíváte, můžete toto pravidlo přepsat.

### <a name="create-network-security-groups"></a>Vytvoření skupin zabezpečení sítě

Vytvořte příchozí pravidlo s názvem *myFrontendNSGRule,* které povolí příchozí webový provoz v *myFrontendVM* pomocí [funkce New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Vytvořením skupiny zabezpečení sítě pro back-endovou podsíť můžete omezit zdroj interního provozu do virtuálního počítače *myBackendVM* pouze na virtuální počítač *myFrontendVM*. Následující příklad vytvoří pravidlo NSG *myBackendNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Přidejte skupinu zabezpečení sítě s názvem *myFrontendNSG* pomocí [new-aznetworksecuritygroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Nyní přidejte skupinu zabezpečení sítě s názvem *myBackendNSG* pomocí new-aznetworksecuritygroup:

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Přidejte skupiny zabezpečení sítě do podsítí:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Vytvoření back-endového virtuálního počítače

Nejjednodušším způsobem, jak vytvořit back-endový virtuální počítač pro účely tohoto kurzu, je pomocí image SQL Serveru. V tomto kurzu se pouze vytvoří virtuální počítač s databázovým serverem, ale informace o přístupu k databázi kurz neobsahuje.

Vytvořte virtuální síťové rozhraní *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Nastavte uživatelské jméno a heslo potřebné pro účet správce na virtuálním počítači pomocí rutiny Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Vytvořte virtuální počítač *myBackendVM*.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

Bitová kopie v tomto příkladu má nainstalovaný SQL Server, ale v tomto kurzu se nepoužívá. Je součástí, aby vám ukázal, jak můžete nakonfigurovat virtuální ho virtuálního počítače pro zpracování webového provozu a virtuálního počítače pro zpracování správy databáze.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste v souvislosti s virtuálními počítači vytvořili a zabezpečené sítě Azure. 

> [!div class="checklist"]
> * Vytvoření virtuální sítě a podsítě
> * Vytvoření veřejné IP adresy
> * Vytvoření front-endového virtuálního počítače
> * Zabezpečení provozu sítě
> * Vytvoření back-endového virtuálního počítače

V dalším kurzu se seznámíte s monitorováním zabezpečení dat na virtuálních počítačích pomocí služby Azure Backup.

> [!div class="nextstepaction"]
> [Zálohování virtuálních počítačů s Windows v Azure](./tutorial-backup-vms.md)
