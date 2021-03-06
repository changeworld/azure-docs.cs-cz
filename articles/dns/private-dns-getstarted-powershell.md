---
title: Úvodní příručka – vytvoření privátní zóny DNS Azure pomocí Azure PowerShellu
description: V tomto článku vytvoříte a otestujete privátní zónu DNS a záznam v Azure DNS. Tento podrobný průvodce vám pomůže vytvořit a spravovat první privátní zónu a záznam DNS pomocí Azure PowerShellu.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 0db53bcd6516bd52e2796deaa49fe0dd582e0588
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76939396"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>Úvodní příručka: Vytvoření privátní zóny DNS Azure pomocí Azure PowerShellu

Tento článek vás provede kroky k vytvoření první privátní zóny a záznamu DNS pomocí Azure PowerShellu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K hostování záznamů DNS pro konkrétní doménu se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do virtuální sítě, zadáte seznam virtuálních sítí, které mají povoleno překládat záznamy v rámci této zóny.  Tyto sítě se nazývají *propojené* virtuální sítě. Pokud je povolena automatická registrace, Azure DNS také aktualizuje záznamy zóny při každém vytvoření virtuálního počítače, změní jeho IP adresu nebo se odstraní.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření privátní zóny DNS
> * Vytvoření testovacích virtuálních počítačů
> * Vytvoření dalšího záznamu DNS
> * Testování privátní zóny

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

Pokud chcete, můžete tento rychlý start dokončit pomocí [azure cli](private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Nejdřív vytvořte skupinu prostředků, která bude obsahovat zónu DNS: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-private-dns-zone"></a>Vytvoření privátní zóny DNS

Zóna DNS se vytvoří pomocí rutiny `New-AzPrivateDnsZone`.

Následující příklad vytvoří virtuální síť s názvem **myAzureVNet**. Potom vytvoří zónu DNS s názvem **private.contoso.com** ve skupině prostředků **MyAzureResourceGroup,** propojí zónu DNS s virtuální sítí **MyAzureVnet** a povolí automatickou registraci.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Pokud chcete vytvořit zónu pouze pro překlad názvů (bez automatické registrace `-EnableRegistration` názvu hostitele), můžete parametr vynechat.

### <a name="list-dns-private-zones"></a>Výpis privátních zón DNS

Vynecháním názvu zóny v rutině `Get-AzPrivateDnsZone` můžete zobrazit výčet všech zón ve skupině prostředků. Tato operace vrátí pole objektů zón.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Vynecháním názvu zóny i názvu skupiny prostředků v rutině `Get-AzPrivateDnsZone` můžete zobrazit výčet všech zón v předplatném Azure.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

Teď vytvořte dva virtuální počítače, abyste mohli privátní zónu DNS otestovat:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Dokončení tohoto procesu může několik minut trvat.

## <a name="create-an-additional-dns-record"></a>Vytvoření dalšího záznamu DNS

Sady záznamů vytvoříte pomocí rutiny `New-AzPrivateDnsRecordSet`. Následující příklad vytvoří záznam s relativním názvem **db** v **zóně**DNS private.contoso.com ve skupině prostředků **MyAzureResourceGroup**. Plně kvalifikovaný název sady záznamů je **db.private.contoso.com**. Typ záznamu je A, IP adresa je 10.2.0.4 a hodnota TTL je 3600 sekund.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Zobrazení záznamů DNS

K výpisu záznamů DNS ve vaší zóně použijte následující příkaz:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>Testování privátní zóny

Nyní můžete otestovat překlad názvů pro **private.contoso.com** soukromé zóny.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurace virtuálních počítačů pro povolení příchozích přenosů ICMP

Překlad adres můžete otestovat pomocí příkazu ping. Za tím účelem nakonfigurujte bránu firewall na obou virtuálních počítačích tak, aby povolovala příchozí pakety ICMP.

1. Připojte se k počítači myVM01 a otevřete okno Windows PowerShellu s oprávněními správce.
2. Spusťte následující příkaz:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Totéž zopakujte pro virtuální počítač myVM02.

### <a name="ping-the-vms-by-name"></a>Odeslání příkazu ping na virtuální počítače podle názvu

1. Z příkazového řádku ve Windows PowerShellu virtuálního počítače myVM02 odešlete příkaz ping do virtuálního počítače myVM01 a použijte v něm automaticky zaregistrovaný název hostitele:

   ```
   ping myVM01.private.contoso.com
   ```

   Zobrazený výstup by měl vypadat zhruba takto:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Teď odešlete příkaz ping na název **db**, který jste předtím vytvořili:

   ```
   ping db.private.contoso.com
   ```

   Zobrazený výstup by měl vypadat zhruba takto:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Odstranění všech prostředků

Pokud už není potřeba, odstraňte skupinu prostředků **MyAzureResourceGroup** a odstraňte prostředky vytvořené v tomto článku.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Scénáře pro Azure DNS Private Zones](private-dns-scenarios.md)
