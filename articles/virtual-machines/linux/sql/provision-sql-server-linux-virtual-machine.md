---
title: Vytvoření virtuálního počítače s Linuxem a SQL Serverem 2017 v Azure | Dokumentace Microsoftu
description: Díky tomuto kurzu se dozvíte, jak vytvořit virtuální počítač s Linuxem a SQL Serverem 2017 na webu Azure Portal.
services: virtual-machines-linux
author: MashaMSFT
manager: craigg
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: conceptual
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ba4eed4dcfd6d8e86c21f1ee5214108c44a8c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060238"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Zřízení virtuálního počítače s Linuxem a SQL Serverem na webu Azure Portal

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

V tomto kurzu rychlého startu použijete portál Azure k vytvoření virtuálního počítače s Linuxem s nainstalovaným SQL Serverem 2017.

V tomto kurzu se naučíte:

* [Vytvoření virtuálního počítače s Linuxem a SQL Serverem z galerie](#create)
* [Připojení k novému virtuálnímu počítači přes SSH](#connect)
* [Změna hesla sa](#password)
* [Konfigurace pro vzdálená připojení](#remote)

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free) než začnete.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a>Vytvoření virtuálního počítače s Linuxem a nainstalovaným SQL Serverem

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. V levém podokně vyberte **Vytvořit prostředek**.

1. V **podokně Vytvořit prostředek** vyberte **Vypočítat**.

1. Vyberte **Zobrazit vše** vedle nadpisu **Doporučené.**

   ![Zobrazení všech imagí virtuálních počítačů](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Do vyhledávacího pole zadejte **SQL Server 2019**a výběrem **možnosti Enter** spusťte hledání.

1. Omezte výsledky hledání výběrem **možnosti Operační systém** > **Redhat**.

    ![Vyhledávací filtr pro image virtuálních mích SQL Server 2019](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Z výsledků hledání vyberte bitovou kopii SQL Serveru 2019 linux. Tento kurz používá **SQL Server 2019 na RHEL74**.

   > [!TIP]
   > Edice Developer umožňuje testovat nebo vyvíjet pomocí funkcí edice Enterprise, ale bez licenčních nákladů serveru SQL Server. Platíte jenom náklady na provozování virtuálního počítače s Linuxem.

1. Vyberte **Vytvořit**. 


### <a name="set-up-your-linux-vm"></a>Nastavení virtuálního počítače s Linuxem

1. Na kartě **Základy** vyberte předplatné **a** **skupinu prostředků**. 

    ![Okno Základy](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. Do **pole Název virtuálního počítače**zadejte název nového virtuálního počítače s Linuxem.
1. Poté zadejte nebo vyberte následující hodnoty:
   * **Oblast**: Vyberte oblast Azure, která je pro vás ta pravá.
   * **Možnosti dostupnosti:** Zvolte možnost dostupnosti a redundance, která je nejlepší pro vaše aplikace a data.
   * **Změnit velikost**: Tuto možnost vyberte, chcete-li vybrat velikost stroje, a až bude hotovo, zvolte **Vybrat**. Další informace o velikostech virtuálních počítačů Azure najdete v tématu [Velikosti virtuálních počítačů s Linuxem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

     ![Výběr velikosti virtuálního počítače](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Pro vývoj a funkční testování použijte velikost virtuálního počítače **DS2** nebo vyšší. Pro testování výkonnosti použijte **DS13** nebo vyšší.

   * **Typ ověřování**: Vyberte **veřejný klíč SSH**.

     > [!Note]
     > U ověřování máte na výběr mezi používáním veřejného klíče SSH nebo hesla. SSH je bezpečnější. Pokyny k vygenerování klíče SSH najdete v tématu [Vytvoření klíčů SSH v Linuxu a na Macu pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

   * **Uživatelské jméno**: Zadejte název správce virtuálního soudu.
   * **Veřejný klíč SSH:** Zadejte svůj veřejný klíč RSA.
   * **Veřejné příchozí porty**: Zvolte **Povolit vybrané porty** a vyberte port **SSH (22)** v seznamu **Vybrat veřejné příchozí porty.** V tomto rychlém startu je tento krok nezbytný pro připojení a dokončení konfigurace serveru SQL Server. Pokud se chcete vzdáleně připojit k serveru SQL Server, budete muset ručně povolit přenosy na výchozí port (1433) používaný serverem Microsoft SQL Server pro připojení přes Internet po vytvoření virtuálního počítače.

     ![Příchozí porty](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. Proveďte požadované změny nastavení na následujících dalších kartách nebo zachovejte výchozí nastavení.
    * **Disky**
    * **Síťové služby**
    * **správy**
    * **Konfigurace hosta**
    * **Značky**

1. Vyberte **Zkontrolovat a vytvořit**.
1. V podokně **Revize + vytvoření** vyberte **Vytvořit**.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a>Připojení k virtuálnímu počítači s Linuxem

Pokud už používáte prostředí BASH, připojte se k virtuálnímu počítači Azure pomocí příkazu **ssh**. V následujícím příkazu nahraďte uživatelské jméno a IP adresu virtuálního počítače pro připojení k vašemu virtuálnímu počítači s Linuxem.

```bash
ssh azureadmin@40.55.55.555
```

IP adresu svého virtuálního počítače najdete na webu Azure Portal.

![IP adresa na webu Azure Portal](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Pokud používáte systém Windows a nemáte prostředí BASH, nainstalujte klienta SSH, například PuTTY.

1. [Stáhněte a nainstalujte PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Spusťte PuTTY.

1. Na obrazovce konfigurace PuTTY zadejte veřejnou IP adresu vašeho virtuálního počítače.

1. Vyberte **Otevřít** a na výzvách zadejte své uživatelské jméno a heslo.

Další informace o připojení k virtuálním počítačům s Linuxem najdete v tématu [Vytvoření virtuálního počítače s Linuxem v Azure pomocí portálu](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal).

> [!Note]
> Pokud se zobrazí výstraha zabezpečení PuTTY o tom, že klíč hostitele serveru není uložen do mezipaměti v registru, zvolte z následujících možností. Pokud tomuto hostiteli důvěřujete, vyberte **Ano,** chcete-li přidat klíč do mezipaměti PuTTy a pokračovat v připojování. Pokud chcete pokračovat v připojování jen jednou, bez přidání klíče do mezipaměti, vyberte **možnost Ne**. Pokud tomuto hostiteli nedůvěřujete, vyberte **Zrušit** a připojení upusťte.

## <a name="change-the-sa-password"></a><a id="password"></a>Změna hesla SA

Nový virtuální počítač nainstaluje SQL Server s náhodným heslem SA. Před připojením k serveru SQL Server pomocí přihlášení sa obnovte toto heslo.

1. Po připojení k vašemu virtuálnímu počítači s Linuxem otevřete nový příkazový terminál.

1. Změňte heslo SA pomocí následujících příkazů:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Po zobrazení výzvy zadejte nové heslo SA a potvrzení hesla.

1. Restartujte službu SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Přidání nástrojů do cesty (volitelné)

Ve výchozím nastavení se nainstaluje několik [balíčků](sql-server-linux-virtual-machines-overview.md#packages) SQL Serveru, včetně balíčku nástrojů příkazového řádku pro SQL Server. Balíček nástrojů obsahuje nástroje **sqlcmd** a **bcp**. Pro usnadnění práce můžete volitelně přidat cestu k nástrojům `/opt/mssql-tools/bin/` do proměnné prostředí **PATH**.

1. Spuštěním následujících příkazů upravte **PATH** pro relace přihlášení i interaktivní relace a relace bez přihlášení:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a>Konfigurace pro vzdálená připojení

Pokud se potřebujete vzdáleně připojit k SQL Serveru na virtuálním počítači Azure, musíte ve skupině zabezpečení sítě nakonfigurovat pravidlo pro příchozí provoz. Toto pravidlo povoluje provoz na portu, na kterém SQL Server naslouchá (výchozí je 1433). Následující kroky ukazují, jak tento krok provést pomocí webu Azure Portal.

> [!TIP]
> Pokud jste během zřizování v nastavení vybrali příchozí port **MS SQL (1433)**, tyto změny už se pro vás provedly. Můžete přejít k další části věnované konfiguraci brány firewall.

1. Na portálu vyberte **Virtuální počítače** a pak vyberte váš virtuální počítač s SQL Serverem.
1. V levém navigačním podokně vyberte v části **Nastavení** **položku Networking**.
1. V okně Síť vyberte **přidat příchozí port** v části Pravidla **příchozího portu**.

   ![Pravidla portů pro příchozí provoz](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. V seznamu **Služba** vyberte **MS SQL**.

    ![Pravidlo skupiny zabezpečení MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Kliknutím na **OK** uložte pravidlo pro virtuální počítač.

### <a name="open-the-firewall-on-rhel"></a>Otevření brány firewall v RHEL

Tento kurz vám ukázal vytvoření virtuálního počítače s Red Hat Enterprise Linuxem (RHEL). Pokud se chcete vzdáleně připojit k virtuálním počítačům s RHEL, je potřeba otevřít port 1433 i v bráně firewall v Linuxu.

1. [Připojte se](#connect) ke svému virtuálnímu počítači s RHEL.

1. V prostředí BASH spusťte následující příkazy:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Další kroky

Když teď máte v Azure virtuální počítač s SQL Serverem 2017, můžete se k němu místně připojit pomocí nástroje **sqlcmd** a spouštět dotazy Transact-SQL.

Pokud jste nakonfigurovali virtuální počítač Azure pro vzdálená připojení SQL Serveru, měli byste být schopni se vzdáleně připojit. Příklad vzdáleného připojení k SQL Serveru v Linuxu z Windows najdete v tématu [Použití SSMS ve Windows k připojení k SQL Serveru v Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Informace o připojení pomocí Visual Studio Code najdete v tématu [Použití nástroje Visual Studio Code k vytváření a spouštění skriptů Transact-SQL pro SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode).

Obecnější informace o SQL Serveru v Linuxu najdete v [tématu Přehled SQL Serveru 2017 na Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Další informace o používání virtuálních počítačů s Linuxem a SQL Serverem 2017 najdete v tématu [Přehled virtuálních počítačů s SQL Serverem 2017 v Azure](sql-server-linux-virtual-machines-overview.md).
