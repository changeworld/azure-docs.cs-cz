---
title: Přehled SQL Serveru na virtuálních počítačích Azure s Windows | Dokumentace Microsoftu
description: Můžete se dozvědět, jak spouštět úplné edice SQL Serveru virtuálních počítačích Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7d8d1505a268976161636abd0ed2d24398978284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374288"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Co je SQL Server na virtuálních počítačích Azure? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

[SQL Server na virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) umožňuje používat plné verze SQL Serveru v cloudu, aniž byste museli spravovat místní hardware. Pokud platíte průběžně, pro virtuální počítače s SQL Serverem se také zjednoduší náklady na licencování.

Virtuální počítače Azure běží v mnoha různých [geografických oblastech](https://azure.microsoft.com/regions/) po celém světě. Také nabízejí celou řadu [velikostí počítačů](../sizes.md). Galerie imagí virtuálních počítačů umožňuje vytvoření virtuálního počítače s SQL Serverem, který má správnou verzi, vydání i operační systém. Díky tomu jsou virtuální počítače vhodné pro mnoho různých úloh SQL Serveru.

## <a name="automated-updates"></a>Automatizované aktualizace

Virtuální počítače Azure s SQL Serverem mohou používat [Automatické opravy](virtual-machines-windows-sql-automated-patching.md) k naplánování okna údržby pro automatickou instalaci důležitých oken a aktualizace SQL serveru.

## <a name="automated-backups"></a>Automatizované zálohy

Virtuální počítače Azure s SQL Serverem mohou využívat výhod [automatizovaného zálohování](virtual-machines-windows-sql-automated-backup-v2.md), které pravidelně vytváří zálohy databáze do Blob Storage. Tento postup můžete použít také ručně. Další informace najdete v tématu [Použití služby Azure Storage pro zálohování a obnovování SQL Serveru](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>Vysoká dostupnost

Pokud požadujete vysokou dostupnost, doporučujeme nakonfigurovat skupiny dostupnosti SQL Serveru. Znamená to využívat více virtuálních počítačů Azure s SQL Serverem ve virtuální síti. Řešení vysoké dostupnosti můžete nakonfigurovat ručně, nebo můžete použít šablony na webu Azure Portal pro automatickou konfiguraci. Přehled možností vysoké dostupnosti najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Výkon

Virtuální počítače Azure nabízejí různé kapacity, takže splňují různé požadavky na pracovní zatížení. Virtuální počítače SQL také poskytují automatickou konfiguraci úložiště, která je optimalizovaná pro vaše požadavky na výkon. Další informace o konfiguraci úložiště pro virtuální počítače na serveru SQL najdete v tématu [Konfigurace úložiště pro virtuální počítače SQL Serveru](virtual-machines-windows-sql-server-storage-configuration.md). Pokud budete chtít ladit výkon, přečtěte si [Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>Začínáme s virtuálními počítači SQL

Chcete-li začít, zvolte image virtuálního počítače s SQL Serverem s požadovanou verzí, vydáním a operačním systémem. Následující oddíly poskytují přímé odkazy na image v galerii virtuálních počítačů s SQL Serverem na webu Azure Portal.

> [!TIP]
> Další informace pro lepší pochopení cen imagí SQL najdete v [doprovodných materiálech k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a>Plaťte průběžně
Následující tabulka obsahuje matici imagí SQL Serveru s průběžnými platbami.

| Version | Operační systém | Edice |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Organizace](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Vývojář](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Dostupné image virtuálních počítačů s Linuxem a SQL Serverem najdete v tématu [Přehled SQL Serveru na virtuálních počítačích Azure (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Nyní je možné změnit licenční model virtuálního počítače SQL Server s platbou za použití a použít vlastní licenci. Další informace naleznete [v tématu Jak změnit model licencování pro virtuální virtuální modul SQL](virtual-machines-windows-sql-ahb.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Používání vlastní licence
Můžete také používat vlastní licenci (BYOL). V tomto scénáři zaplatíte jenom za virtuální počítač bez jakýchkoli dalších poplatků za licencování SQL Serveru.  Používáním vlastní licence můžete časem ušetřit peníze za nepřetržité produkční úlohy. Požadavky k použití této možnosti najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md#byol).

Chcete-li přenést vlastní licenci, můžete buď převést existující virtuální počítač SQL s platbou za použití, nebo můžete nasadit bitovou kopii s předponou **{BYOL}**. Další informace o přepínání licenčního modelu mezi platbami za použití a BYOL najdete [v tématu Jak změnit licenční model pro virtuální počítač SQL](virtual-machines-windows-sql-ahb.md). 

| Version | Operační systém | Edice |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Podnik BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standardní BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Podnik BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standardní BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Podnik BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standardní BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

Je možné nasadit starší image SQL Serveru, který není k dispozici na webu Azure Portal pomocí PowerShellu. Chcete-li zobrazit všechny dostupné obrázky pomocí powershellu, použijte následující příkaz:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Další informace o nasazení virtuálních počítačů SQL Server pomocí PowerShellu našel v článku [Jak zřídit virtuální počítače SQL Serveru pomocí Azure PowerShellu](virtual-machines-windows-ps-sql-create.md).


### <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači
Po vytvoření virtuálního počítače s SQL Serverem se k němu připojte z aplikací nebo nástrojů, jako je například SQL Server Management Studio (SSMS). Pokyny najdete v tématu [Připojení k virtuálnímu počítači SQL Serveru na Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrace dat
Pokud máte existující databázi, bude ji nutné přesunout do nově zřízeného virtuálního počítače s SQL Serverem. Seznam možností migrace a pokyny najdete v části [Migrace databáze do SQL Serveru ve virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Vytváření a správa prostředků Azure SQL pomocí portálu Azure

Portál Azure poskytuje jednu stránku, kde můžete spravovat [všechny prostředky Azure SQL,](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) včetně virtuálních počítačů SQL.

Pokud chcete získat přístup na stránku **prostředků Azure SQL,** vyberte **Azure SQL** v nabídce portálu Azure nebo vyhledejte a vyberte Azure **SQL** z libovolné stránky.

![Hledání Azure SQL](./media/quickstart-sql-vm-create-portal/search-for-azure-sql.png)

> [!NOTE]
> **Azure SQL** poskytuje rychlý a snadný způsob přístupu ke všem databázím SQL, elastickým fondům, databázovým serverům, instancím spravovaným SQL a virtuálním počítačům SQL. Azure SQL není služba nebo prostředek. 

Chcete-li spravovat existující zdroje, vyberte požadovanou položku v seznamu. Chcete-li vytvořit nové prostředky Azure SQL, vyberte **+ Přidat**. 

![Vytvoření prostředku Azure SQL](./media/quickstart-sql-vm-create-portal/create-azure-sql-resource.png)

Po výběru **+ Přidat**zobrazte další informace o různých možnostech výběrem **možnosti Zobrazit podrobnosti** na libovolné dlaždici.

![databáze dlaždice podrobnosti](./media/quickstart-sql-vm-create-portal/sql-vm-details.png)

Podrobnosti najdete tady:

- [Vytvoření jedné databáze](../../../sql-database/sql-database-single-database-get-started.md)
- [Vytvoření elastického fondu](../../../sql-database/sql-database-elastic-pool.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Vytvoření spravované instance](../../../sql-database/sql-database-managed-instance-get-started.md)
- [Vytvoření virtuálního počítače SQL](quickstart-sql-vm-create-portal.md)

## <a name="sql-vm-image-refresh-policy"></a><a id="lifecycle"></a>Zásady aktualizace imagí virtuálních počítačů SQL
Azure pro každou podporovanou kombinaci operačního systému, jeho verze a edice udržuje pouze jednu image virtuálního počítače. To znamená, že se image v průběhu času aktualizují a staré image se odebírají. Další informace najdete v části **Image** tématu [Nejčastější dotazy k virtuálním počítačům s SQL Serverem](virtual-machines-windows-sql-server-iaas-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Program Zlepšování softwaru a služeb na základě zkušeností uživatelů (CEIP)
Program Zlepšování softwaru a služeb na základě zkušeností uživatelů (CEIP) je ve výchozím nastavení povolen. Ten pravidelně odesílá zprávy do Microsoftu s cílem přispět k vylepšování SQL Serveru. S programem CEIP není spojená žádná nutná úloha správy – za předpokladu, že ho nechcete po zřízení zakázat. Tento program můžete přizpůsobit nebo vypnout připojením k virtuálnímu počítači pomocí vzdálené plochy. Pak spusťte nástroj **pro zasílání zpráv o chybách a vytváření sestav využití SQL Serveru**. Podle pokynů zakažte generování sestav. Další informace o shromažďování dat najdete v [Prohlášení o zásadách ochrany osobních údajů pro SQL Server](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>Související produkty a služby
### <a name="windows-virtual-machines"></a>Virtuální počítače s Windows
* [Přehled služby Virtual Machines](../overview.md)

### <a name="storage"></a>Úložiště
* [Úvod do Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Síťové služby
* [Přehled služby Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP adresy v Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Vytvoření plně kvalifikovaného názvu domény (FQDN) na webu Azure Portal](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Dokumentace SQL Serveru](https://docs.microsoft.com/sql/index)
* [Porovnání Azure SQL Database](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Další kroky

Začínáme s SQL Serverem na virtuálních počítačích Azure:

* [Vytvoření virtuálního počítače s SQL Serverem na webu Azure Portal](quickstart-sql-vm-create-portal.md)

Odpovědi na nejčastější dotazy o virtuálních počítačích SQL:

* [SQL Server na Azure Virtual Machines – nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md)

Zobrazení referenčních architektur pro spouštění n-vrstvých aplikací na serveru SQL Server v systému IaaS

* [Aplikace Úrovně Windows N v Azure pomocí SQL Serveru](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Spuštění n-vrstvé aplikace ve více oblastech Azure pro vysokou dostupnost](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
