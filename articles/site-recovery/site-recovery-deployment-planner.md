---
title: Plánovač nasazení obnovení webu Azure pro zotavení po havárii společnosti VMware
description: Přečtěte si o Plánovači nasazení webu Azure pro zotavení po havárii virtuálních počítačích VMware do Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mayg
ms.openlocfilehash: 70d84516e2d7a42b1c6a3714d9060bedf6535f58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366292"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>O plánovači nasazení azure site recovery pro VMware do Azure
Tento článek představuje uživatelskou příručku k Plánovači nasazení služby Azure Site Recovery pro produkční nasazení VMware do Azure.

## <a name="overview"></a>Přehled

Než začnete chránit jakékoli virtuální počítače VMware pomocí Azure Site Recovery, přidělte dostatečnou šířku pásma v závislosti na vaší denní frekvenci změn dat, abyste dosáhli požadovaného cíle bodu obnovení (RPO). Nezapomeňte místně nasadit správný počet konfiguračních serverů a procesových serverů.

Také je nutné vytvořit správný typ a počet cílových účtů služby Azure Storage. Vytvoříte účty služby Storage úrovně Standard nebo Premium, které budou zohledňovat nárůst počtu vašich zdrojových produkčních serverů způsobený zvyšováním využití v průběhu času. Typ úložiště zvolíte pro každý virtuální počítač na základě charakteristik úloh (jako je počet vstupně-výstupních operací [IOPS] čtení a zápisu za sekundu nebo četnost změn dat) a omezení Site Recovery.

 Plánovač nasazení služby Site Recovery je nástroj příkazového řádku pro scénáře zotavení po havárii z Hyper-V do Azure i z VMware do Azure. Pomocí tohoto nástroje můžete vzdáleně profilovat virtuální počítače VMware (bez jakéhokoli dopadu na produkční prostředí) a porozumět tak požadavkům na šířku pásma a úložiště pro úspěšnou replikaci a testovací převzetí služeb při selhání. Nástroj můžete spustit místně bez nutnosti instalace jakýchkoli komponent Site Recovery. Pokud chcete získat přesné výsledky dosažené propustnosti, spusťte plánovač na Windows Serveru splňujícím minimální požadavky konfiguračního serveru Site Recovery, který časem budete muset nasadit v jednom z prvních kroků produkčního nasazení.

Nástroj poskytuje následující podrobnosti:

**Posouzení kompatibility**

* Vyhodnocení způsobilosti virtuálního počítače na základě počtu disků, velikosti disků, počtu vstupně-výstupních operací za sekundu (IOPS), četnosti změn, typu spuštění (EFI nebo BIOS) a verze operačního systému

**Srovnání šířky pásma sítě a posouzení cíle bodu obnovení**

* Odhadovaná šířka pásma sítě potřebná pro rozdílovou replikaci
* Propustnost z místního prostředí do Azure, které Site Recovery může dosáhnout
* Počet virtuálních počítačů pro dávku na základě odhadované šířky pásma pro dokončení počáteční replikace v daném čase
* Cíl bodu obnovení, kterého je možné dosáhnout pro danou šířku pásma
* Dopad na požadovaný cíl bodu obnovení při zřízení menší šířky pásma

**Požadavky na infrastrukturu Azure**

* Typ úložiště (standardní nebo prémiové úložiště) pro každý virtuální měsících
* Celkový počet účtů úložiště standardu a premium, které mají být nastaveny pro replikaci (zahrnuje účty úložiště mezipaměti)
* Návrhy pojmenování účtů úložiště na základě pokynů pro službu Storage
* Počet jader Azure, která se mají nastavit před testovacím převzetím služeb při selhání nebo převzetím služeb při selhání v rámci předplatného
* Doporučená velikost virtuálního počítače Azure pro každý místní virtuální počítač

**Požadavky na místní infrastrukturu**
* Požadovaný počet konfiguračních serverů a procesových serverů, které se mají nasadit v místním prostředí

**Odhadované náklady na zotavení po havárii do Azure**
* Odhadované celkové náklady na zotavení po havárii do Azure: náklady na licenci Site Recovery, výpočty, úložiště a síť
* Podrobná analýza nákladů na virtuální počítač


