---
title: Migrace virtuálních počítačů AWS do Azure pomocí služby Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje migraci virtuálních počítačů s Windows na platformě Amazon Web Services (AWS) do Azure pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 929bc0695bda2e64f77f7e9286e06cee787822ba
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388963"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrace virtuálních počítačů služby Amazon Web Services (AWS) do Azure

Tento kurz ukazuje, jak migrovat virtuální počítače Amazon Web Services (AWS) do virtuálních počítačů Azure pomocí Azure Site Recovery. Při migraci instancí EC2 služby AWS do Azure se virtuální počítače zpracovávají jako místní fyzické počítače. V tomto kurzu se naučíte:


> [!TIP]
> Teď byste měli použít službu Azure Migrate k migraci virtuálních počítačích AWS do Azure, namísto služby Azure Site Recovery. [Další informace](../migrate/tutorial-migrate-physical-virtual-machines.md).


> [!div class="checklist"]
> * Ověření požadavků
> * Příprava prostředků Azure
> * Připravit instance EC2 služby AWS na migraci
> * Nasazení konfiguračního serveru
> * Povolení replikace pro virtuální počítače
> * Otestování převzetí služeb při selhání, aby bylo jisté, že všechno funguje.
> * Spustit jednorázové převzetí služeb při selhání do Azure

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.


## <a name="prerequisites"></a>Požadavky
- Ověřte, že na virtuálních počítačích, které chcete migrovat, běží podporovaná verze operačního systému. Podporované verze: 
  - Windows Server 2016 
  - Windows Server 2012 R2
  - Windows Server 2012 
  - 64bitová verze systému Windows Server 2008 R2 SP1 nebo novější
  - Red Hat Enterprise Linux 6.4 až 6.10, 7.1 až 7.6 (pouze virtualizované instance HVM) *(instance s ovladači RedHat PV nejsou podporovány.)*
  - CentOS 6.4 až 6.10, 7.1 až 7.6 (pouze virtualizované instance HVM)
 
- Na každý virtuální počítač, který chcete replikovat, bude nutné nainstalovat službu Mobility. 

    > [!IMPORTANT]
    > Služba Site Recovery tuto službu nainstaluje automaticky, když u virtuálního počítače zapnete replikaci. U automatické instalace musíte připravit účet na instancích EC2, který služba Site Recovery použije k získání přístupu k virtuálnímu počítači. Můžete použít účet domény nebo místní účet. 
    > - U virtuálních počítačů s Linuxem by měl být na zdrojovém serveru s Linuxem účet superuživatel. 
    > - Pokud u virtuálních počítačů s Windows nepoužíváte doménový účet, zakažte na místním počítači vzdálené řízení přístupu uživatele:
    >
    >      V registru přidejte v části **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** položku DWORD **LocalAccountTokenFilterPolicy** a hodnotu nastavte na **1**.

- Samostatná instance EC2, kterou můžete používat jako konfigurační server služby Site Recovery. Tato instance musí běžet na Windows Serveru 2012 R2.

## <a name="prepare-azure-resources"></a>Příprava prostředků Azure

V Azure je potřeba mít připravených několik prostředků, které budou migrované instance EC2 potřebovat. Patří mezi ně účet úložiště, trezor a virtuální síť.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Image replikovaných počítačů jsou uložené ve službě Azure Storage. Virtuální počítače Azure se vytvoří z úložiště, když převezmete služby při selhání z místní lokality do Azure.

1. V levé nabídce webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Úložiště** > **Účet úložiště**.
2. Zadejte název účtu úložiště. V těchto kurzech používáme název **awsmigrated2017**. Název musí:
    - Být v Azure jedinečný
    - Mít délku 3 až 24 znaků
    - Obsahovat jenom číslice a malá písmena
3. V polích pro **model nasazení**, **druh účtu**, **výkon** a **vyžadování zabezpečeného přenosu** nechte výchozí možnosti.
5. V poli **Replikace** vyberte výchozí možnost **RA-GRS** (geograficky redundantní úložiště jen pro čtení).
6. Vyberte předplatné, které chcete v tomto kurzu použít.
7. V poli **Skupina prostředků** vyberte **Vytvořit novou**. V tomto příkladu používáme pro skupinu prostředků název **migrationRG**.
8. Jako **umístění** vyberte **Západní Evropa**.
9. Vyberte **Vytvořit** a vytvořte účet úložiště.

