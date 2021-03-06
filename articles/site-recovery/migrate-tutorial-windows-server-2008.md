---
title: Migrace serverů Windows Server 2008 do Azure pomocí Azure Site Recovery
description: Tento článek popisuje migraci místních počítačů s Windows Serverem 2008 do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 20fe29a6588891c35520db01ac0403fb5b3a85d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73936140"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Migrace serverů s Windows Serverem 2008 do Azure

V tomto kurzu se dozvíte, jak migrovat místní servery s Windows Serverem 2008 nebo 2008 R2 do Azure pomocí Azure Site Recovery. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Příprava místního prostředí na migraci
> * Nastavení cílového prostředí
> * Nastavení zásady replikace
> * Povolení replikace
> * Spuštění testu migrace za účelem ověření, že vše funguje podle očekávání
> * Převzetí služeb při selhání do Azure s dokončení migrace

V části Omezení a známé problémy najdete několik omezení a alternativních řešení známých problémů, se kterými se můžete setkat při migraci počítačů s Windows Serverem 2008 do Azure. 

> [!NOTE]
> Teď můžete migrovat z místního do Azure pomocí služby Azure Migrate. [Další informace](../migrate/migrate-services-overview.md).


## <a name="supported-operating-systems-and-environments"></a>Podporované operační systémy a prostředí


|Operační systém  | Místní prostředí  |
|---------|---------|
|Windows Server 2008 SP2 – 32bitová a 64bitová verze(IA-32 a x86-64)</br>– Standard</br>– Enterprise</br>– Datacenter   |     Virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery    |
|Windows Server 2008 R2 SP1 – 64bitová verze</br>– Standard</br>– Enterprise</br>– Datacenter     |     Virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery|

> [!WARNING]
> - Migrace serverů se Server Core se nepodporuje.
> - Před migrací se ujistěte, že máte nainstalovanou nejnovější aktualizaci Service Pack a aktualizace Windows.


## <a name="prerequisites"></a>Požadavky

Než začnete, je vhodné se seznámit s architekturou Azure Site Recovery pro [migraci VMware a fyzických serverů](vmware-azure-architecture.md) nebo [migraci virtuálních počítačů Hyper-V](hyper-v-azure-architecture.md). 

Pokud chcete migrovat virtuální počítače Hyper-V s Windows Serverem 2008 nebo Windows Serverem 2008 R2, postupujte podle kroků v kurzu [migrace místních počítačů do Azure](migrate-tutorial-on-premises-azure.md).

