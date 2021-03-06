---
title: Excel & Apache Hadoop s ovladačem ODBC – Azure HDInsight
description: Přečtěte si, jak nastavit a používat ovladač Microsoft Hive ODBC pro Excel k dotazování dat v clusterech HDInsight z Microsoft Excelu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/02/2020
ms.openlocfilehash: f356009197c0446efa2ea2d7f0e90040229df47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251071"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Připojení aplikace Excel k Apache Hadoopu ve službě Azure HDInsight pomocí konektoru Microsoft Hive ODBC Driver

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Řešení Microsoft pro velké objemy dat integruje komponenty Microsoft Business Intelligence (BI) s clustery Apache Hadoop, které byly nasazeny v Azure HDInsight. Příkladem této integrace je možnost připojit aplikaci Excel k datovému skladu Hive clusteru Hadoop v hdinsightu pomocí ovladače Microsoft Hive Open Database Connectivity (ODBC).

Je také možné připojit data spojená s clusterem HDInsight a dalšími zdroji dat, včetně jiných (jiných clusterů Hadoop) (mimo HDInsight), z Excelu pomocí doplňku Microsoft Power Query pro Excel. Informace o instalaci a používání Power Query najdete v [tématu Připojení Excelu k HDInsightu pomocí Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Požadavky

Než začnete tento článek, musíte mít následující položky:

* Cluster HDInsight Hadoop. Pokud chcete ho vytvořit, [přečtěte si, že začínáme s Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Pracovní stanice s Office 2010 Professional Plus nebo novějším nebo Excelem 2010 nebo novějším.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalace ovladače Microsoft Hive ODBC

Stáhněte a nainstalujte verzi [ovladače Microsoft Hive ODBC Driver,](https://www.microsoft.com/download/details.aspx?id=40886) která odpovídá verzi aplikace, kde budete používat ovladač ODBC.  Pro tento článek ovladač se používá pro Aplikaci Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Vytvoření zdroje dat Apache Hive ODBC

Následující kroky ukazují, jak vytvořit zdroj dat OdBC hive.

1. V systému Windows přejděte na úvodní > nástroje pro správu systému Windows > zdroje dat ODBC (32bitový)/(64bitový).  Otevře se okno **Správce zdroje dat ROZHRANÍ ODBC.**

    ![Správce zdroje dat OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Konfigurace dsn pomocí správce zdroje dat ODBC")

1. Na kartě **DSN uživatele** vyberte **Přidat,** chcete-li otevřít okno **Vytvořit nový zdroj dat.**

1. Vyberte **ovladač Microsoft Hive ODBC Driver**a pak klepnutím na příkaz **Dokončit** otevřete okno **Instalace dsn ovladače Microsoft Hive ODBC.**

1. Zadejte nebo vyberte tyto hodnoty:

   | Vlastnost | Popis |
   | --- | --- |
   |  Název zdroje dat |Zadejte název zdroje dat. |
   |  Hostitelé |Zadejte `HDInsightClusterName.azurehdinsight.net`. Například, `myHDICluster.azurehdinsight.net`. Poznámka: `HDInsightClusterName-int.azurehdinsight.net` Je podporována tak dlouho, dokud je virtuální počítač klienta partnerský vztah do stejné virtuální sítě. |
   |  Port |Použijte **443**. (Tento port se změnil z 563 na 443.) |
   |  Databáze |Použít **výchozí**. |
   |  Mechanismus |Vyberte **službu Windows Azure HDInsight** |
   |  Uživatelské jméno |Zadejte uživatelské jméno HTTP clusteru HDInsight. Výchozí uživatelské jméno **admin**. |
   |  Heslo |Zadejte uživatelské heslo clusteru HDInsight. Zaškrtněte políčko **Uložit heslo (Šifrované).**|

1. Volitelné: Vyberte **upřesnit možnosti...**  

   | Parametr | Popis |
   | --- | --- |
   |  Použití nativního dotazu |Pokud je vybrána, ovladač ODBC se nepokouší převést TSQL do HiveQL. Budete ji používat pouze v případě, že jste si 100% jisti, že odesíláte čisté příkazy HiveQL. Při připojování k SQL Server nebo Azure SQL Database, měli byste nechat nezaškrtnuté. |
   |  Řádky načtené na blok |Při načítání velkého počtu záznamů může být vyžadováno ladění tohoto parametru, aby byl zajištěn optimální výkon. |
   |  Výchozí délka sloupce řetězce, délka binárního sloupce, měřítko desetinného sloupce |Délky a přesnosti datového typu mohou ovlivnit způsob vrácení dat. Způsobují vrácení nesprávných informací z důvodu ztráty přesnosti a/nebo zkrácení. |

    ![Rozšířené možnosti konfigurace DSN](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Rozšířené možnosti konfigurace DSN")

1. Chcete-li otestovat zdroj dat, vyberte **možnost Testovat.** Pokud je zdroj dat správně nakonfigurován, výsledek testu ukazuje **ÚSPĚCH!**.  

1. Chcete-li zavřít okno Test, vyberte **ok.**  

1. Výběrem **možnosti OK** zavřete okno **Instalace dsn ovladače Microsoft Hive ODBC.**  

1. Chcete-li zavřít okno **Správce zdroje dat ROZHRANÍ ODBC,** vyberte **ok.**  

## <a name="import-data-into-excel-from-hdinsight"></a>Import dat do Excelu ze služby HDInsight

Následující kroky popisují způsob importu dat z tabulky Hive do sešitu aplikace Excel pomocí zdroje dat ODBC, který jste vytvořili v předchozí části.

1. V Excelu otevřete nový nebo existující sešit.

2. Na kartě **Data** přejděte na **Najdi z** > **jiných zdrojů** > **z ODBC** a spusťte okno Z **ODBC.**

    ![Průvodce otevřením datového připojení aplikace Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Průvodce otevřením datového připojení aplikace Excel")

3. V rozevíracím seznamu vyberte název zdroje dat, který jste vytvořili v poslední části, a pak vyberte **OK**.

4. Při prvním použití se otevře dialogové okno **ovladače ROZHRANÍ ODBC.** V levé nabídce vyberte **Windows.** Pak vyberte **Připojit,** chcete-li otevřít okno **Navigátoru.**

5. Z **navigátoru**přejděte na**výchozí** > **hivesampletable HIVE** **HIVE** > a pak vyberte **Načíst**. Trvá několik okamžiků, než se data importují do aplikace Excel.

    ![HDInsight Excel Hive ODBC navigátor](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "HDInsight Excel Hive ODBC navigátor")

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak pomocí ovladače Microsoft Hive ODBC načíst data ze služby HDInsight do aplikace Excel. Podobně můžete načíst data ze služby HDInsight do databáze SQL. Je také možné nahrát data do služby HDInsight. Další informace naleznete v tématu:

* [Vizualizujte data Apache Hive pomocí Microsoft Power BI v Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizujte data interactive query hive pomocí Power BI v Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Pomocí Apache Zeppelin můžete ve službě Azure HDInsight spouštět dotazy Apache Hive](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojte Excel k Apache Hadoop pomocí Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojte se k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů Data Lake Tools pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Použijte nástroj Azure HDInsight pro kód Visual Studia](../hdinsight-for-vscode.md).
* [Nahrajte data do HDInsight](./../hdinsight-upload-data.md).
