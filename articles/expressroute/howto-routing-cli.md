---
title: 'Azure ExpressRoute: Konfigurace partnerského vztahu: rozhraní příkazového příkazu'
description: Tento článek vám pomůže vytvořit a zřídit soukromý, veřejný a partnerský vztah Microsoftu okruhu ExpressRoute. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro váš okruh, aktualizovat je nebo je odstranit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 91a1b6cc877b31fbcef638e34d3147d3377ce85c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476113"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute pomocí cli

Tento článek vám pomůže vytvořit a spravovat konfiguraci směrování nebo partnerský vztah pro okruh ExpressRoute v modelu nasazení Správce prostředků pomocí cli. Můžete také zkontrolovat stav, aktualizovat nebo odstranit a zrušit poskytování partnerských vztahu pro okruh ExpressRoute. Pokud chcete pro práci s okruhem použít jinou metodu, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Veřejný partnerský vztah](about-public-peering.md)
> * [Video - Soukromý partnerský vztah](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI](/cli/azure/install-azure-cli).
* Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md), [požadavky na směrování](expressroute-routing.md)a stránky [pracovního postupu.](expressroute-workflows.md)
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být v zřízeném a povoleném stavu, abyste mohli spouštět příkazy v tomto článku.

Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IPVPN, jako je MPLS), váš poskytovatel připojení nakonfiguruje a spravuje směrování za vás.

Můžete nakonfigurovat privátní partnerský vztah a partnerský vztah Microsoftu pro okruh ExpressRoute (veřejný partnerský vztah Azure se nezastaral pro nové okruhy). Partnerských stran lze nakonfigurovat v libovolném pořadí, které zvolíte. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o směrovacích doménách a partnerských serverech naleznete v tématu [Směrovací domény ExpressRoute](expressroute-circuit-peerings.md). Informace o veřejném partnerského vztahu naleznete v tématu [ExpressRoute public peering](about-public-peering.md).

## <a name="microsoft-peering"></a><a name="msft"></a>Partnerský vztah Microsoftu

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Microsoft peering obvodů ExpressRoute, které byly nakonfigurovány před 1. Microsoft peering obvodů ExpressRoute, které jsou nakonfigurovány na nebo po srpnu 1, 2017 nebude mít žádné předpony inzerované, dokud není připojen filtr trasy k okruhu. Další informace naleznete [v tématu Konfigurace filtru postupu pro partnerský vztah společnosti Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Nainstalujte nejnovější verzi azure cli. Použijte nejnovější verzi rozhraní příkazového řádku Azure (CLI). Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovní postupy.](expressroute-workflows.md)

   ```azurecli
   az login
   ```

   Vyberte předplatné, pro které chcete vytvořit okruh ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Vytvořte okruh ExpressRoute. Podle pokynů vytvořte [okruh ExpressRoute](howto-circuit-cli.md) a mějte ho zřízený poskytovatelem připojení. Pokud váš poskytovatel připojení nabízí spravované služby Vrstvy 3, můžete požádat svého poskytovatele připojení o povolení partnerského vztahu Microsoftu za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud však váš poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte v konfiguraci pomocí dalších kroků. 

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízena a také povolena. Použijte následující příklad:

   ```azurecli
   az network express-route list
   ```

   Odpověď bude podobná jako v následujícím příkladu:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Nakonfigurujte partnerský vztah Microsoftu pro okruh. Před pokračováním se ujistěte, že máte k dispozici následující informace.

   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud plánujete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
   * **Nepovinné -** Číslo ASN odběratele: Pokud inzerujete předpony, které nejsou registrovány na číslo AS partnerského vztahu, můžete zadat číslo AS, na které jsou registrovány.
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * **Nepovinné -** Hash MD5, pokud se rozhodnete použít jeden.

   Spusťte následující příklad konfigurace partnerského vztahu Microsoftu pro váš okruh:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Zobrazení podrobností partnerského vztahu Microsoftu

