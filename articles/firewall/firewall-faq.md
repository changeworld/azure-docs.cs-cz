---
title: Nejčastější dotazy k Azure Firewall
description: Nejčastější dotazy k bráně Azure Firewall Spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky virtuální sítě Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: ea94e452b463fffc1800e09fa1302abacdf015cc
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383072"
---
# <a name="azure-firewall-faq"></a>Nejčastější dotazy k Azure Firewall

## <a name="what-is-azure-firewall"></a>Co je brána Azure Firewall?

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností cloudu. Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Jaké funkce jsou podporované v Azure Firewall?

* Stavový firewall jako služba
* Integrovaná vysoká dostupnost s neomezenou škálovatelností cloudu
* Filtrování FQDN
* Značky plně kvalifikovaných názvů domén
* Pravidla filtrování síťového provozu
* Podpora pro odchozí SNAT
* Podpora DNAT u příchozích přenosů
* Centrálně vytvářejte, vynucujte a protokolujte zásady aplikace a připojení k síti napříč předplatnými Azure a virtuálními sítěmi
* Plně integrovaná s Azure Monitorem pro protokolování a analýzy

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Jaký je typický model nasazení pro Azure Firewall?

Azure Firewall můžete nasadit do libovolné virtuální sítě, ale zákazníci ji obvykle nasazují v centrální virtuální síti a v modelu hub-and-spoke do ní nasazují další virtuální sítě. Potom můžete nastavit výchozí trasu z partnerských virtuálních sítí tak, aby přectola na tuto centrální virtuální síť brány firewall. Globální partnerský vztah virtuální sítě je podporovaný, ale nedoporučuje se z důvodu potenciálníproblémy s výkonem a latencí napříč oblastmi. Pro dosažení nejlepšího výkonu nasaďte jednu bránu firewall na oblast.

Výhodou tohoto modelu je schopnost centrálně vykonávat kontrolu na více paprskových virtuálních dálcích v různých předplatných. Existují také úspory nákladů, protože nemusíte nasadit bránu firewall v každé virtuální síti zvlášť. Úspory nákladů by měly být měřeny oproti nákladům přidruženého partnerského vztahu na základě vzorců provozu zákazníka.

## <a name="how-can-i-install-the-azure-firewall"></a>Jak můžu nainstalovat Azure Firewall?

Azure Firewall můžete nastavit pomocí portálu Azure, PowerShellu, rozhraní REST API nebo pomocí šablon. [Podrobný návod najdete v tématu: Nasazení a konfigurace brány Azure firewall pomocí portálu Azure.](tutorial-firewall-deploy-portal.md)

## <a name="what-are-some-azure-firewall-concepts"></a>Jaké jsou některé koncepty Azure Firewall?

Azure Firewall podporuje pravidla a kolekce pravidel. Kolekce pravidel je sada pravidel, která sdílejí stejné pořadí a prioritu. Kolekce pravidel jsou spouštěny v pořadí podle jejich priority. Kolekce síťových pravidel mají vyšší prioritu než kolekce pravidel aplikace a všechna pravidla jsou ukončována.

Existují tři typy kolekcí pravidel:

* *Pravidla aplikace*: Konfigurace plně kvalifikovaných názvů domén (FQDNs), ke kterým lze přistupovat z podsítě.
* *Pravidla sítě*: Konfigurace pravidel, která obsahují zdrojové adresy, protokoly, cílové porty a cílové adresy.
* *Pravidla NAT*: Konfigurace pravidel DNAT tak, aby umožňovala příchozí připojení k Internetu.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Podporuje Azure Firewall filtrování příchozích přenosů?

Azure Firewall podporuje příchozí a odchozí filtrování. Příchozí ochrana se obvykle používá pro protokoly bez protokolu HTTP/S. Například protokoly RDP, SSH a FTP. Nejlepší příchozí ochrana HTTP/S slouží k použití brány firewall webové aplikace, například [brány firewall webových aplikací Azure (WAF).](../web-application-firewall/overview.md)

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Které služby protokolování a analýzy azure firewall podporuje?

