---
title: Odpovědi na časté dotazy
description: 'Odpovědi na běžné dotazy týkající se funkcí služby Azure Backup, včetně trezorů služby Recovery Services, co může zálohovat, jak to funguje, šifrování a omezení. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: 039666c4b54da6ac5444f5aa5acda5f1bb438782
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156051"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – Nejčastější dotazy

V tomto článku najdete odpovědi na běžné dotazy týkající se služby Azure Backup.

## <a name="recovery-services-vault"></a>Trezor služby Recovery Services

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Je v rámci předplatného Azure nějak omezený počet trezorů, které lze vytvořit?

Ano. Na jedno předplatné můžete vytvořit až 500 trezorů služby Recovery Services pro každou podporovanou oblast služby Azure Backup. Pokud potřebujete další trezory, vytvořte další předplatné.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Je nějak omezený počet serverů nebo počítačů, které lze zaregistrovat k trezoru?

K jednomu trezoru můžete zaregistrovat až 1000 počítačů. Pokud používáte Microsoft Azure Backup Agent, můžete zaregistrovat až 50 agentů MARS na trezor. A můžete zaregistrovat 50 Serverů MABS / DPM serverů do trezoru.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Kolik zdrojů dat nebo položek je možné chránit v jednom trezoru?

V jednom trezoru můžete chránit až 2000 zdrojů dat / položek napříč všemi úlohami (virtuální počítače IaaS, SQL, AFS atd.).
Pokud v trezoru například chráníte 500 virtuálních počítačů a 400 sdílených složek Azure Files, můžete v něm chránit jenom 1 100 databází SQL.

### <a name="how-many-policies-can-i-create-per-vault"></a>Kolik zásad je možné vytvořit pro každý trezor?

Na jeden trezor můžete mít až 200 zásad.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Pokud má moje organizace jeden trezor, jak se při obnovování dat dají izolovat data jednoho serveru od jiného?

Data serveru, která chcete obnovovat dohromady, by při nastavování zálohování měla používat stejné přístupové heslo. Pokud chcete obnovení izolovat na konkrétní server nebo servery, použijte přístupové heslo jenom pro tento server nebo servery. Například servery lidských zdrojů mohou používat jedno šifrovací heslo, účetní servery jiné a servery úložiště ještě jiné.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Dá se přesunout trezor mezi předplatnými?

Ano. Informace o přesunu trezoru služby Recovery Services najdete v tomto [článku](backup-azure-move-recovery-services-vault.md).

### <a name="can-i-move-backup-data-to-another-vault"></a>Dají se zálohovaná data přesunout do jiného trezoru?

Ne. Zálohovaná data uložená v trezoru se nedají přesunout do jiného trezoru.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Můžu po vytvoření zálohy změnit obnovení z GRS na LRS?

Ne. Trezor Recovery Services může měnit možnosti úložiště jenom před uložením jakýchkoli záloh.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Dá se pro virtuální počítače zálohované v trezoru služby Recovery Services použít obnovování na úrovni položek (Item Level Restore, ILR)?

- ILR se podporuje pro virtuální počítače Azure zálohované pomocí funkce zálohování virtuálních počítačů Azure. Další informace najdete v tomto [článku](backup-azure-restore-files-from-vm.md).
- ILR se nepodporuje pro body obnovení online místních virtuálních počítačů zálohovaných službou Azure Backup Server nebo System Center DPM.

## <a name="azure-backup-agent"></a>Agent Azure Backup

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Kde najdu běžné otázky týkající se agenta služby Azure Backup pro zálohování virtuálních počítačů Azure?

- Informace o agentovi spuštěném na virtuálních počítačích Azure najdete v těchto [nejčastějších dotazech](backup-azure-vm-backup-faq.md).
- Informace o agentovi použitém k zálohování složek Azure Files najdete v těchto [nejčastějších dotazech](backup-azure-file-folder-backup-faq.md).

## <a name="general-backup"></a>Obecné zálohování

### <a name="are-there-limits-on-backup-scheduling"></a>Existují pro plánování zálohování nějaká omezení?

Ano.

- Počítače s Windows Serverem nebo Windows můžete zálohovat až třikrát denně. Pro zásady plánování můžete nastavit denní nebo týdenní plány.
- DPM můžete zálohovat až dvakrát denně. Pro zásady plánování můžete nastavit denní, týdenní, měsíční nebo roční plány.
- Virtuální počítače Azure zálohujete jednou denně.

### <a name="what-operating-systems-are-supported-for-backup"></a>Které operační systémy se pro zálohování podporují?

Azure Backup podporuje pro zálohování souborů a složek a aplikací chráněných pomocí Azure Backup Serveru a DPM následující operační systémy.

