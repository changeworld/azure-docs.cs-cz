---
title: 'Azure ExpressRoute: Konfigurace partnerského vztahu'
description: Tento článek dokumentuje kroky pro vytváření a zřizování ExpressRoute soukromé a partnerský vztah Microsoftu. Tento článek také ukazuje, jak zkontrolovat stav, aktualizovat nebo odstranit partnerských stran pro okruh.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: mialdrid
ms.openlocfilehash: 18d2db18e9880028c60b4b545c3628f4a9cb4703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264788"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute

Tento článek vám pomůže vytvořit a spravovat konfiguraci směrování pro okruh ExpressRoute Azure Resource Manager (ARM) pomocí portálu Azure. Můžete také zkontrolovat stav, aktualizovat nebo odstranit a zrušit poskytování partnerských vztahu pro okruh ExpressRoute. Pokud chcete pro práci s okruhem použít jinou metodu, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Veřejný partnerský vztah](about-public-peering.md)
> * [Video - Soukromý partnerský vztah](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

Můžete nakonfigurovat privátní partnerský vztah a partnerský vztah Microsoftu pro okruh ExpressRoute (veřejný partnerský vztah Azure se nezastaral pro nové okruhy). Partnerských stran lze nakonfigurovat v libovolném pořadí, které zvolíte. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o směrovacích doménách a partnerských serverech naleznete v tématu [Směrovací domény ExpressRoute](expressroute-circuit-peerings.md). Informace o veřejném partnerského vztahu naleznete v tématu [ExpressRoute public peering](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Před zahájením konfigurace se ujistěte, že jste si přečetli stránku s [předpoklady](expressroute-prerequisites.md), stránku s [požadavky směrování](expressroute-routing.md) a stránku s [pracovními postupy](expressroute-workflows.md).
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený vaším poskytovatelem připojení. Chcete-li konfigurovat partnerský vztah(y), okruh ExpressRoute musí být ve zřízeném a povoleném stavu. 
* Pokud plánujete použít sdílený klíč/Hodnotit hodnotu hash MD5, použijte jej na obou stranách tunelu a omezte počet alfanumerických znaků na maximálně 25. Speciální znaky nejsou podporovány. 

Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IPVPN, jako je MPLS), váš poskytovatel připojení konfiguruje a spravuje směrování za vás. 

> [!IMPORTANT]
> Aktuálně prostřednictvím portálu pro správu služeb neinzerujeme partnerské vztahy nakonfigurované poskytovateli služeb. Pracujeme na tom, aby tato možnost brzy fungovala. Před konfigurací partnerských stran Protokolu BGP se obraťte na svého poskytovatele služeb.
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Partnerský vztah Microsoftu

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Microsoft peering obvodů ExpressRoute, které byly nakonfigurovány před 1. Microsoft peering obvodů ExpressRoute, které jsou nakonfigurovány na nebo po srpnu 1, 2017 nebude mít žádné předpony inzerované, dokud není připojen filtr trasy k okruhu. Další informace naleznete [v tématu Konfigurace filtru postupu pro partnerský vztah společnosti Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Nakonfigurujte okruh ExpressRoute. Zkontrolujte **stav zprostředkovatele** a ujistěte se, že okruh je plně zřízena poskytovatelem připojení před pokračováním dále.

   Pokud váš poskytovatel připojení nabízí spravované služby Vrstvy 3, můžete požádat svého poskytovatele připojení o povolení partnerského vztahu Microsoftu za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v následujících částech. Pokud však váš poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte v těchto krocích.

   **Okruh - Stav zprostředkovatele: Není zřízeno**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Okruh - stav zprostředkovatele: Zřízeno**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Nakonfigurujte partnerský vztah Microsoftu pro okruh. Před pokračováním se ujistěte, že máte k dispozici následující informace.

   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě přiřadíte směrovači první použitou adresu IP, protože společnost Microsoft používá druhou použitelnou adresu IP pro svůj směrovač.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR. Z této podsítě přiřadíte směrovači první použitou adresu IP, protože společnost Microsoft používá druhou použitelnou adresu IP pro svůj směrovač.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. Pro primární i sekundární odkazy je nutné použít stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud plánujete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
   * **Nepovinné -** Číslo ASN odběratele: Pokud inzerujete předpony, které nejsou registrovány na číslo AS partnerského vztahu, můžete zadat číslo AS, na které jsou registrovány.
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * **Nepovinné -** Hash MD5, pokud se rozhodnete použít jeden.
3. Můžete vybrat partnerský vztah, který chcete konfigurovat, jak je znázorněno v následujícím příkladu. Vyberte řádek partnerského vztahu Microsoftu.

   [![Výběr řádku partnerského vztahu Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Výběr řádku partnerského vztahu Microsoftu")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Nakonfigurujte partnerský vztah Microsoftu. Po zadání všech parametrů **uložte** konfiguraci. Následující obrázek ukazuje ukázkovou konfiguraci:

   ![Konfigurace peeringu s Microsoftem](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Společnost Microsoft ověří, zda jsou v registru směrování v Internetu přiřazeny zadané předpony inzerované veřejné a peer asn (nebo "ASN zákazníka". Pokud získáváte veřejné předpony z jiné entity a přiřazení není zaznamenáno v registru směrování, automatické ověření nebude dokončeno a bude vyžadovat ruční ověření. Pokud se automatické ověření nezdaří, zobrazí se zpráva "Ověření je potřeba". 
>
> Pokud se zobrazí zpráva "Ověření je potřeba", shromážděte dokumenty, které ukazují, že veřejné předpony jsou přiřazeny vaší organizaci entitou, která je uvedena jako vlastník předpon v registru směrování, a odešlete tyto dokumenty k ručnímu ověření otevření lístku podpory, jak je uvedeno níže. 
>

   Pokud se okruh dostane do stavu "Ověření potřebné", musíte otevřít lístek podpory, abyste našemu týmu podpory zobrazili doklad o vlastnictví předpon. Lístek podpory můžete otevřít přímo z portálu, jak je znázorněno v následujícím příkladu:

   ![Potřebné ověření - lístek podpory](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Po úspěšném přijetí konfigurace se zobrazí něco podobného následujícímu obrázku:

   ![Stav partnerského vztahu: Nakonfigurováno](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Stav partnerského vztahu: Nakonfigurováno")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Zobrazení podrobností partnerského vztahu Microsoftu

Vlastnosti partnerského vztahu Microsoftu můžete zobrazit výběrem řádku pro partnerský vztah.

[![Zobrazit vlastnosti partnerského vztahu Microsoftu](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Zobrazení vlastností")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Můžete vybrat řádek pro partnerský vztah, který chcete upravit, potom upravit vlastnosti partnerského vztahu a uložit změny.

![Vybrat řádek partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat kliknutím na ikonu odstranění, jak je znázorněno na následujícím obrázku:

![Odstranit partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Soukromý partnerský vztah Azure

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci privátního partnerského vztahu Azure pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Nakonfigurujte okruh ExpressRoute. Než budete pokračovat, ujistěte se, že je okruh poskytovatelem připojení plně zřízený. 

   Pokud váš poskytovatel připojení nabízí spravované služby Vrstvy 3, můžete požádat svého poskytovatele připojení o povolení privátního partnerského vztahu Azure. V takovém případě nebudete muset postupovat podle pokynů uvedených v následujících částech. Pokud však váš poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte dalšími kroky.

   **Okruh - Stav zprostředkovatele: Není zřízeno**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Okruh - stav zprostředkovatele: Zřízeno**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Nakonfigurujte soukromý partnerský vztah Azure pro okruh. Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:

   * Podsíť /30 pro primární propojení. Podsíť nesmí být součástí žádného adresního prostoru vyhrazeného pro virtuální sítě. Z této podsítě přiřadíte směrovači první použitou adresu IP, protože společnost Microsoft používá druhou použitelnou adresu IP pro svůj směrovač.
   * Podsíť /30 pro sekundární propojení. Podsíť nesmí být součástí žádného adresního prostoru vyhrazeného pro virtuální sítě. Z této podsítě přiřadíte směrovači první použitou adresu IP, protože společnost Microsoft používá druhou použitelnou adresu IP pro svůj směrovač.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN. Pro primární i sekundární odkazy je nutné použít stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS s výjimkou čísla od 65515 do 65520, včetně.
   * Při nastavování privátního partnerského vztahu je nutné inzerovat trasy z místního směrovače Edge do Azure prostřednictvím protokolu BGP.
   * **Nepovinné -** Hash MD5, pokud se rozhodnete použít jeden.
3. Vyberte řádek privátního partnerského vztahu Azure, jak je znázorněno v následujícím příkladu:

   [![Výběr řádku soukromého partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Výběr řádku soukromého partnerského vztahu")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Nakonfigurujte soukromý partnerský vztah. Po zadání všech parametrů **uložte** konfiguraci.

   ![konfigurace soukromého partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Po úspěšném přijetí konfigurace se zobrazí něco podobného následujícímu příkladu:

   ![uložený soukromý partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Zobrazení podrobností soukromého partnerského vztahu Azure

Vlastnosti soukromého partnerského vztahu Azure můžete zobrazit výběrem partnerského vztahu.

[![Zobrazit vlastnosti soukromého partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Zobrazit vlastnosti soukromého partnerského vztahu")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Můžete vybrat řádek pro partnerský vztah a upravit vlastnosti partnerského vztahu. Po aktualizaci uložte změny.

![aktualizovat soukromý partnerský vztah](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat výběrem ikony odstranění, jak je znázorněno na následujícím obrázku:

> [!WARNING]
> Před spuštěním tohoto příkladu je nutné zajistit, aby byly odebrány všechny virtuální sítě a připojení ExpressRoute Global Reach. 
> 
> 

![odstranění soukromého partnerského vztahu](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Další kroky

Další [krok, propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
