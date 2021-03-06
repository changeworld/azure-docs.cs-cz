---
title: Nastavení zotavení po havárii fyzických místních serverů pomocí Azure Site Recovery
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro místní servery Windows a Linux pomocí služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 2f92c2b800c6d30cc5f365e6d24925a70d3db55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257924"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Nastavení zotavení po havárii do Azure pro místní fyzické servery

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

Tento kurz ukazuje, jak nastavit zotavení po havárii místních fyzických serverů Windows a Linux do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení požadavků na Azure a místní chod
> * Vytvoření trezoru služby Recovery Services pro Site Recovery 
> * Nastavení prostředí zdrojové a cílové replikace
> * Vytvoření zásady replikace
> * Povolení replikace pro server

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Ujistěte se, že rozumíte [architektuře a součástem](physical-azure-architecture.md) pro tento scénář.
- Zkontrolujte [požadavky na podporu](vmware-physical-secondary-support-matrix.md) pro všechny komponenty.
- Ujistěte se, že servery, které chcete replikovat, splňují [požadavky virtuálních zařízení Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Příprava Azure. Potřebujete předplatné Azure, virtuální síť Azure a účet úložiště.
- Připravte si účet pro automatickou instalaci služby Mobility na každém serveru, který chcete replikovat.

Než začnete, všimněte si, že:

- Po převzetí služeb při selhání do Azure, fyzické servery nelze selhat zpět do místních fyzických počítačů. Můžete jenom navrácení služeb po selhání virtuálních měn VMware. 
- Tento kurz nastaví zotavení po havárii fyzického serveru do Azure s nejjednodušším nastavením. Pokud se chcete dozvědět více o dalších možnostech, přečtěte si naše návody:
    - Nastavte [zdroj replikace](physical-azure-set-up-source.md), včetně konfiguračního serveru Site Recovery.
    - Nastavení [cíle replikace](physical-azure-set-up-target.md)
    - Konfigurace [zásady replikace](vmware-azure-set-up-replication.md) a [povolení replikace](vmware-azure-enable-replication.md)


### <a name="set-up-an-azure-account"></a>Nastavení účtu Azure

Pořízení [účtu](https://azure.microsoft.com/)Microsoft Azure .

- Můžete začít s [bezplatnou zkušební verzí](https://azure.microsoft.com/pricing/free-trial/).
- Přečtěte si informace o [cenách site recovery](site-recovery-faq.md#pricing)a získejte [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/site-recovery/).
- Zjistěte, které [oblasti jsou podporovány](https://azure.microsoft.com/pricing/details/site-recovery/) pro obnovení webu.

### <a name="verify-azure-account-permissions"></a>Ověření oprávnění účtu Azure

Ujistěte se, že váš účet Azure má oprávnění pro replikaci virtuálních počítačů do Azure.

- Zkontrolujte [oprávnění,](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) která potřebujete k replikaci počítačů do Azure.
- Ověřte a upravte přístupová oprávnění [založená na rolích.](../role-based-access-control/role-assignments-portal.md) 



### <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Nastavte [síť Azure](../virtual-network/quick-create-portal.md).

- Virtuální počítače Azure se umístí do této sítě, když se vytvoří po převzetí služeb při selhání.
- Síť by měla být ve stejné oblasti jako trezor služby Recovery Services.


## <a name="set-up-an-azure-storage-account"></a>Nastavení účtu úložiště Azure

Nastavte [účet úložiště Azure](../storage/common/storage-account-create.md).

- Site Recovery replikuje místní počítače do úložiště Azure. Virtuální počítače Azure se vytvoří z úložiště po převzetí služeb při selhání dojde.
- Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Služba Mobility musí být nainstalována na každém serveru, který chcete replikovat. Site Recovery nainstaluje tuto službu automaticky, když povolíte replikaci serveru. Chcete-li nainstalovat automaticky, je třeba připravit účet, který site recovery bude používat pro přístup k serveru.

- Můžete použít doménu nebo místní účet
- Pokud u virtuálních měn windows nepoužíváte účet domény, zakažte řízení vzdáleného přístupu uživatelů v místním počítači. Chcete-li to provést, přidejte do registru **v HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**položku DWORD **LocalAccountTokenFilterPolicy**s hodnotou 1.
- Chcete-li přidat položku registru a zakázat nastavení z příkazového příkazového příkazu, zadejte:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Pro Linux by měl být účet kořenna zdrojovém serveru Linux.


## <a name="create-a-vault"></a>Vytvoření trezoru

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Výběr cíle ochrany

Vyberte, co chcete replikovat, a replikujte ji.

1. Klikněte na **Trezory služby Recovery Services** > trezor.
2. V nabídce Zdroje klikněte na **položku Site Recovery** > **Prepare Infrastructure** > **Protection .**
3. V **cíli Ochrana**vyberte možnost **Azure** > **Není virtualizovaná/Jiná**.

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nastavte konfigurační server, zaregistrujte ho v trezoru a zjišťujte virtuální počítače.

1. Klikněte na **Site Recovery** > **Připravt infrastructure** > **Source**.
2. Pokud konfigurační server nemáte, klepněte na tlačítko **+Konfigurační server**.
3. V **části Přidat server**zkontrolujte, zda se **konfigurační server** zobrazuje v části Typ **serveru**.
4. Stáhněte instalační soubor sjednoceného instalačního programu pro obnovení webu.
5. Stáhněte registrační klíč trezoru. Potřebujete to při spuštění sjednoceného nastavení. Klíč je platný pět dní od jeho vygenerování.

   ![Nastavení zdroje](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrace konfiguračního serveru v úschovně

Než začnete, postupujte takto: 

#### <a name="verify-time-accuracy"></a>Ověření přesnosti času
V počítači konfiguračního serveru zkontrolujte, zda jsou systémové hodiny synchronizovány s [časovým serverem](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Mělo by se to shodovat. Pokud je to 15 minut vpředu nebo vzadu, nastavení může selhat.

#### <a name="verify-connectivity"></a>Ověření připojení
Ujistěte se, že počítač má přístup k těmto adresám URL na základě vašeho prostředí: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Pravidla brány firewall založené na IP adresách by měla umožňovat komunikaci se všemi adresami URL Azure, které jsou uvedeny výše přes port HTTPS (443). Chcete-li zjednodušit a omezit rozsahy IP adres, doporučujeme provést filtrování adres URL.

- **Komerční IP adresy** – povolte [rozsahy IP adres datového centra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)a port HTTPS (443). Povolte rozsahy IP adres pro oblast Azure vašeho předplatného pro podporu adres URL Služby AAD, Zálohování, Replikace a Úložiště.  
- **Ip adresy pro státní správu** – povolte [rozsahy IP adres Datového centra Azure vlády](https://www.microsoft.com/en-us/download/details.aspx?id=57063)a port HTTPS (443) pro všechny oblasti USGov (Virginie, Texas, Arizona a Iowa) pro podporu adres URL AAD, Zálohování, Replikace a Úložiště.  

#### <a name="run-setup"></a>Spuštění instalace
Spusťte sjednocené nastavení jako místního správce a nainstalujte konfigurační server. Procesní server a hlavní cílový server jsou také nainstalovány ve výchozím nastavení na konfiguračním serveru.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Po dokončení registrace se konfigurační server zobrazí na stránce **Nastavení** > **serverů** v úschovně.

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit cíl infrastruktury** > **Target**a vyberte předplatné Azure, které chcete použít.
2. Zadejte cílový model nasazení.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

   ![Cíl](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Chcete-li vytvořit novou zásadu replikace, klepněte na položku**Zásady replikace infrastruktury** >  **obnovení lokality** > **+Zásady replikace**.
2. V části **Vytvoření zásad replikace** zadejte název zásady.
3. V části **Prahová hodnota cíle bodu obnovení** (RPO) zadejte omezení cíle bodu obnovení. Tato hodnota určuje, jak často jsou vytvářeny body obnovení dat. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
4. V části **Uchování bodu obnovení** zadejte (v hodinách), jak dlouhý je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu. Pro počítače replikované do úložiště úrovně Premium se podporuje uchování po dobu až 24 hodin. V případě úložiště úrovně Standard je to 72 hodin.
5. V **četnosti snímků konzistentních s aplikací**určete, jak často (v minutách) budou vytvořeny body obnovení obsahující snímky konzistentní s aplikací. Kliknutím na tlačítko **OK** vytvořte zásadu.

    ![Zásady replikace](./media/physical-azure-disaster-recovery/replication-policy.png)


Tato zásada se automaticky přidruží ke konfiguračnímu serveru. Ve výchozím nastavení se pro navrácení služeb po obnovení automaticky vytvoří zásada párování. Pokud je například zásada replikace **zásadou opakování,** vytvoří se zásada navrácení služeb po **selhání, která by nabyla na znovunavrácení služeb** po selhání. Tato zásada se nepoužije, dokud nespustíte navrácení služeb po obnovení z Azure.

## <a name="enable-replication"></a>Povolení replikace

Povolte replikaci pro každý server.

- Obnovení sítě nainstaluje službu Mobility, pokud je povolena replikace.
- Povolíte-li replikaci serveru, může trvat 15 minut nebo déle, než se změny projeví a zobrazí se na portálu.

1. Klepněte na **položku Replikovat** > **zdroj**aplikace .
2. V části **Zdroj** vyberte konfigurační server.
3. V **pole Typ stroje**vyberte fyzické **stroje**.
4. Vyberte procesní server (konfigurační server). Pak klikněte na **OK**.
5. V **targetu**vyberte předplatné a skupinu prostředků, ve které chcete po převzetí služeb při selhání vytvořit virtuální počítače Azure. Zvolte model nasazení, který chcete použít v Azure (klasická správa prostředků nebo správa prostředků).
6. Vyberte účet úložiště Azure, který chcete použít pro replikaci dat. 
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se po převzetí služeb při selhání vytvoří.
8. Chcete-li použít nastavení sítě pro všechny počítače, které vyberete pro ochranu, vyberte **možnost Konfigurovat nyní pro vybrané počítače**. Chcete-li vybrat síť Azure pro každý počítač, **vyberte možnost Konfigurovat později.** 
9. Ve **fyzické matné a**klepněte na tlačítko **+Fyzický počítač**. Zadejte název a adresu IP. Vyberte operační systém počítače, který chcete replikovat. Trvá několik minut, než budou servery objeveny a uvedeny. 
10. V **části Vlastnosti** > **Konfigurace vlastností**vyberte účet, který bude zpracovávaný server používat k automatické instalaci služby Mobility do počítače.
11. V >  **nastavení replikace**Konfigurace nastavení**replikace**ověřte, zda je vybrána správná zásada replikace. 
12. Klepněte na tlačítko **Povolit replikaci**. Průběh úlohy **Povolit ochranu** můžete sledovat v části**Úlohy** > **obnovení webu** **nastavení** > . Po spuštění úlohy **Finalize Protection** je počítač připraven k převzetí služeb při selhání.


Chcete-li sledovat servery, které přidáte, můžete zkontrolovat čas posledního zjištění v **konfiguračních serverech** > **Poslední kontakt na**. Chcete-li přidat počítače bez čekání na naplánovaný čas zjišťování, zvýrazněte konfigurační server (neklikejte na něj) a klepněte na tlačítko **Aktualizovat**.

## <a name="next-steps"></a>Další kroky

[Spusťte cvičení zotavení po havárii](tutorial-dr-drill-azure.md).
