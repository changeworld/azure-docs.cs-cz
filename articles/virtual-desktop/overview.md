---
title: Co je Windows Virtual Desktop? – Azure
description: Přehled virtuální plochy windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 927696d029bf1b8742dc0001e03799322f368191
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261716"
---
# <a name="what-is-windows-virtual-desktop"></a>Co je Windows Virtual Desktop? 

Windows Virtual Desktop je služba virtualizace plochy a aplikací, která běží v cloudu.

Co můžete dělat při spuštění Windows Virtual Desktop v Azure:

* Nastavení nasazení windows 10 s více relacemi, které poskytuje kompletní Windows 10 se škálovatelností
* Virtualizace Office 365 ProPlus a optimalizace pro běh ve virtuálních scénářích pro více uživatelů
* Poskytněte virtuálním desktopům windows 7 bezplatné rozšířené aktualizace zabezpečení
* Přenesete stávající plochy vzdálené plochy (RDS) a plochy a aplikace systému Windows Server do libovolného počítače
* Virtualizace stolních počítačů i aplikací
* Správa desktopů a aplikací pro Windows 10, Windows Server a Windows 7 díky jednotné správě

## <a name="introductory-video"></a>Úvodní video

Přečtěte si o Windows Virtual Desktop, proč je jedinečná a co je nového v tomto videu:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Další videa o virtuální ploše Windows najdete v [našem seznamu videí](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Klíčové funkce

S Windows Virtual Desktop můžete nastavit škálovatelné a flexibilní prostředí:

* Vytvořte si úplné prostředí virtualizace plochy ve svém předplatném Azure, aniž byste museli spouštět další servery brány.
* Publikujte tolik hostitelských fondů, kolik potřebujete, abyste vyhověli různým úlohám.
* Přineste si vlastní image pro produkční úlohy nebo test z Galerie Azure.
* Snižte náklady pomocí sdružených prostředků s více relacemi. Díky nové funkci více relací systému Windows 10 Enterprise, která je v systému Windows Server určena výhradně pro roli Windows Virtual Desktop a Rd Desktop Host (RDSH), můžete výrazně snížit počet virtuálních počítačů a operačního systému (OS) a přitom uživatelům poskytnout stejné prostředky.
* Poskytněte individuální vlastnictví prostřednictvím osobních (trvalých) ploch.

Virtuální plochy můžete nasadit a spravovat:

* Pomocí rozhraní PowerShell a REST pro Windows Virtual Desktop můžete konfigurovat fondy hostitelů, vytvářet skupiny aplikací, přiřazovat uživatele a publikovat prostředky.
* Publikujte úplné desktopové nebo jednotlivé vzdálené aplikace z jednoho fondu hostitelů, vytvořte jednotlivé skupiny aplikací pro různé sady uživatelů nebo dokonce přiřaďte uživatele k více skupinám aplikací, abyste snížili počet obrázků.
* Při správě prostředí použijte integrovaný delegovaný přístup k přiřazení rolí a shromažďování diagnostiky k pochopení různých chyb konfigurace nebo uživatelů.
* K řešení chyb použijte novou službu Diagnostika.
* Spravujte pouze image a virtuální počítače, ne infrastrukturu. Role vzdálené plochy nemusíte osobně spravovat, jako to děláte se službou Vzdálená plocha, jenom virtuální počítače v předplatném Azure.

Můžete také přiřadit a připojit uživatele k virtuálním plochám:

* Po přiřazení mohou uživatelé spustit libovolného klienta virtuální plochy systému Windows a připojit uživatele k publikovaným plochám a aplikacím systému Windows. Připojte se z libovolného zařízení prostřednictvím nativní aplikace v zařízení nebo webového klienta HTML5 virtuální plochy Windows.
* Bezpečně navazovat uživatele prostřednictvím reverzní připojení ke službě, takže nikdy nebudete muset ponechat žádné příchozí porty otevřené.

## <a name="requirements"></a>Požadavky

Existuje několik věcí, které potřebujete k nastavení virtuální plochy systému Windows a úspěšnému připojení uživatelů k jejich stolním počítačům a aplikacím systému Windows.

Plánujeme přidat podporu pro následující operační týmy, takže se ujistěte, že máte [příslušné licence](https://azure.microsoft.com/pricing/details/virtual-desktop/) pro uživatele na základě plochy a aplikací, které chcete nasadit:

|Operační systém|Požadovaná licence|
|---|---|
|Windows 10 Enterprise s více relacemi nebo Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Obchodní<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Obchodní<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licence klientského přístupu RDS (CAL) se zárukou softwaru|

Vaše infrastruktura potřebuje pro podporu windows virtuální plochy následující věci:

* Služba [Azure Active Directory](/azure/active-directory/)
* Synchronizovaná služba Služby Active Directory systému Windows Server se službou Azure Active Directory. Můžete nakonfigurovat pomocí jedné z následujících možností:
  * Azure AD Connect (pro hybridní organizace)
  * Služby domény Azure AD (pro hybridní nebo cloudové organizace)
* Předplatné Azure, které obsahuje virtuální síť, která obsahuje službu Windows Server Active Directory nebo je k ní připojená
  
Virtuální počítače Azure, které vytvoříte pro Virtuální plochu Windows, musí být:

* [Standardní doména připojená](../active-directory-domain-services/active-directory-ds-comparison.md) nebo [hybridní AD-připojen .](../active-directory/devices/hybrid-azuread-join-plan.md) Virtuální počítače nemůže být připojen y Azure AD.
* Spuštění jedné z následujících [podporovaných bitových kopií operačního systému](#supported-virtual-machine-os-images).

>[!NOTE]
>Pokud potřebujete předplatné Azure, můžete [si zaregistrovat měsíční bezplatnou zkušební verzi](https://azure.microsoft.com/free/). Pokud používáte bezplatnou zkušební verzi Azure, měli byste použít Službu Azure AD Domain Services, abyste udrželi službu Windows Server Active Directory synchronizovanou s Azure Active Directory.

Virtuální počítače Azure, které vytvoříte pro Virtuální plochu Windows, musí mít přístup k následujícím adresám URL:

|Adresa|Odchozí port TCP|Účel|Výrobní číslo|
|---|---|---|---|
|*.wvd.microsoft.com|443|Provoz služeb|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Aktualizace zásobníku agenta a SXS|AzureCloud|
|*.core.windows.net|443|Provoz agentů|AzureCloud|
|*.servicebus.windows.net|443|Provoz agentů|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Provoz agentů|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Aktivace Windows|Internet|

>[!IMPORTANT]
>Ve většině případů doporučujeme používat značky služeb místo adres URL, abyste předešli problémům se službou. Odblokování těchto adres URL je nezbytné pro spolehlivé nasazení virtuální plochy systému Windows. Blokování přístupu k těmto adresám URL není podporováno a ovlivní funkčnost služby. Tyto adresy URL odpovídají jenom webům a prostředkům virtuální plochy Windows a nezahrnují adresy URL pro jiné služby, jako je Azure Active Directory.

V následující tabulce jsou uvedeny volitelné adresy URL, ke kterým můžou mít vaše virtuální počítače Azure přístup:

|Adresa|Odchozí port TCP|Účel|Výrobní číslo|
|---|---|---|---|
|*.microsoftonline.com|443|Ověřování pro služby MS Online Services|Žádný|
|*.events.data.microsoft.com|443|Telemetrická služba|Žádný|
|www.msftconnecttest.com|443|Detekuje, zda je operační operační spoje připojen k internetu|Žádný|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Žádný|
|login.windows.net|443|Přihlášení ke službám MS Online Services, Office 365|Žádný|
|*.sfx.ms|443|Aktualizace klientského softwaru OneDrivu|Žádný|
|*.digicert.com|443|Kontrola odvolání certifikátu|Žádný|


>[!NOTE]
>Program Windows Virtual Desktop v současné době neobsahuje seznam rozsahů adres IP, které lze zařadit na seznam povolených adres, které by umožnily síťový provoz. V tuto chvíli podporujeme pouze konkrétní adresy URL pro výběr z bílého seznamu.
>
>Seznam adres URL souvisejících s Office, včetně požadovaných adres URL souvisejících se službou Azure Active Directory, najdete v [tématu Adresy URL office 365 a rozsahy IP adres](/office365/enterprise/urls-and-ip-address-ranges).
>
>Znak se zástupnými znaky (*) je nutné použít pro adresy URL zahrnující provoz služeb. Pokud nechcete používat * pro provoz související s agentem, zde je návod, jak najít adresy URL bez zástupných znaků:
>
>1. Zaregistrujte své virtuální počítače do fondu hostitelů Virtuální plochy Windows.
>2. Otevřete **prohlížeč událostí** a přejděte na **protokoly** > **systému** > Windows Application**WVD-Agent** a vyhledejte ID události 3702.
>3. Seznam adres URL, které najdete v části ID události 3702, zapíšete seznam adres URL. Adresy URL pod ID události 3702 jsou specifické pro oblast. Proces whitelistingu budete muset zopakovat s příslušnými adresy URL pro každou oblast, ve které chcete virtuální počítače nasadit.

Virtuální plocha Windows zahrnuje plochy a aplikace windows, které doručujete uživatelům, a řešení pro správu, které je hostováno v Azure společností Microsoft jako služba. Plochy a aplikace se můžou nasadit na virtuálních počítačích (VM) v libovolné oblasti Azure a řešení pro správu a data pro tyto virtuální počítače budou umístěny ve Spojených státech. To může vést k přenosu dat do Spojených států.

Chcete-li dosáhnout optimálního výkonu, ujistěte se, že síť splňuje následující požadavky:

* Latence odezvy (RTT) ze sítě klienta do oblasti Azure, kde byly nasazeny fondy hostitelů, by měla být menší než 150 ms.
* Síťový provoz může proudit mimo hranice země nebo oblasti, když se virtuální počítače, které hostují stolní počítače a aplikace, připojí ke službě pro správu.
* Chcete-li optimalizovat výkon sítě, doporučujeme, aby virtuální počítače hostitele relace byly umístěny ve stejné oblasti Azure jako služba pro správu.

## <a name="supported-remote-desktop-clients"></a>Podporovaní klienti vzdálené plochy

Následující klienti vzdálené plochy podporují virtuální plochu systému Windows:

* [Plocha systému Windows](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (náhled)](connect-android.md)

> [!IMPORTANT]
> Virtuální plocha systému Windows nepodporuje klienta RADC (RemoteApp and Desktop Connections) ani klienta MSTSC (Připojení ke vzdálené ploše).

> [!IMPORTANT]
> Virtuální plocha systému Windows momentálně nepodporuje klienta vzdálené plochy z Windows Storu. Podpora pro tohoto klienta bude přidána v budoucí verzi.

Klienti vzdálené plochy musí mít přístup k následujícím adresám URL:

|Adresa|Odchozí port TCP|Účel|Klient (klienty)|
|---|---|---|---|
|*.wvd.microsoft.com|443|Provoz služeb|Všechny|
|*.servicebus.windows.net|443|Poradce při potížích s daty|Všechny|
|go.microsoft.com|443|Microsoft FWLinks|Všechny|
|aka.ms|443|Zkracovač adres URL společnosti Microsoft|Všechny|
|docs.microsoft.com|443|Dokumentace|Všechny|
|privacy.microsoft.com|443|Prohlášení o ochraně osobních údajů
|Všechny|
|query.prod.cms.rt.microsoft.com|443|Aktualizace klienta|Windows Desktop|

>[!IMPORTANT]
>Otevření těchto adres URL je nezbytné pro spolehlivé prostředí klienta. Blokování přístupu k těmto adresám URL není podporováno a ovlivní funkčnost služby. Tyto adresy URL odpovídají jenom klientským webům a prostředkům a nezahrnují adresy URL pro jiné služby, jako je Azure Active Directory.

## <a name="supported-virtual-machine-os-images"></a>Podporované bitové kopie operačního systému virtuálního počítače

Windows Virtual Desktop podporuje následující bitové kopie operačního systému x64:

* Windows 10 Enterprise s více relacemi, verze 1809 nebo novější
* Windows 10 Enterprise, verze 1809 nebo novější
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop nepodporuje image operačního systému X86 (32bit), Windows 10 Enterprise N nebo Windows 10 Enterprise KN. Windows 7 také nepodporuje žádná řešení profilů založených na virtuálním pevném disku nebo VHDX hostovaná ve spravovaném úložišti Azure Z důvodu omezení velikosti sektoru.

Dostupné možnosti automatizace a nasazení závisí na tom, který operační systém a verzi zvolíte, jak je znázorněno v následující tabulce: 

|Operační systém|Galerie obrázků Azure|Ruční nasazení virtuálního počítače|Integrace šablon Azure Resource Manageru|Zřídit fondy hostitelů na Azure Marketplace|Aktualizace agenta virtuální plochy systému Windows|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 více relací, verze 1903|Ano|Ano|Ano|Ano|Automaticky|
|Windows 10 více relací, verze 1809|Ano|Ano|Ne|Ne|Automaticky|
|Windows 10 Enterprise, verze 1903|Ano|Ano|Ano|Ano|Automaticky|
|Windows 10 Enterprise, verze 1809|Ano|Ano|Ne|Ne|Automaticky|
|Windows 7 Enterprise|Ano|Ano|Ne|Ne|Ruční|
|Windows Server 2019|Ano|Ano|Ne|Ne|Automaticky|
|Windows Server 2016|Ano|Ano|Ano|Ano|Automaticky|
|Windows Server 2012 R2|Ano|Ano|Ne|Ne|Automaticky|

## <a name="next-steps"></a>Další kroky

Chcete-li začít, budete muset vytvořit klienta. Další informace o tom, jak vytvořit klienta, pokračujte v kurzu pro vytváření klienta.

> [!div class="nextstepaction"]
> [Vytvoření klienta ve Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