Azure Firewall je integrovaný s Azure Monitorpro zobrazení a analýzu protokolů brány firewall. Protokoly lze odesílat do Log Analytics, Azure Storage nebo Event Hubs. Můžou je analyzovat v Log Analytics nebo pomocí různých nástrojů, jako je Excel a Power BI. Další informace naleznete [v tématu Sledování protokolů brány Azure firewall](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Jak azure firewall funguje jinak než stávající služby, jako jsou virtuální virtuální va na trhu?

Azure Firewall je základní služba brány firewall, která může řešit určité scénáře zákazníků. Očekává se, že budete mít kombinaci neva třetích stran a Azure Firewall. Lepší spolupráce je hlavní prioritou.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaký je rozdíl mezi WAF aplikační brány a Azure Firewall?

Brána firewall webových aplikací (WEB Application Firewall) je funkce application gateway, která poskytuje centralizovanou příchozí ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení. Brána Azure Firewall poskytuje příchozí ochranu pro protokoly bez http/s (například RDP, SSH, FTP), ochranu na úrovni odchozí sítě pro všechny porty a protokoly a ochranu na úrovni aplikace pro odchozí protokoly HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Jaký je rozdíl mezi skupinami zabezpečení sítě (NSG) a bránou Azure Firewall?

Služba Azure Firewall doplňuje funkce skupiny zabezpečení sítě. Společně poskytují lepší "obranu do hloubky" zabezpečení sítě. Skupiny zabezpečení sítě poskytují filtrování distribuovaných síťových vrstev, které omezuje provoz na prostředky v rámci virtuálních sítí v každém předplatném. Azure Firewall je plně stavová centralizovaná síťová brána firewall jako služba, která poskytuje ochranu na úrovni sítě a aplikací napříč různými předplatnými a virtuálními sítěmi.

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>Jsou skupiny zabezpečení sítě (NSG) podporovány v síti AzureFirewallSubnet?

Azure Firewall je spravovaná služba s více vrstvami ochrany, včetně ochrany platformy pomocí nsG na úrovni nic (nelze zobrazit).  Skupiny zabezpečení na úrovni podsítě nejsou v síti AzureFirewallSubnet vyžadovány a jsou zakázány, aby bylo zajištěno žádné přerušení služby.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Jak nastavím Azure Firewall pomocí koncových bodů služby?

Pro bezpečný přístup ke službám PaaS doporučujeme koncové body služby. Můžete povolit koncové body služby v podsíti Azure Firewall a zakázat je ve virtuálních sítích připojených paprsků. Tímto způsobem můžete využívat obě funkce: zabezpečení koncového bodu služby a centrální protokolování pro veškerý provoz.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Jaké jsou ceny pro Azure Firewall?

Viz [Ceny azure firewall .](https://azure.microsoft.com/pricing/details/azure-firewall/)

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Jak můžu zastavit a spustit Azure Firewall?

Můžete použít Azure PowerShell *navrátit* a *přidělit* metody.

Příklad:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Bránu firewall a veřejnou IP adresu je nutné přerozdělit do původní skupiny prostředků a předplatného.

## <a name="what-are-the-known-service-limits"></a>Jaké jsou známé limity služeb?

Omezení služeb Azure Firewall najdete v [tématu Omezení předplatného a služeb Azure, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Může azure firewall v rozbočovačvirtuální sítě dopředit a filtrovat síťový provoz mezi dvěma virtuálními sítěmi s paprsky?

Ano, azure firewall ve virtuální síti rozbočovače můžete použít k směrování a filtrování provozu mezi dvěma paprskovými virtuálními sítěmi. Podsítě v každé virtuální síti s paprskem musí mít UDR odkazující na bránu Azure firewall jako výchozí bránu, aby tento scénář fungoval správně.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Může brána Azure předávat a filtrovat síťový provoz mezi podsítěmi ve stejné virtuální síti nebo partnerských virtuálních sítích?

Ano. Konfigurace UDR pro přesměrování přenosů mezi podsítěmi ve stejné virtuální síti však vyžaduje další pozornost. Při použití rozsahu adres virtuální sítě jako cílové předpony pro UDR je dostačující, to také směruje veškerý provoz z jednoho počítače do jiného počítače ve stejné podsíti prostřednictvím instance Azure Firewall. Chcete-li tomu zabránit, zahrňte trasu pro podsíť v UDR s dalším typem směrování **virtuální sítě**. Správa těchto tras může být těžkopádná a náchylná k chybám. Doporučenou metodou pro segmentaci interní sítě je použití skupin zabezpečení sítě, které nevyžadují udr.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Předává Azure Firewall odchozí SNAT mezi privátními sítěmi?

Azure Firewall není SNAT, pokud cílová IP adresa je privátní rozsah IP adres na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Pokud vaše organizace používá rozsah veřejných IP adres pro privátní sítě, Azure Firewall SNAT steska provozu na jednu z privátních IP adres brány firewall v AzureFirewallSubnet. Bránu Azure Firewall **not** můžete nakonfigurovat tak, aby nespořila rozsah veřejných IP adres. Další informace naleznete v tématu [Rozsahprijových IP adres Azure Firewall SNAT](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Je podporováno vynucené tunelování/řetězení do síťového virtuálního zařízení?

Vynucené tunelové propojení je podporováno. Další informace naleznete v tématu [vynucené tunelové propojení Azure Firewall (náhled)](forced-tunneling.md). 

Azure Firewall musí mít přímé připojení k Internetu. Pokud se vaše síť AzureFirewallSubnet učí výchozí trasu do místní sítě prostřednictvím protokolu BGP, musíte to přepsat udr 0.0.0.0/0 s hodnotou **NextHopType** nastavenou jako **Internet,** aby bylo zachováno přímé připojení k Internetu.

Pokud vaše konfigurace vyžaduje vynucené tunelové propojení do místní sítě a můžete určit cílové IP předpony pro vaše cíle internetu, můžete nakonfigurovat tyto rozsahy s místní sítí jako další směrování prostřednictvím uživatelem definované trasy na AzureFirewallSubnet. Nebo můžete použít Protokol BGP k definování těchto tras.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Existují nějaká omezení skupiny prostředků brány firewall?

Ano. Brána firewall, virtuální síť a veřejná IP adresa musí být ve stejné skupině prostředků.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Musím při konfiguraci aplikace DNAT pro příchozí síťový provoz v Internetu také nakonfigurovat odpovídající síťové pravidlo, které tento provoz povolí?

Ne. Pravidla překladu napřeklad implicitně přidat odpovídající síťové pravidlo povolit přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu. Další informace najdete v článku, který pojednává o [logice zpracování pravidel služby Azure Firewall](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Jak fungují zástupné znaky v cílovém vícenežnovém název pravidla aplikace?

Pokud nakonfigurujete ***.contoso.com**, umožňuje *libovolnou hodnotu*.contoso.com, ale ne contoso.com (vrchol domény). Pokud chcete povolit vrchol domény, musíte jej explicitně nakonfigurovat jako cílový fqdn.

## <a name="what-does-provisioning-state-failed-mean"></a>Co znamená *stav zřizování: Selhalo?*

Při každém použití změny konfigurace se azure firewall pokusí aktualizovat všechny základní back-endové instance. Ve výjimečných případech může jedna z těchto back-endových instancí selhat aktualizovat s novou konfiguraci a proces aktualizace se zastaví se selháním stavu zřizování. Brána Azure Firewall je stále funkční, ale použitá konfigurace může být v nekonzistentním stavu, kde některé instance mají předchozí konfiguraci, kde jiné mají nastavené aktualizované pravidlo. Pokud k tomu dojde, zkuste aktualizovat konfiguraci ještě jednou, dokud operace proběhne úspěšně a brána firewall je ve stavu *úspěšné* zřizování.

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Jak azure firewall zpracovává plánovanou údržbu a neplánované chyby?
Azure Firewall se skládá z několika back-endových uzlů v konfiguraci aktivní a aktivní.  Pro všechny plánované údržby máme logiku vypouštění připojení řádně aktualizovat uzly.  Aktualizace se plánují během mimo pracovní dobu pro každou oblast Azure, aby se dále omezilo riziko narušení.  V případě neplánovaných problémů instancijeme nový uzel, který nahradí neúspěšný uzel.  Připojení k novému uzlu je obvykle obnoveno během 10 sekund od okamžiku selhání.

## <a name="how-does-connection-draining-work"></a>Jak funguje vypouštění připojení?

Pro všechny plánované údržby logiky vyprázdnění připojení řádně aktualizuje back-endové uzly. Azure Firewall čeká 90 sekund na ukončení existujících připojení. V případě potřeby mohou klienti automaticky obnovit připojení k jinému back-endovému uzlu.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Existuje omezení znaků pro název brány firewall?

Ano. Pro název brány firewall je limit 50 znaků.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Proč azure firewall potřebuje velikost podsítě /26?

Azure Firewall musí zřídit více instancí virtuálních strojů při škálování. Adresní prostor /26 zajišťuje, že brána firewall má k dispozici dostatek adres IP pro škálování.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Je třeba změnit velikost podsítě brány firewall, jak se škáluje služba?

Ne. Azure Firewall nepotřebuje podsíť větší než /26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Jak mohu zvýšit propustnost brány firewall?

Počáteční kapacita azure firewallu je 2,5 – 3 Gb/s a škáluje se na 30 Gb/s. Automaticky se škáluje na základě využití procesoru a propustnosti.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Jak dlouho trvá, než se Azure Firewall zvětšuje?

Azure Firewall se postupně škáluje, když průměrná propustnost nebo spotřeba procesoru je 60 %. Horizontální navýšení kapacity trvá pět až sedm minut. Při testování výkonu se ujistěte, že testujete alespoň 10 až 15 minut, a inicializujte nová připojení, abyste využili výhod nově vytvořených uzlů brány firewall.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Umožňuje azure brána Azure ve výchozím nastavení přístup ke službě Active Directory?

Ne. Brána Azure Firewall ve výchozím nastavení blokuje přístup ke službě Active Directory. Chcete-li povolit přístup, nakonfigurujte značku služby AzureActiveDirectory. Další informace naleznete v tématu [značky služeb Azure Firewall](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Můžu vyloučit velký název sítě nebo IP adresu z filtrování založeného na Azure Firewall na threat intelligence?

Ano, azure powershell uděláte takto:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist.FQDNs = @("fqdn1", "fqdn2", …)
$fw.ThreatIntelWhitelist.IpAddress = @("ip1", "ip2", …)

Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Proč se příkaz ping tcp a podobné nástroje mohou úspěšně připojit k cílovému virtuálnímu síti, i když žádné pravidlo na Azure Firewall tento provoz neumožňuje?

Příkaz ping protokolu TCP se ve skutečnosti nepřipojuje k cílovému vícesměrovému síti. K tomu dochází, protože transparentní proxy server Azure Firewall naslouchá na portu 80/443 pro odchozí provoz. Příkaz ping protokolu TCP naváže připojení k bráně firewall, která poté paket zahodí a zaznamená připojení. Toto chování nemá žádný dopad na zabezpečení. Abychom se však vyhnuli nejasnostem, zkoumáme potenciální změny tohoto chování.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Existují omezení pro počet IP adres podporovaných skupinami IP?

Ano. Další informace najdete v tématu [Omezení předplatného a služeb Azure, kvóty a omezení.](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)