**OS** | **Sku** | **Podrobnosti**
--- | --- | ---
Pracovní stanice | |
Windows 10, 64bitová verze | Enterprise, Pro, Home | Na počítačích by měly být spuštěné nejnovější aktualizace Service Pack a aktualizace služeb.
Windows 8.1, 64bitová verze | Enterprise, Pro | Na počítačích by měly být spuštěné nejnovější aktualizace Service Pack a aktualizace služeb.
Windows 8, 64bitová verze | Enterprise, Pro | Na počítačích by měly být spuštěné nejnovější aktualizace Service Pack a aktualizace služeb.
Windows 7, 64bitová verze | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Na počítačích by měly být spuštěné nejnovější aktualizace Service Pack a aktualizace služeb.
Server | |
Windows Server 2019, 64bitová verze | Standard, Datacenter, Essentials | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Server 2016, 64bitová verze | Standard, Datacenter, Essentials | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Server 2012 R2, 64bitová verze | Standard, Datacenter, Foundation | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Server 2012, 64bitová verze | Datacenter, Foundation, Standard | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Storage Server 2016, 64bitová verze | Standard, Workgroup | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Storage Server 2012 R2, 64bitová verze | Standard, Workgroup, Essential | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Storage Server 2012, 64bitová verze | Standard, Workgroup | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Server 2008 R2 SP1, 64bitová verze | Standard, Enterprise, Datacenter, Foundation | S nejnovějšími aktualizacemi.
Windows Server 2008, 64bitová verze | Standard, Enterprise, Datacenter | S nejnovějšími aktualizacemi.

Azure Backup nepodporuje 32bitové operační systémy.

Pro zálohy virtuálních počítačů Azure s Linuxem Azure Backup podporuje [seznam distribucí schválených pro Azure](../virtual-machines/linux/endorsed-distros.md), kromě základního OS Linux a 32bitového operačního systému. Další vlastní distribuce Linuxu mohou také fungovat, pokud je ve virtuálním počítači dostupný agent virtuálního počítače a pokud je podporovaný Python.

### <a name="are-there-size-limits-for-data-backup"></a>Existují nějaká omezení velikosti pro zálohování dat?

Platí tato omezení velikosti:

OS/počítač | Omezení velikosti zdroje dat
--- | ---
Windows 8 nebo novější | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 nebo novější | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Virtuální počítač Azure | 16 datových disků<br/> Pokud se chcete zaregistrovat k privátní verzi Preview virtuálních počítačů s víc než 16 disky (až 32 disků), napište nám na AskAzureBackupTeam@microsoft.com. <br><br> Datový disk až 32 TB

### <a name="how-is-the-data-source-size-determined"></a>Jak se určuje velikost zdroje dat?

Následující tabulka vysvětluje, jak je určená velikost jednotlivých zdrojů dat.

**Nastavení** | **Podrobnosti**
--- | ---
Svazek |Množství dat zálohovaných z jednoho svazku virtuálního počítače
Databáze SQL Serveru |Velikost jedné zálohované databáze SQL
SharePoint | Součet databází obsahu a konfiguračních databází v rámci zálohované farmy služby SharePoint
Výměna |Součet všech databází systému Exchange na zálohovaném serveru Exchange
BMR a stav systému |Každá jednotlivá kopie BMR nebo stavu systému zálohovaného počítače

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Existuje nějaké omezení množství dat zálohovaných s využitím trezoru služby Recovery Services?

Celkové množství dat, která lze zálohovat pomocí trezoru služby Recovery Services, není nijak omezeno. Jednotlivé zdroje dat (jiné než virtuální počítače Azure) mohou mít maximálně velikost 54 400 GB. Další informace o limitech naleznete [v části Omezení úložiště v matici podpory](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support).

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Proč je velikost dat přenášených do trezoru služby Recovery Services menší než velikost dat vybraných k zálohování?

Data zálohovaná z agenta služby Azure Backup, DPM nebo Azure Backup Serveru projdou před odesláním kompresí a šifrováním. Po kompresi a šifrování jsou data v trezoru o 30–40 % menší.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Dají se z bodu obnovení v trezoru odstranit jednotlivé soubory?

Ne, Azure Backup nepodporuje odstraňování nebo mazání jednotlivých položek z uložených záloh.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Když zruším úlohu zálohování poté, co už se spustila, dojde k odstranění přenášených dat?

Ne. Všechna data přenášená do trezoru před zrušením úlohy zálohování zůstanou v trezoru.

- Azure Backup používá mechanismus kontrolních bodů k příležitostnému přidávání kontrolních bodů do zálohovaných dat během zálohování.
- Díky kontrolním bodům v zálohovaných datech je možné při dalším procesu zálohování ověřit integritu souborů.
- Následující zálohování proběhne jako přírůstkové vzhledem k naposledy zálohovaným datům. Přírůstkové zálohování přenáší jen nová nebo změněná data, což znamená lepší využití přenosové kapacity.

Když ve virtuálním počítači Azure zrušíte úlohu zálohování, budou dosud přenesená data ignorována. Při následujícím přírůstkovém zálohování se přenesou data, která se změnila od poslední úspěšně dokončené úlohy zálohování.

## <a name="retention-and-recovery"></a>Uchovávání a obnovení

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Jsou zásady uchovávání pro počítače s DPM a počítače s Windows bez DPM stejné?