>[!IMPORTANT]
>
>Protože se využití časem bude pravděpodobně zvyšovat, všechny předchozí výpočty jsou provedeny s předpokladem 30% faktoru růstu v charakteristikách úloh. Pro výpočty se také používají hodnoty 95. percentilu všech metrik profilace, jako je počet vstupně-výstupních operací čtení a zápisu za sekundu a četnost změn. Faktor růstu i výpočet percentilu je možné konfigurovat. Další informace o faktoru růstu najdete v části Aspekty faktoru růstu. Další informace o hodnotě percentilu najdete v části Hodnota percentilu používaná k výpočtu.
>

## <a name="support-matrix"></a>Matice podpory

| | **Z VMware do Azure** |**Z Hyper-V do Azure**|**Z Azure do Azure**|**Z Hyper-V do sekundární lokality**|**Z VMware do sekundární lokality**
--|--|--|--|--|--
Podporované scénáře |Ano|Ano|Ne|Ano*|Ne
Podporovaná verze | vCenter 6,7, 6,5, 6,0 nebo 5,5| Windows Server 2016, Windows Server 2012 R2 | Není k dispozici |Windows Server 2016, Windows Server 2012 R2|Není k dispozici
Podporovaná konfigurace|vCenter, ESXi| Cluster Hyper-V, hostitel Hyper-V|Není k dispozici|Cluster Hyper-V, hostitel Hyper-V|Není k dispozici|
Počet serverů, které je možné profilovat, na spuštěnou instanci Plánovače nasazení služby Site Recovery |Jeden (virtuální počítače, které patří k jednomu vCenter Serveru nebo jednomu serveru ESXi, jde profilovat najednou)|Více (najednou je možné profilovat virtuální počítače napříč více hostiteli nebo hostitelskými clustery)| Není k dispozici |Více (najednou je možné profilovat virtuální počítače napříč více hostiteli nebo hostitelskými clustery)| Není k dispozici

*Nástroj je primárně určen pro scénář zotavení po havárii z Hyper-V do Azure. Pro zotavení po havárii z Hyper-V do sekundární lokality je možné ho použít pouze k pochopení doporučení na straně zdroje, jako je požadovaná šířka pásma sítě, požadovaný prázdný prostor úložiště na každém ze zdrojových serverů Hyper-V a počet dávek počáteční replikace a definice dávek. Ignorujte doporučení Azure a náklady ze sestavy. Kromě toho není možné použít operaci Zjištění propustnosti pro scénář zotavení po havárii z Hyper-V do sekundární lokality.

## <a name="prerequisites"></a>Požadavky
Nástroj má dvě hlavní fáze: profilace a generování sestav. Existuje také třetí možnost – výpočet pouze propustnosti. Požadavky na server, ze kterého se spouští profilace a měření propustnosti, jsou uvedené v následující tabulce.

