---
title: Migrace dat do účtu SQL API Azure Cosmos DB pomocí striimu
description: Zjistěte, jak pomocí Striimu migrovat data z databáze Oracle do účtu SQL API Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 36ba9e2d3385184f32876a6d067b58f7c21a90bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71003274"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Migrace dat do účtu SQL API Azure Cosmos DB pomocí striimu
 
Image Striim na Azure Marketplace nabízí nepřetržitý přesun dat v reálném čase z datových skladů a databází do Azure. Při přesouvání dat můžete provádět in-line denormalizaci, transformaci dat, povolit analýzy v reálném čase a scénáře vytváření sestav dat. Se Striimem můžete snadno začít přemisťovat podniková data do rozhraní Azure Cosmos DB SQL API. Azure poskytuje nabídku marketplace, která usnadňuje nasazení Striimu a migrace dat do Azure Cosmos DB. 

Tento článek ukazuje, jak pomocí striimu migrovat data z **databáze Oracle** do účtu SQL API **Azure Cosmos DB**.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte [předplatné Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

* Databáze Oracle spuštěná místně s některými daty v ní.

## <a name="deploy-the-striim-marketplace-solution"></a>Nasazení řešení striim marketplace

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Vyberte **Vytvořit prostředek** a vyhledejte **Striim** na webu Azure Marketplace. Vyberte první možnost a **Vytvořit**.

   ![Najít položku tržiště Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Dále zadejte vlastnosti konfigurace instance Striim. Prostředí Striim se nasazuje ve virtuálním počítači. V podokně **Základy** zadejte **uživatelské jméno virtuálního počítače**, heslo **virtuálního počítače** (toto heslo se používá pro SSH do virtuálního počítače). Vyberte **podrobnosti o předplatném**, **skupině prostředků**a **umístění,** kde chcete nasadit striim. Po dokončení vyberte **OK**.

   ![Konfigurace základních nastavení pro striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)

1. V podokně **nastavení striimového clusteru** zvolte typ nasazení Striim a velikost virtuálního počítače.

   |Nastavení | Hodnota | Popis |
   | ---| ---| ---|
   |Typ nasazení Striim |Standalone | Striim lze spustit v **samostatné** nebo **clusterové** typy nasazení. Samostatný režim nasadí server Striim na jednom virtuálním počítači a můžete vybrat velikost virtuálních počítačů v závislosti na objemu dat. Režim clusteru nasadí server Striim na dva nebo více virtuálních počítačích s vybranou velikostí. Clusterová prostředí s více než 2 uzly nabízejí automatickou vysokou dostupnost a převzetí služeb při selhání.</br></br> V tomto kurzu můžete vybrat samostatnou možnost. Použijte výchozí virtuální počítače o velikosti "Standard_F4s".  | 
   | Název clusteru Striim|    <Striim_cluster_Name>|  Název clusteru Striim.|
   | Heslo striimového clusteru|   Striim_cluster_password <>|  Heslo pro cluster.|

   Po vyplnění formuláře vyberte **OK,** chcete-li pokračovat.

1. V podokně **Nastavení přístupu striim** nakonfigurujte **veřejnou IP adresu** (zvolte výchozí hodnoty), název domény pro **Striim**, **heslo správce,** které chcete použít k přihlášení k uživatelskému rozhraní Striim. Konfigurace virtuální sítě a podsítě (zvolte výchozí hodnoty). Po vyplnění podrobností pokračujte výběrem **možnosti OK.**

   ![Nastavení přístupu striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure ověří nasazení a ujistěte se, že všechno vypadá dobře; ověření trvá několik minut. Po dokončení ověření vyberte **ok**.
  
1. Nakonec zkontrolujte podmínky použití a vyberte **Vytvořit,** chcete-li vytvořit svou instanci Striim. 

## <a name="configure-the-source-database"></a>Konfigurace zdrojové databáze 

V této části nakonfigurujete databázi Oracle jako zdroj pro přesun dat.  Pro připojení k řešení Oracle budete potřebovat [ovladač Oracle JDBC.](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) Chcete-li číst změny ze zdrojové databáze Oracle, můžete použít [logminer](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) nebo [xstream api](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). Ovladač Oracle JDBC musí být k dispozici ve striimově cestě třídy Java, aby bylo možné číst, zapisovat nebo uchovávat data z databáze Oracle.

Stáhněte si ovladač [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) do místního počítače. Do clusteru Striim jej nainstalujete později.

## <a name="configure-the-target-database"></a>Konfigurace cílové databáze

V této části nakonfigurujete účet AZURE Cosmos DB SQL API jako cíl pro přesun dat.

1. Vytvořte [účet Azure Cosmos DB SQL API](create-cosmosdb-resources-portal.md) pomocí portálu Azure.

1. Přejděte do podokna **Průzkumník dat** ve svém účtu Azure Cosmos. Chcete-li vytvořit nový kontejner, vyberte **nový kontejner.** Předpokládejme, že migrujete *produkty* a *objednáváte* data z databáze Oracle do Azure Cosmos DB. Vytvořte novou databázi s názvem **StriimDemo** s kontejnerem s názvem **Objednávky**. Zřízení kontejneru s **1000 RU** (tento příklad používá 1000 RU, ale měli byste použít propustnost odhadovanou pro vaše úlohy) a **/ORDER_ID** jako klíč oddílu. Tyto hodnoty se budou lišit v závislosti na zdrojových datech. 

   ![Vytvoření účtu rozhraní SQL API](./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Konfigurace toku dat Oracle to Azure Cosmos DB

1. Teď se vraťme ke Striimu. Před interakcí se společností Striim nainstalujte ovladač Oracle JDBC, který jste stáhli dříve.

1. Přejděte na striimovou instanci, kterou jste nasadili na webu Azure Portal. V horním řádku nabídek vyberte tlačítko **Připojit** a na kartě **SSH** zkopírujte adresu URL v poli Přihlášení pomocí pole **místního účtu virtuálního účtu.**

   ![Získat adresu URL SSH](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Otevřete nové okno terminálu a spusťte příkaz SSH, který jste zkopírovali z portálu Azure. Tento článek používá terminál v Systému MacOS, můžete postupovat podle podobných pokynů pomocí PuTTY nebo jiného klienta SSH na počítači se systémem Windows. Po zobrazení výzvy zadejte **ano,** chcete-li pokračovat a zadat **heslo,** které jste nastavili pro virtuální počítač v předchozím kroku.

   ![Připojení k virtuálnímu virtuálnímu virtuálnímu zařízení Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Nyní otevřete novou kartu terminálu a zkopírujte soubor **ojdbc8.jar,** který jste stáhli dříve. Pomocí následujícího příkazu SCP zkopírujte soubor jar z místního počítače do složky TMP instance Striim spuštěné v Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Zkopírování souboru Jar z skladového zařízení do zařízení Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Dále přejděte zpět do okna, kde jste provedli SSH do instance Striim a Přihlaste se jako sudo. Přesuňte soubor **ojdbc8.jar** z adresáře **/tmp** do adresáře **lib** instance Striim pomocí následujících příkazů:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Přesunutí souboru Jar do složky lib](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Ze stejného okna terminálu restartujte server Striim provedením následujících příkazů:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim bude chvíli trvat, než začne. Pokud chcete zobrazit stav, spusťte následující příkaz: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Teď přejděte zpět do Azure a zkopírujte veřejnou IP adresu vašeho virtuálního počítače Striim. 

   ![Kopírovat adresu IP virtuálního virtuálního počítačů Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Chcete-li přejít na webové uživatelské prostředí striimu, otevřete v prohlížeči novou kartu a zkopírujte veřejnou IP adresu následovanou: 9080. Přihlaste se pomocí uživatelského jména **správce** spolu s heslem správce, které jste zadali na webu Azure Portal.

   ![Přihlášení do Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Teď dorazíš na Striimovu domovskou stránku. Existují tři různá **podokna**– řídicí panely , **aplikace**a **sourcepreview**. Podokno Řídicí panely umožňuje přesouvat data v reálném čase a vizualizovat je. Podokno Aplikace obsahuje vaše datové kanály streamování nebo toky dat. Na pravé straně stránky je SourcePreview, kde si můžete zobrazit náhled dat před přesunutím.

1. Vyberte podokno **Aplikace,** prozatím se zaměříme na toto podokno. Existuje celá řada ukázkových aplikací, které můžete použít k informacím o Striim, ale v tomto článku vytvoříte vlastní. V pravém horním rohu vyberte tlačítko **Přidat aplikaci.**

   ![Přidání aplikace Striim](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Existuje několik různých způsobů, jak vytvořit aplikace Striim. Vyberte **Začít se šablonou,** chcete-li začít s existující šablonou.

   ![Spuštění aplikace se šablonou](./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png)

1. Do pole **Šablony hledání** zadejte "Cosmos" a vyberte **Target: Azure Cosmos DB** a pak vyberte Oracle CDC do Azure **Cosmos DB**.

   ![Vyberte oracle cdc do cosmos DB](./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png)

1. Na další stránce pojmenujte aplikaci. Můžete zadat název, například **oraToCosmosDB** a pak vyberte **Uložit**.

1. Dále zadejte zdrojovou konfiguraci zdrojové instance Oracle. Zadejte hodnotu **pro název zdroje**. Název zdroje je pouze konvence pojmenování pro aplikaci Striim, můžete použít něco jako **src_onPremOracle**. Zadejte hodnoty pro zbytek url **zdrojových**parametrů , **Uživatelské jméno**, **Heslo**, zvolte **LogMiner** jako čtečku pro čtení dat z Oracle. Pokračujte výběrem tlačítka **Next** (Další).

   ![Konfigurace zdrojových parametrů](./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png)

1. Striim zkontroluje vaše prostředí a ujistěte se, že se může připojit ke zdrojové instanci Oracle, má správná oprávnění a že cdc bylo správně nakonfigurováno. Po ověření všech hodnot vyberte **možnost Další**.

   ![Ověřit zdrojové parametry](./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png)

1. Vyberte tabulky z databáze Oracle, které chcete migrovat. Například vyberte tabulku Objednávky a vyberte **Další**. 

   ![Výběr zdrojových tabulek](./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png)

1. Po výběru zdrojové tabulky můžete provést složitější operace, jako je mapování a filtrování. V takovém případě vytvoříte repliku zdrojové tabulky v Azure Cosmos DB. Takže, vyberte **Další** pro konfiguraci cíle

1. Nyní nakonfigurujme cíl:

   * **Název cíle** – zadejte popisný název cíle. 
   * **Vstup z** - Z rozevíracího seznamu vyberte vstupní datový proud z toho, který jste vytvořili ve zdrojové konfiguraci Oracle. 
   * **Kolekce**– zadejte vlastnosti konfigurace cílové Azure Cosmos DB. Syntaxe kolekce je **SourceSchema.SourceTable, TargetDatabase.TargetContainer**. V tomto příkladu by hodnota byla "SYSTEM. OBJEDNÁVKY, StriimDemo.Orders". 
   * **AccessKey** – primární klíč vašeho účtu Azure Cosmos.
   * **ServiceEndpoint** – identifikátor URI vašeho účtu Azure Cosmos, které najdete v části **klíče** na portálu Azure. 

   Vyberte **Uložit** a **Další**.

   ![Konfigurace cílových parametrů](./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png)


1. Dále se dostanete do návrháře toku, kde můžete přetáhnout a vynechat konektory boxu a vytvořit aplikace streamování. V tomto okamžiku nebudete provádět žádné změny toku. tak pokračujte a nasaďte aplikaci výběrem tlačítka **Nasadit aplikaci.**
 
   ![Nasazení aplikace](./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png)

1. V okně nasazení můžete určit, zda chcete spustit určité části aplikace v určitých částech topologie nasazení. Vzhledem k tomu, že běžíme v jednoduché topologii nasazení prostřednictvím Azure, použijeme výchozí možnost.

   ![Použít výchozí možnost](./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png)

1. Po nasazení můžete zobrazit náhled datového proudu a zobrazit data protékající. Vyberte ikonu **vlny** a oční bulvu vedle ní. V horním řádku nabídek vyberte tlačítko **Nasazené** a vyberte **Spustit aplikaci**.

   ![Spuštění aplikace](./media/cosmosdb-sql-api-migrate-data-striim/start-app.png)

1. Pomocí **čtečky CDC (Změna sběru dat)** Striim bude vyzvednout pouze nové změny v databázi. Pokud máte data protékající zdrojovými tabulkami, uvidíte je. Vzhledem k tomu, že se jedná o ukázkovou tabulku, zdroj není připojen k žádné aplikaci. Pokud používáte ukázkový generátor dat, můžete do databáze Oracle vložit řetězec událostí.

1. Uvidíte data protékající platformou Striim. Striim také snímá všechna metadata spojená s vaší tabulkou, což je užitečné pro sledování dat a ujistěte se, že data přistane na správný cíl.

   ![Konfigurace kanálu CDC](./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png)

1. Nakonec se přihlásíme do Azure a přejdeme na váš účet Azure Cosmos. Aktualizujte Průzkumníka dat a uvidíte, že data dorazila.  

   ![Ověření migrovaných dat v Azure](./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png)

Pomocí řešení Striim v Azure můžete průběžně migrovat data do Azure Cosmos DB z různých zdrojů, jako je Oracle, Cassandra, MongoDB a různé další do Azure Cosmos DB. Chcete-li se dozvědět více, navštivte [webové stránky Striim](https://www.striim.com/), [stáhněte si bezplatnou 30denní zkušební verzi striimu](https://go2.striim.com/download-free-trial)a pro jakékoli problémy při nastavování cesty migrace se Striimem podejte [žádost o podporu.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Další kroky

* Pokud migrujete data do rozhraní AZURE Cosmos DB SQL API, přečtěte [si, jak migrovat data do účtu CASSandro API pomocí Striim](cosmosdb-cassandra-api-migrate-data-striim.md)

* [Sledování a ladění dat pomocí metrik Azure Cosmos DB](use-metrics.md)