### <a name="create-a-vault"></a>Vytvoření trezoru

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Všechny služby**. Vyhledejte a pak vyberte **trezory služby Recovery Services**.
2. Na stránce trezorů služby Azure Recovery Services vyberte **Přidat**.
3. Do pole **Název** zadejte **myVault**.
4. V rozevíracím seznamu **Předplatné** vyberte předplatné, které chcete použít.
4. V části **Skupina prostředků** vyberte **Použít existující** a potom vyberte **migrationRG**.
5. Jako **umístění** vyberte **Západní Evropa**.
5. Pokud chcete mít k novému trezoru rychlý přístup z řídicího panelu, vyberte možnost **Připnout na řídicí panel**.
7. Až to budete mít, vyberte **Vytvořit**.

Chcete-li zobrazit nový trezor, přejděte na **řídicí panel** > **Všechny prostředky**. Nový trezor se také zobrazí na hlavní stránce **Trezory služby Recovery Services**.

### <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Když se po migraci vytvoří virtuální počítače Azure (převzetí služeb při selhání), připojí se do této sítě Azure.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Sítě** >
   **Virtuální síť**.
3. Do pole **Název** zadejte **myMigrationNetwork**.
4. Výchozí hodnotu ponechte pro **adresní prostor** (musí být zadejte hodnotu).
5. V rozevíracím seznamu **Předplatné** vyberte předplatné, které chcete použít.
6. Ve **skupině Prostředků**vyberte Použít **existující**a pak **vyberte migrationRG**.
7. Jako **umístění** vyberte **Západní Evropa**.
8. V **části Podsíť**ponechte výchozí hodnoty pro **Název** a rozsah IP **adres (musí zadat hodnotu).**
9. Přidejte pokyny pro nastavení ochrany DDoS.
10. Možnost **Koncové body služby** nechte zakázanou.
11. Přidejte pokyny pro nastavení brány firewall.
12. Až to budete mít, vyberte **Vytvořit**.

## <a name="prepare-the-infrastructure"></a>Příprava infrastruktury

Na stránce trezoru na webu Azure Portal vyberte v části **Začínáme** možnost **Site Recovery** a potom vyberte **Příprava infrastruktury**. Proveďte následující kroky.

### <a name="1-protection-goal"></a>1: Cíl ochrany

Na stránce **Cíl ochrany** vyberete následující hodnoty:

|    |  |
|---------|-----------|
| Kde se vaše počítače nachází? |Vyberte možnost pro **místní řešení**.|
| Kam chcete své počítače replikovat? |Vyberte možnost **připojení k Azure**.|
| Provádíte migraci? | Vyberte **Ano**a zaškrtněte políčko vedle **I underdstand, ale chtěl bych pokračovat s Azure Site Recovery.**
| Jsou vaše počítače virtualizované? |Vyberte **Nevirtualizované nebo jiné**.|

Jakmile to budete mít, přejděte zvolením **OK** na další krok.

### <a name="2-select-deployment-planning"></a>2: Vyberte plánování nasazení

Na stránce **Dokončili jste plánování nasazení?** vyberte **Udělám to později** a potom vyberte **OK**.

### <a name="3-prepare-source"></a>3: Připravte zdroj

Na stránce **Připravit zdroj** vyberte **+ Konfigurační server**.