| Požadavek na server | Popis|
|---|---|
|Profilace a měření propustnosti| <ul><li>Operační systém: Windows Server 2016 nebo Windows Server 2012 R2<br>(Ideálně alespoň stejná velikost jako [doporučená velikost pro konfigurační server](https://aka.ms/asr-v2a-on-prem-components))</li><li>Konfigurace počítače: 8 virtuálních CPU, 16 GB paměti RAM, 300 GB HDD</li><li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Distribuovatelné součásti Visual C++ pro Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Přístup k internetu do Azure (*.blob.core.windows.net) z tohoto serveru, port 443<br>[Toto je volitelné. Během generování sestavy můžete poskytnout dostupnou šířku pásma ručně.]</li><li>Účet služby Azure Storage</li><li>Přístup správce na server</li><li>Volné místo na disku alespoň 100 GB (za předpokladu 1,000 virtuálních počítačů, každý průměrně se 3 disky a profilovaný po dobu 30 dnů)</li><li>Nastavení statistiky vCenter VMware může být 1 nebo vyšší</li><li>Povolit port vCenter (výchozí 443): Plánovač nasazení obnovení webu používá tento port k připojení k hostiteli serveru vCenter/ESXi</ul></ul>|
| Generování sestav | Počítač s Windows nebo Windows Server s Excelem 2013 nebo novějším.<li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Distribuovatelné součásti Visual C++ pro Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli) je vyžadována pouze v případě, že předáte možnost -User v příkazu generování sestavy k načtení nejnovějších informací o konfiguraci virtuálních počítačů. Plánovač nasazení se připojuje k serveru vCenter. Povolte port vCenter (výchozí 443) pro připojení k serveru vCenter.</li>|
| Uživatelská oprávnění | Oprávnění jen ke čtení pro uživatelský účet používaný pro přístup k serveru VMware vCenter nebo k hostiteli VMware vSphere ESXi během profilace |

> [!NOTE]
>
>Nástroj může profilovat pouze virtuální počítače s disky VMDK a RDM. Nemůže profilovat virtuální počítače s disky iSCSI nebo NFS. Site Recovery podporuje disky iSCSI a NFS pro servery VMware. Vzhledem k tomu, že plánovač nasazení nesídlí v hostu a profilaci provádí pouze pomocí čítačů výkonu vCenter, nástroj do těchto typů disků nevidí.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Stažení a rozbalení nástroje plánovače nasazení
1. Stáhněte si nejnovější verzi [Plánovače nasazení služby Site Recovery](https://aka.ms/asr-deployment-planner).
Nástroje je zabalený ve složce .zip. Aktuální verze nástroje podporuje pouze scénář nasazení VMware do Azure.

2. Zkopírujte složku .zip na Windows Server, ze kterého chcete nástroj spustit.
Nástroj můžete spustit z Windows Serveru 2012 R2, pokud má server přístup k internetu pro připojení k serveru vCenter nebo k hostiteli vSphere ESXi, na kterém sídlí virtuální počítače určené k profilaci. Doporučujeme však spustit nástroj na serveru, jehož konfigurace hardwaru odpovídá [pokynům k nastavení velikosti konfiguračního serveru](https://aka.ms/asr-v2a-on-prem-components). Pokud jste již nasadili komponenty Site Recovery v místním prostředí, spusťte nástroj z konfiguračního serveru.

    Doporučujeme, abyste měli stejnou konfiguraci hardwaru konfiguračního serveru (který obsahuje integrovaný procesový server) a serveru, na kterém nástroj spouštíte. Taková konfigurace zajistí, že dosažená propustnost, kterou nástroj hlásí, bude odpovídat skutečné propustnosti, které může Site Recovery dosáhnout během replikace. Výpočet propustnosti závisí na dostupné šířce pásma sítě na serveru a na konfiguraci hardwaru (jako je CPU a úložiště) serveru. Pokud nástroj spustíte z nějakého jiného serveru, vypočítá se propustnost z tohoto serveru do Azure. Navíc se může lišit konfigurace hardwaru tohoto serveru a konfiguračního serveru, proto dosažená propustnost, kterou nástroj hlásí, nemusí odpovídat skutečnosti.

3. Rozbalte složku .zip.
Složka obsahuje několik souborů a podsložek. Spustitelný soubor je ASRDeploymentPlanner.exe v nadřazené složce.

    Příklad: Zkopírujte soubor .zip na jednotku E:\ a rozbalte jej.
    E:\ASR nasazení Planner_v2.3.zip

    E:\ASR Nasazení Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="update-to-the-latest-version-of-deployment-planner"></a>Aktualizace na nejnovější verzi Plánovače nasazení

Nejnovější aktualizace jsou shrnuty v [historii verzí](site-recovery-deployment-planner-history.md)Plánovače nasazení .

Pokud máte předchozí verzi Plánovače nasazení, proveďte jednu z následujících akcí:
 * Pokud nejnovější verze neobsahuje opravu profilace a ve vaší stávající verzi Deployment Planneru již profilace probíhá, pokračujte v ní.
 * Pokud nejnovější verze obsahuje opravu profilace, doporučujeme zastavit profilaci ve stávající verzi a znovu ji spustit v nové verzi.


 >[!NOTE]
 >
 >Při spuštění profilace v nové verzi předejte stejnou cestu k výstupnímu adresáři, aby nástroj připojil data profilu k existujícím souborům. K vygenerování sestavy se použije úplná sada dat profilace. Pokud předáte jiný výstupní adresář, vytvoří se nové soubory a stará data profilace se k vygenerování sestavy nepoužijí.
 >
 >Každá nová verze Plánovače nasazení je kumulativní aktualizací souboru .zip. Nemusíte kopírovat nejnovější soubory do předchozí složky. Můžete vytvořit a použít novou složku.


## <a name="version-history"></a>Historie verzí
Nejnovější verze nástroje Site Recovery Deployment Planner je 2.5.
Opravy přidané v jednotlivých aktualizacích najdete na stránce [Historie verzí Plánovače nasazení služby Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-deployment-planner-history).

## <a name="next-steps"></a>Další kroky
[Spuštění Plánovače nasazení služby Site Recovery](site-recovery-vmware-deployment-planner-run.md)