Ano, oba tyto typy mohou mít denní, týdenní, měsíční nebo roční zásady uchovávání.

### <a name="can-i-customize-retention-policies"></a>Dají se zásady uchovávání přizpůsobit?

Ano, zásady můžete přizpůsobovat. Můžete například nakonfigurovat požadavky na týdenní a denní uchovávání, ale ne na roční a měsíční.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Dají se pro plánování zálohování a zásady uchovávání používat jiné časy?

Ne. Zásady uchovávání informací lze aplikovat pouze na body záloh. Tento obrázek například ukazuje zásady uchovávání informací pro zálohy vytvořené ve 12:00 a 18:00.

![Plánování zálohování a uchovávání](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Pokud se záloha uchovává po dlouhou dobu, trvá pak obnovení staršího datového bodu déle?

Ne. Obnovení nejstaršího i nejnovějšího bodu trvá stejně dlouho. Každý bod obnovení se chová jako úplný bod.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Jestliže se každý bod obnovení chová jako úplný bod, ovlivní to celkové fakturovatelné úložiště zálohování?

Typické produkty s dlouhodobými body uchování ukládají zálohovaná data jako úplné body. 

- Úplné body jsou *neefektivní* z hlediska úložiště, ale jejich obnovení je snadnější a rychlejší.
- Přírůstkové kopie jsou *efektivní* z hlediska úložiště, ale vyžadují obnovení řetězu dat, což ovlivňuje dobu obnovení.

Architektura úložiště Azure Backup nabízí to nejlepší z obou světů – optimální ukládání dat pro rychlé obnovení a nízké poplatky za úložiště. Tento přístup zajišťuje efektivní využití příchozí i odchozí šířky pásma. Velikost úložiště dat i čas potřebný k obnovení dat jsou omezeny na minimum. Další informace o [přírůstkovém zálohování](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/)

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Je nějak omezený počet bodů obnovení, které lze vytvořit?

Na jednu chráněnou instanci je možné vytvořit až 9 999 bodů obnovení. Chráněná instance je počítač, server (fyzický nebo virtuální) nebo úloha zálohující do Azure.

- Další informace o [zálohování a uchovávání](./backup-support-matrix.md)

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Kolikrát se dají obnovit data zálohovaná do Azure?

Počet obnovení z Azure Backup není omezený.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Platí se za výchozí přenos z Azure při obnovování dat?

Ne. Obnovení je zdarma a výchozí přenos se vám neúčtuje.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Co se stane, když změním zásady zálohování?

Pokud se použije nová zásada, plán a uchovávání se budou řídit touto novou zásadou.

- Pokud se doba uchovávání prodlouží, existující body obnovení se označí k zachování pro novou zásadu.
- Pokud se doba uchovávání zkrátí, označí se k vyřazení v rámci další úlohy čištění a následně se odstraní.

## <a name="encryption"></a>Šifrování

### <a name="is-the-data-sent-to-azure-encrypted"></a>Jsou data odesílaná do Azure šifrovaná?

Ano. Data se na místním počítači šifrují pomocí AES256. Data se odesílají prostřednictvím zabezpečeného spojení HTTPS. Data přenášená v cloudu jsou propojením HTTPS chráněná jenom mezi úložištěm a službou Recovery Service. Data přenášená mezi službou Recovery Service a uživatelským počítačem zabezpečuje protokol iSCSI. K ochraně tunelu iSCSI se využívá zabezpečené tunelové propojení.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Jsou šifrovaná i zálohovaná data v Azure?

Ano. Data v Azure jsou v klidovém stavu zašifrovaná.

- V případě místního zálohování se šifrování v klidovém stavu zajišťuje pomocí hesla, které zadáte při zálohování do Azure.
- Pro virtuální počítače Azure jsou neaktivní uložená data zašifrovaná pomocí Šifrování služby Storage (SSE).

Microsoft nikdy nedešifruje zálohovaná data.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Jaká je minimální délka šifrovacího klíče, který se používá k šifrování zálohovaných dat?

Pokud používáte agenta Azure Backup, šifrovací klíč by měl obsahovat alespoň 16 znaků. Pro virtuální počítače Azure neplatí žádné omezení délky klíčů, které používá služba Azure Key Vault.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Co se stane, když ztratím šifrovací klíč? Můžu data obnovit? Může je obnovit Microsoft?

Klíč, pomocí kterého se šifrují zálohovaná data, je k dispozici jenom u vás. Microsoft neudržuje jeho kopii v Azure a nemá ke klíči žádný přístup. Pokud tento klíč ztratíte, Microsoft nemůže zálohovaná data obnovit.

## <a name="next-steps"></a>Další kroky

Přečtěte si další nejčastější dotazy:

- [Běžné dotazy](backup-azure-vm-backup-faq.md) týkající se zálohování virtuálních počítačů Azure
- [Běžné dotazy](backup-azure-file-folder-backup-faq.md) týkající se agenta Azure Backup