1. Použijte instanci EC2, na které běží Windows Server 2012 R2, abyste vytvořili konfigurační server a zaregistrovali ho ve svém trezoru služby Site Recovery.
2. Na virtuálním počítači instance EC2, který používáte jako konfigurační server, nakonfigurujte proxy, aby měl přístup k [adresám URL služby](site-recovery-support-matrix-to-azure.md).
3. Stáhněte si [sjednocenou instalaci Microsoft Azure Site Recovery](https://aka.ms/unifiedinstaller_wus). Můžete ji stáhnout na místní počítač a pak ji zkopírovat na virtuální počítač, který používáte jako konfigurační server.
4. Vyberte tlačítko **Stáhnout** a stáhněte si registrační klíč trezoru. Zkopírujte stažený soubor do virtuálního počítače, který používáte jako konfiguračních server.
5. Na tomto virtuálním počítači klikněte pravým tlačítkem na stažený instalační soubor sjednocené instalace Microsoft Azure Site Recovery a pak vyberte **Run as administrator** (Spustit jako správce).

    1. Na stránce **Before You Begin** (Než začnete) vyberte **Install the configuration server and process server** (Nainstalovat konfigurační server a procesový server) a potom vyberte **Next** (Další).
    2. Na stránce **Third-Party Software License** (Licence k softwaru jiného výrobce) vyberte **I accept the third-party license agreement** (Přijímám licenční smlouvu třetí strany) a potom vyberte **Next** (Další).
    3. Na stránce **registrace** vyberte **Browse** (Procházet) a potom přejděte do umístění se souborem registračního klíče trezoru. Vyberte **další**.
    4. Na stránce **Internet Settings** (Nastavení internetu) vyberte **Connect to Azure Site Recovery without a proxy server** (Připojit k Azure Site Recovery bez proxy serveru) a pak vyberte **Next** (Další).
    5. Na stránce **Prerequisites Check**(Kontrola předpokladů) provede program kontrolu několika položek. Po dokončení vyberte **Next** (Další).
    6. Na stránce **MySQL Configuration** (Konfigurace MySQL) zadejte požadovaná hesla a potom vyberte **Next** (Další).
    7. V části **Environment Details** (Podrobnosti prostředí) vyberte **No** (Ne). Počítače VMware nemusíte chránit. Potom vyberte **Další**.
    8. Na stránce **Install Location** (Umístění instalace) přijměte zvolením možnosti **Next** (Další) výchozí umístění.
    9. Na stránce **Network Selection** (Výběr sítě) přijměte zvolením možnosti **Next** (Další) výchozí síť.
    10. V části **Summary** (Souhrn) vyberte **Install** (Nainstalovat).
    11. V části **Installation Progress** (Průběh instalace) vidíte, jak je instalace daleko. Po dokončení vyberte **Finish** (Dokončit). Zobrazí se okno se zprávou o restartování. Vyberte **OK**. V dalším kroku se zobrazí okno s heslem pro připojení ke konfiguračnímu serveru. Zkopírujte heslo do schránky a bezpečně si ho uložte.
6. Na virtuálním počítači spusťte program cspsconfigtool.exe, pomocí kterého vytvoříte na konfiguračním serveru jeden nebo více účtů pro správu. Ujistěte se, že účty pro správu mají oprávnění správce na instancích EC2, které chcete migrovat.

Po dokončení nastavení konfiguračního serveru přejděte zpátky na portál Azure Portal a vyberte server, který jste vytvořili jako **Konfigurační server**. Zvolením možnosti **OK** přejděte na krok 3: Příprava cíle.

### <a name="4-prepare-target"></a>4: Připravte cíl

V této části zadáte informace o prostředcích, které jste vytvořili v předchozích krocích tohoto kurzu v části [Příprava prostředků Azure](#prepare-azure-resources).

1. Na stránce **Předplatné** vyberte požadované předplatné Azure, které jste použili v kurzu [Příprava Azure](tutorial-prepare-azure.md).
2. Jako model nasazení vyberte **Resource Manager**.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure. Mělo by se jednat o prostředky, které jste vytvořili v předchozích krocích tohoto kurzu v části [Příprava prostředků Azure](#prepare-azure-resources).
4. Až to bude hotové, vyberte **OK**.

### <a name="5-prepare-replication-settings"></a>5: Příprava nastavení replikace

Před povolením replikace musíte vytvořit zásady replikace.

1. Vyberte **Vytvořit a přidružit**.
2. Do pole **Name** (Název) zadejte **myReplicationPolicy**.
3. Ve zbývajících polích nechte výchozí nastavení a zvolením možnosti **OK** vytvořte zásady. Nová zásada se automaticky přidruží ke konfiguračnímu serveru.

Po dokončení všech pěti kroků v části **Příprava infrastruktury** vyberte **OK**.

## <a name="enable-replication"></a>Povolení replikace

Povolte replikaci pro všechny virtuální počítače, které chcete migrovat. Když je replikace povolená, služba Site Recovery automaticky nainstaluje službu Mobility.

1. Přejděte na [portál Azure](https://portal.azure.com).
1. Na stránce svého trezoru v části **Začínáme** vyberte **Site Recovery**.
2. V části **Pro místní počítače a virtuální počítače Azure** vyberte **Krok 1: Replikace aplikace**. Na stránkách průvodce zadejte následující informace. Po zadání údajů na každé stránce zvolte **OK**.
   - 1: Konfigurace zdroje

     |  |  |
     |-----|-----|
     | Zdroj: | Zvolte **Místní**.|
     | Zdrojové umístění:| Zadejte název instance EC2 konfiguračního serveru.|
     |Typ počítače: | Zvolte **Fyzické počítače**.|
     | Procesový server: | Z rozevíracího seznamu vyberte konfigurační server.|

   - 2: Konfigurace cíle

     |  |  |
     |-----|-----|
     | Cíl: | Nechte zadanou výchozí hodnotu.|
     | Předplatné: | Zvolte předplatné, které jste dosud používali.|
     | Skupina prostředků po převzetí služeb při selhání:| Použijte skupinu prostředků, kterou jste vytvořili v části [Příprava prostředků Azure](#prepare-azure-resources).|
     | Model nasazení po převzetí služeb při selhání: | Zvolte **Resource Manager** (Správce prostředků).|
     | Účet úložiště: | Vyberte účet úložiště, který jste vytvořili v části [Příprava prostředků Azure](#prepare-azure-resources).|
     | Síť Azure: | Zvolte **Nakonfigurovat pro vybrané počítače**.|
     | Síť Azure po převzetí služeb při selhání: | Zvolte síť, kterou jste vytvořili v části [Příprava prostředků Azure](#prepare-azure-resources).|
     | Podsíť: | V rozevíracím seznamu vyberte **výchozí**.|

   - 3: Výběr fyzických počítačů

     Vyberte **Fyzický počítač** a zadejte **název**, **IP adresu** a **typ operačního systému** instance EC2, kterou chcete migrovat. Vyberte **OK**.

   - 4: Konfigurace vlastností

     Vyberte účet, který jste vytvořili na konfiguračním serveru, a potom zvolte **OK**.

   - 5: Konfigurace nastavení replikace

     Ujistěte se, že v rozevíracím seznamu pro zásady replikace máte vybranou možnost **myReplicationPolicy**, a potom zvolte **OK**.

3. Po dokončení průvodce zvolte **Povolit replikaci**.

Průběh úlohy **Povolení ochrany** můžete sledovat tady: **Sledování a sestavy** > **Úlohy** > **Úlohy Site Recovery**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.        

Když virtuálnímu počítači povolíte replikaci, může trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Když spustíte testovací převzetí služeb při selhání, dojde k následujícím událostem:

- Spustí se kontrola předpokladů, která zjistí, zda jsou splněné všechny podmínky pro převzetí služeb při selhání.
- Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
- Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.

Spusťte na portálu test převzetí služeb při selhání:

1. Na stránce trezoru přejděte na **Položky** > **replikované položky chráněné**. Vyberte virtuální počítač a potom zvolte **Testovací převzetí služeb při selhání**.
2. Vyberte bod obnovení, který chcete pro převzetí služeb při selhání použít:
    - **Nejnovější zpracovaný**: Vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Zobrazí se časové razítko. U této možnosti nedochází k prodlevě při zpracování dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
    - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost vrátí všechny virtuální počítače k nejnovějšímu konzistentnímu bodu obnovení vzhledem k aplikacím. Zobrazí se časové razítko.
    - **Vlastní**: Vyberete si libovolný bod obnovení.

3. V části **Testovací převzetí služeb při selhání** vyberte cílovou síť Azure, ke které se virtuální počítače Azure po převzetí služeb při selhání připojí. Měla by to být síť, kterou jste vytvořili v části [Příprava prostředků Azure](#prepare-azure-resources).
4. Zvolením možnosti **OK** zahajte převzetí služeb při selhání. Pokud chcete sledovat průběh, otevřete zvolením virtuálního počítače jeho vlastnosti. Nebo můžete vybrat úlohu **Testovací převzetí služeb při selhání** na stránce trezoru. Chcete-li to provést, vyberte **možnost Monitorování a sestavu** > **úloh obnovení****webu** >  .
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal objeví replika virtuálního počítače Azure. Pokud chcete zobrazit tento virtuální počítač, vyberte **Virtuální počítače**. Ujistěte se, že má virtuální počítač odpovídající velikost, je připojený ke správné síti a je spuštěný.
6. Nyní byste se měli moct k replikovanému virtuálnímu počítači v Azure připojit.
7. Virtuální počítače Azure vytvořené během testu převzetí služeb při selhání odstraníte zvolením možnosti **Vyčistit testovací převzetí služeb při selhání** v plánu obnovení. V **poznámkách**zaznamenejte a uložte všechna pozorování spojená s převzetím služeb při selhání testu.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování. Zpracování se dokončí za 8 až 10 minut.

## <a name="migrate-to-azure"></a>Migrace do Azure

Spusťte skutečné převzetí služeb při selhání pro instance EC2 a proveďte jejich migraci na virtuální počítače Azure:

1. V části**Replikované položky** **chráněné položky** > vyberte instance AWS a pak vyberte **převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Vyberte nejnovější bod obnovení a spusťte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
1. Ujistěte se, že se virtuální počítač zobrazuje na stránce **Replikované položky**.
2. Klikněte pravým tlačítkem na každý virtuální počítač a vyberte **Dokončit migraci**. Tím postupujte takto:

   - Tím se dokončí proce migrace, zastaví se replikace virtuálního počítače AWS a zastaví se fakturace služby Site Recovery pro daný virtuální počítač.
   - Tento krok vyčistí data replikace. Neodstraní migrované virtuální aplikace. 

     ![Dokončení migrace](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> *Nepřerušujte probíhající převzetí služeb při selhání*. Před zahájením převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud probíhající proces převzetí služeb při selhání přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.  


## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak migrovat instance ES2 AWS do virtuálních počítačů Azure. Další informace o virtuálních počítačích Azure najdete v kurzech pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](../virtual-machines/windows/tutorial-manage-vm.md)