Ve zbývající části tohoto kurzu se dozvíte, jak migrovat místní virtuální počítače VMware a fyzické servery s Windows Serverem 2008 nebo 2008 R2.
> [!TIP]
> Hledáte způsob, jak migrovat virtuální počítače VMware do Azure bez agentů? [Klikněte zde](https://aka.ms/migrateVMs-signup)


## <a name="limitations-and-known-issues"></a>Omezení a známé problémy

- Na konfiguračním serveru, dalších procesových serverech a ve službě Mobility, které použijete k migraci serverů s Windows Serverem 2008 R2, musí běžet software Azure Site Recovery verze 9.19.0.0 nebo novější.

- Body obnovení konzistentní vzhledem k aplikacím a funkce zajištění konzistence napříč několika virtuálními počítači se nepodporují u replikace serverů s Windows Serverem 2008 SP2. Servery s Windows Serverem 2008 SP2 se musí migrovat do bodu obnovení konzistentního vzhledem k selháním. Body obnovení konzistentní vzhledem k selháním se ve výchozím nastavení generují každých 5 minut. Použití zásady replikace s nakonfigurovanou frekvencí pořizování snímků konzistentních vzhledem k aplikacím způsobí kritický stav replikace kvůli chybějícím bodům obnovení konzistentním vzhledem k aplikacím. Pokud se chcete vyhnout falešně pozitivním výsledkům, nastavte frekvenci pořizování snímků konzistentních vzhledem k aplikacím v zásadě replikace na Vypnuto.

- Migrované servery musí obsahovat .NET Framework 3.5 s aktualizací Service Pack 1, aby služba Mobility fungovala.

- Pokud váš server obsahuje dynamické disky, možná si všimnete, že v určitých konfiguracích se tyto disky na serveru, u kterého došlo k převzetí služeb při selhání, označí jako offline nebo se zobrazí jako cizí disky. Také si můžete všimnout, že se stav zrcadlené sady u zrcadlených svazků napříč dynamickými disky označí jako Selhání redundance. Tento problém můžete vyřešit v nástroji diskmgmt.msc tím, že tyto disky ručně importujete a znovu je aktivujete.

- Migrované servery musí obsahovat ovladač vmstorfl.sys. Pokud na migrovaném serveru tento ovladač není, převzetí služeb při selhání může selhat. 
  > [!TIP]
  >Zkontrolujte, že se tento ovladač nachází v umístění C:\Windows\system32\drivers\vmstorfl.sys. Pokud se ovladač nenajde, můžete tento problém obejít tím, že ve stejném umístění vytvoříte fiktivní soubor. 
  >
  > Otevřete příkazový řádek (Spustit > cmd) a spusťte následující příkaz: copy nul c:\Windows\system32\drivers\vmstorfl.sys

- K serverům s 32bitovým operačním systémem Windows Server 2008 R2 se možná nebudete moct připojit pomocí protokolu RDP ihned poté, co u nich dojde k převzetí služeb při selhání nebo testovacímu převzetí služeb při selhání do Azure. Na webu Azure Portal restartujte virtuální počítač, u kterého došlo k převzetí služeb při selhání, a zkuste se připojit znovu. Pokud se pořád nemůžete připojit, zkontrolujte, jestli je server nakonfigurovaný tak, aby povoloval připojení ke vzdálené ploše, a ujistěte se, že připojení neblokují žádná pravidla brány firewall ani skupiny zabezpečení sítě. 
  > [!TIP]
  > Před migrací serverů se důrazně doporučuje provést testovací převzetí služeb při selhání. Ujistěte se, že jste provedli alespoň jeden úspěšný test převzetí služeb při selhání na každém serveru, který migrujete. V rámci testovacího převzetí služeb při selhání se připojte k počítači, u kterého došlo k testovacímu převzetí služeb při selhání, a zkontrolujte, že vše funguje podle očekávání.
  >
  >Operace testovacího převzetí služeb při selhání je nenarušující a pomáhá testovat migrace tím, že vytváří virtuální počítače v izolované síti podle vašeho výběru. Na rozdíl od operace převzetí služeb při selhání během operace testovacího převzetí služeb při selhání dál pokračuje replikace dat. Než budete připraveni na migraci, můžete provést tolik testovacích převzetí služeb při selhání, kolik budete chtít. 
  >
  >


## <a name="getting-started"></a>Začínáme

Provedením následujících úloh připravte předplatné Azure a VMware v místním prostředí nebo fyzické prostředí:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. Příprava místního [vmware](vmware-azure-tutorial-prepare-on-premises.md)


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. Přihlaste se ke**službě Obnovení** [portálu](https://portal.azure.com) > Azure .
2. Klepněte **na tlačítko Vytvořit** > nástroje**pro** > správu prostředků Backup a Site**Recovery**.
3. Do pole **Název** zadejte popisný název **W2K8-migration**. Pokud máte více předplatných, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků **w2k8migrate**.
5. Zadejte oblast Azure. Pokud chcete zkontrolovat podporované oblasti, přečtěte si informace o geografické dostupnosti v [tématu Podrobnosti o cenách obnovení webu Azure](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Pokud chcete mít možnost rychle se dostat k trezoru z řídicího panelu, klikněte na **Připnout na řídicí panel** a potom klikněte na **Vytvořit**.

   ![Nový trezor](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Nový trezor se přidá do oblasti **Řídicí panel** v části **Všechny prostředky** a na hlavní stránku **Trezory Recovery Services**.


## <a name="prepare-your-on-premises-environment-for-migration"></a>Příprava místního prostředí na migraci

- Pokud chcete migrovat virtuální počítače s Windows Serverem 2008 spuštěné ve VMware, [nastavte místní konfigurační server ve VMware](vmware-azure-tutorial.md#set-up-the-source-environment).
- Pokud konfigurační server není možné nastavit jako virtuální počítač VMware, [nastavte konfigurační server na místním fyzickém serveru nebo virtuálním počítači](physical-azure-disaster-recovery.md#set-up-the-source-environment).

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit cíl infrastruktury** > **Target**a vyberte předplatné Azure, které chcete použít.
2. Zadejte model nasazení Resource Manager.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.


## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Chcete-li vytvořit novou zásadu replikace, klepněte na položku**Zásady replikace infrastruktury** >  **obnovení lokality** > **+Zásady replikace**.
2. V části **Vytvoření zásad replikace** zadejte název zásady.
3. V části **Prahová hodnota cíle bodu obnovení** (RPO) zadejte omezení cíle bodu obnovení. Když cíl bodu obnovení replikace překročí tento limit, vygeneruje se upozornění.
4. V části **Uchování bodu obnovení** zadejte (v hodinách), jak dlouhý je interval uchovávání dat pro jednotlivé body obnovení. Replikované servery lze obnovit do libovolného bodu v tomto okně. Pro počítače replikované do úložiště úrovně Premium se podporuje uchování po dobu až 24 hodin. V případě úložiště úrovně Standard je to 72 hodin.
5. V části **Frekvence pořizování snímků konzistentních vzhledem k aplikacím** zadejte **Vypnuto**. Kliknutím na tlačítko **OK** vytvořte zásadu.

Tato zásada se automaticky přidruží ke konfiguračnímu serveru.

> [!WARNING]
> Ujistěte se, že v nastavení Frekvence pořizování snímků konzistentních vzhledem k aplikacím v zásadě replikace zadáte **VYPNUTO**. Při replikaci serverů s Windows Serverem 2008 se podporují pouze body obnovení konzistentní vzhledem k selháním. Zadání jakékoli jiné hodnoty pro frekvenci snímek konzistentní aplikace bude mít za následek falešné výstrahy otočením stavu replikace serveru kritické z důvodu nedostatku bodů obnovení konzistentní aplikace.

   ![Vytvoření zásady replikace](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>Povolení replikace

[Povolte replikaci](physical-azure-disaster-recovery.md#enable-replication) pro server s Windows Serverem 2008 SP2 nebo Windows Serverem 2008 R2 SP1, který se má migrovat.
   
   ![Přidání fyzického serveru](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Povolení replikace](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>Spuštění testu migrace

Po dokončení počáteční replikace a změně stavu serveru na **Chráněno** můžete provést testovací převzetí služeb při selhání replikovaných serverů.

Spusťte [testovací převzetí služeb při selhání](tutorial-dr-drill-azure.md) do Azure a ujistěte se, že vše funguje podle očekávání.

   ![Testovací převzetí služeb při selhání](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>Migrace do Azure

Spusťte převzetí služeb při selhání pro počítače, které chcete migrovat.

1. V **nastavení** > **Replikované položky** klepněte na počítač > **převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Vyberte nejnovější bod obnovení.
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí server vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Zkontrolujte, že se virtuální počítač Azure zobrazuje v Azure podle očekávání.
5. V **části Replikované položky**klepněte pravým tlačítkem myši na server > **Úplná migrace**. Tím postupujte takto:

    - Dokončí proces migrace, zastaví replikaci serveru a zastaví fakturaci obnovení webu pro obsluhu.
    - Tento krok vyčistí data replikace. Neodstraní migrované virtuální aplikace.

   ![Dokončení migrace](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Nerušit probíhající převzetí služeb při selhání**: Replikace serveru je zastavena před spuštěním převzetí služeb při selhání. Pokud probíhá převzetí služeb při selhání, převzetí služeb při selhání se zastaví, ale server nebude pokračovat v replikaci.