Podrobnosti o konfiguraci můžete získat pomocí následujícího příkladu:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Společnost Microsoft ověří, zda jsou v registru směrování v Internetu přiřazeny zadané předpony inzerované veřejné a peer asn (nebo "ASN zákazníka". Pokud získáváte veřejné předpony z jiné entity a přiřazení není zaznamenáno v registru směrování, automatické ověření nebude dokončeno a bude vyžadovat ruční ověření. Pokud se automatické ověření nezdaří, zobrazí se "AdvertisedPublicPrefixesState" jako "Ověření potřebné" na výstupu výše uvedeného příkazu. 
> 
> Pokud se zobrazí zpráva "Ověření je potřeba", shromážděte dokumenty, které ukazují, že veřejné předpony jsou přiřazeny vaší organizaci entitou, která je uvedena jako vlastník předpon v registru směrování, a odešlete tyto dokumenty k ručnímu ověření otevření lístku podpory, jak je uvedeno níže. 
> 
>

Výstup se podobá následujícímu příkladu:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Můžete aktualizovat libovolnou část konfigurace. Inzerované předpony obvodu jsou aktualizovány z 123.1.0.0/24 na 124.1.0.0/24 v následujícím příkladu:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>Přidání nastavení partnerského vztahu Microsoftu iPv6 do existující konfigurace Protokolu IPv4

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat spuštěním následujícího příkladu:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="azure-private-peering"></a><a name="private"></a>Soukromý partnerský vztah Azure

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci privátního partnerského vztahu Azure pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Nainstalujte nejnovější verzi azure cli. Musíte použít nejnovější verzi rozhraní příkazového řádku Azure (CLI).* Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovní postupy](expressroute-workflows.md) před zahájením konfigurace.

   ```azurecli
   az login
   ```

   Vyberte předplatné, pro které chcete vytvořit okruh ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Vytvořte okruh ExpressRoute. Podle pokynů vytvořte [okruh ExpressRoute](howto-circuit-cli.md) a mějte ho zřízený poskytovatelem připojení. Pokud váš poskytovatel připojení nabízí spravované služby Vrstvy 3, můžete požádat svého poskytovatele připojení o povolení privátního partnerského vztahu Azure. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud však váš poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte v konfiguraci pomocí dalších kroků.

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízena a také povolena. Použijte následující příklad:

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   Odpověď bude podobná jako v následujícím příkladu:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Nakonfigurujte soukromý partnerský vztah Azure pro okruh. Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:

   * Podsíť /30 pro primární propojení. Podsíť nesmí být součástí žádného adresního prostoru vyhrazeného pro virtuální sítě.
   * Podsíť /30 pro sekundární propojení. Podsíť nesmí být součástí žádného adresního prostoru vyhrazeného pro virtuální sítě.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS. Zkontrolujte, že nepoužíváte 65515.
   * **Nepovinné -** Hash MD5, pokud se rozhodnete použít jeden.

   Pomocí následujícího příkladu nakonfigurujte privátní partnerský vztah Azure pro váš okruh:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Pokud se rozhodnete použít hash MD5, použijte následující příklad:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.
   > 
   > 

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Zobrazení podrobností soukromého partnerského vztahu Azure

Podrobnosti o konfiguraci můžete získat pomocí následujícího příkladu:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

Výstup se podobá následujícímu příkladu:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Libovolnou část konfigurace můžete aktualizovat v následujícím příkladu. V tomto příkladu je ID sítě VLAN okruhu aktualizováno ze 100 na 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následujícího příkladu:

> [!WARNING]
> Před spuštěním tohoto příkladu je nutné zajistit, aby byly odebrány všechny virtuální sítě a připojení ExpressRoute Global Reach. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```


## <a name="next-steps"></a>Další kroky

Dalším krokem je [Propojení virtuální sítě s okruhem ExpressRoute](howto-linkvnet-cli.md).

* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
