---
title: Co je Apache Spark - Azure HDInsight
description: Tento článek představuje Spark ve službě HDInsight a různé scénáře, ve kterých můžete použít cluster Spark ve službě HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 02/25/2020
ms.openlocfilehash: 83bfeb85d110f7a84720e943e28f5e014e4c3888
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605046"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Co je Apache Spark ve službě Azure HDInsight

Apache Spark je paralelní architektura zpracování, která podporuje zpracování v paměti pro zvýšení výkonu analytických aplikací pro velká data. Apache Spark ve službě Azure HDInsight je implementace Apache Sparku v cloudu od Microsoftu. HDInsight usnadňuje vytvoření a konfiguraci clusteru Spark v Azure. Clustery Spark ve HDInsightu jsou kompatibilní s Azure Storage a Azure Data Lake Storage. Clustery HDInsight Spark proto můžete použít ke zpracování dat uložených v Azure. Součásti a informace o správu verzí najdete v tématu [součásti apache hadoop a verze v Azure HDInsight](../hdinsight-component-versioning.md).

![Spark: jednotné rozhraní](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Co je Apache Spark?

Spark poskytuje primitivy pro clusterové výpočty v paměti. Úloha Spark může načítat data, ukládat je do paměti a opakovaně je dotazovat. V paměti je výpočetní technika mnohem rychlejší než aplikace založené na disku, jako je Například Hadoop, který sdílí data prostřednictvím distribuovaného souborového systému Hadoop (HDFS). Spark se také integruje do programovacího jazyka Scala a díky tomu umožňuje pracovat s distribuovanými datovými sadami stejně jako s místními kolekcemi. Není nutné strukturovat všechno jako mapovací a redukční operace.

![Tradiční MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark1.png)

Clustery Spark ve službě HDInsight nabízejí plně spravovanou službu Spark. Tady najdete výhody, které přináší vytvoření clusteru Spark ve službě HDInsight.

| Funkce | Popis |
| --- | --- |
| Snadné vytvoření |Nový cluster Spark ve službě HDInsight můžete vytvořit během několika minut pomocí webu Azure Portal, Azure PowerShellu nebo sady HDInsight .NET SDK. Viz [Začínáme s clusterem Apache Spark ve službě HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Snadné používání |Spark cluster v HDInsight patří Jupyter a Apache Zeppelin notebooky. Tyto poznámkové bloky můžete použít pro interaktivní zpracování dat a vizualizaci. Viz [Použití notebooků Apache Zeppelin s Apache Spark](apache-spark-zeppelin-notebook.md) a [Načtením dat a spouštění dotazů v clusteru Apache Spark](apache-spark-load-data-run-query.md).|
| Rozhraní REST API |Mezi clustery Spark v HDInsightu patří [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), server úloh Spark založený na rozhraní REST API, který vzdáleně odesílá a monitoruje úlohy. Viz [Použití rozhraní APACHE Spark REST API k odeslání vzdálených úloh do clusteru HDInsight Spark](apache-spark-livy-rest-interface.md).|
| Podpora úložiště datových jezer Azure | Clustery Spark ve HDInsightu můžou používat Azure Data Lake Storage jako primární úložiště nebo další úložiště. Další informace o úložišti datových jezer najdete [v tématu Přehled úložiště datových jezer Azure](../../data-lake-store/data-lake-store-overview.md). |
| Integrace se službami Azure |Cluster Spark ve službě HDInsight se dodává s konektorem k Azure Event Hubs. Kromě [Apache Kafka](https://kafka.apache.org/), který je už k dispozici jako součást Sparku, můžete vytvářet streamovací aplikace pomocí event hubů. |
| Podpora pro ML Server | Podpora pro ML Server v HDInsightu je k dispozici jako typ clusteru **ML Services**. Cluster ML Services můžete nastavit pro spouštění distribuovaných výpočtů v R s rychlostí, kterou zajišťuje cluster Spark. Další informace najdete v tématu [Co je ml služby v Azure HDInsight](../r-server/r-server-overview.md). |
| Integrace v prostředí IDE třetích stran | HDInsight poskytuje několik modulů plug-in pro integrovaná vývojová prostředí (IDE), které jsou užitečné k vytváření a odesílání aplikací do clusteru HDInsight Spark. Další informace najdete [v tématech Použití sady nástrojů Azure pro IntelliJ IDEA](apache-spark-intellij-tool-plugin.md), [Použití nástrojů Spark & Hive pro VSCode](../hdinsight-for-vscode.md)a [Použití sady nástrojů Azure Toolkit pro eclipse](apache-spark-eclipse-tool-plugin.md).|
| Počet souběžných dotazů |Clustery Spark v HDInsight podporují souběžné dotazy. Tato schopnost umožňuje sdílení stejných prostředků clusteru pro více dotazů od jednoho uživatele nebo více dotazů od různých uživatelů a aplikací. |
| Ukládání do mezipaměti na SSD |Data do mezipaměti můžete ukládat volitelně buď do paměti nebo na SSD disky připojené k uzlům clusteru. Ukládání do mezipaměti zajišťuje nejlepší výkon dotazů, ale může být nákladné. Ukládání na disky SSD poskytuje skvělou možnost pro zlepšení výkonu dotazů, aniž by bylo nutné vytvořit cluster velikosti, která je potřeba pro umístění celé datové sady do paměti. Viz [Zlepšení výkonu úloh Apache Spark pomocí Azure HDInsight IO Cache](apache-spark-improve-performance-iocache.md). |
| Integrace s nástroji BI |Clustery Spark ve službě HDInsight nabízí konektory pro nástroje BI, například [Power BI](https://www.powerbi.com/) pro analýzu dat. |
| Předem zavedené knihovny Anaconda |Clustery Spark ve službě HDInsight obsahují předinstalované knihovny Anaconda. [Anaconda](https://docs.continuum.io/anaconda/) poskytuje téměř 200 knihoven pro strojové učení, analýzu dat, vizualizaci a tak dále. |
| Škálovatelnost | HDInsight umožňuje dynamicky měnit počet uzlů clusteru pomocí funkce Automatické škálování. Viz [Automatické škálování clusterů Azure HDInsight](../hdinsight-autoscale-clusters.md). Clustery Spark lze také vynechat bez ztráty dat, protože všechna data jsou uložená v Azure Storage nebo Data Lake Storage. |
| SLA |Clustery Spark ve službě HDInsight přináší nepřetržitou podporu a smlouvu SLA zajišťující 99,9% dostupnost. |

Clustery Apache Spark ve službě HDInsight zahrnují následující součásti, které jsou ve výchozím nastavení v clusterech k dispozici.

* [Spark Core](https://spark.apache.org/docs/latest/). Obsahuje Spark Core, Spark SQL, rozhraní API pro vysílání datového proudu Spark, GraphX a MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apačská Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Poznámkový blok Jupyter](https://jupyter.org)
* [Notebook Apache Zeppelin](http://zeppelin-project.org/)

Clustery Spark ve službě HDInsight také poskytují [ovladač ODBC](https://go.microsoft.com/fwlink/?LinkId=616229) umožňující připojení ke clusterům Spark ve službě HDInsight z nástrojů BI, například Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Architektura clusteru Spark

![Architektura HDInsight Sparku](./media/apache-spark-overview/hdi-spark-architecture.png)

Je snadné pochopit součásti Spark tím, že porozumíme tomu, jak Spark běží na clusterech HDInsight.

Aplikace Spark se v clusteru spouští jako nezávislé sady procesů, které koordinuje objekt SparkContext v hlavním programu (označuje se jako řídicí program).

SparkContext se může připojit k několika typům správců clusterů, kteří přidělují prostředky napříč aplikacemi. Mezi tyto správce clusteru patří [Apache Mesos](https://mesos.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)nebo správce clusteru Spark. V HDInsight se Spark spouští pomocí správce clusteru YARN. Po připojení Spark získá exekutory na pracovních uzlech v clusteru, což jsou procesy, které provádí výpočty a ukládají data pro vaši aplikaci. Pak do exekutorů odešle kód vaší aplikace (definovaný v souborech JAR nebo Pythonu předaných do objektu SparkContext). Nakonec SparkContext odešle do exekutorů úlohy ke spuštění.

SparkContext spouští hlavní funkci uživatele a provádí různé paralelní operace na pracovních uzlech. Potom SparkContext shromažďuje výsledky těchto operací. Pracovní uzly číst a zapisovat data z a do hadoopdistribuovaného systému souborů. Pracovní uzly také ukládají transformovaná data do paměti jako sady RDD (Resilient Distributed Dataset).

SparkContext se připojuje k hlavnímu uzlu Sparku a je zodpovědný za převod aplikace do orientovaného acyklického grafu (DAG) jednotlivých úloh, které se provádějí v rámci procesu exekutora na pracovních uzlech. Každá aplikace získá vlastní procesy exekutora, které zůstávají v provozu po dobu trvání celé aplikace a spouští úlohy ve více vláknech.

## <a name="spark-in-hdinsight-use-cases"></a>Případy použití Sparku ve službě HDInsight

Clustery Spark v HDInsight podporují následující klíčové scénáře:

### <a name="interactive-data-analysis-and-bi"></a>Interaktivní analýzu dat a BI

Apache Spark ve HDInsightu ukládá data ve Službě Azure Storage nebo Azure Data Lake Storage. Obchodní specialisté a osoby provádějící klíčová rozhodnutí analyzují a vytváří z těchto dat sestavy a používají Microsoft Power BI pro vytváření interaktivních sestav z analyzovaných dat. Analytici mohou začínat z nestrukturovaných / částečně strukturovaných dat v úložišti clusteru, definovat schéma pro data s využitím poznámkových bloků a následně vytvořit modely dat pomocí Microsoft Power BI. Clustery Spark ve službě HDInsight podporují také různé nástroje BI třetích stran, například Tableau, a usnadňují tak práci datovým analytikům, obchodním specialistům a osobám provádějícím klíčová rozhodnutí.

* [Kurz: Vizualizace dat Sparku pomocí Power BI](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Spark Machine Learning

Systém Apache Spark je vybavený knihovnou [MLlib](https://spark.apache.org/mllib/) pro strojové učení, jejímž základem je Spark a kterou můžete používat z clusteru Spark v HDInsight. Spark cluster v HDInsight také zahrnuje Anaconda, distribuce Pythonu s různými druhy balíčků pro strojové učení. Spojte tyto možnosti s integrovanou podporou pro poznámkové bloky Jupyter a Zeppelin a máte prostředí pro tvorbu aplikací strojového učení.

* [Kurz: Předvídání teplot budov pomocí dat vzduchotechniky](apache-spark-ipython-notebook-machine-learning.md)  
* [Kurz: Předvídejte výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Vysílání datových proudů a analýza dat v reálném čase ve Sparku

Clustery Spark v HDInsight nabízí bohatou podporu pro vytváření řešení pro analýzu v reálném čase. Zatímco Spark již obsahuje konektory pro načítání dat z mnoha zdrojů, například soketů Kafka, Flume, Twitter, ZeroMQ nebo TCP, Spark v HDInsight přidává prvotřídní podporu pro příjem dat z Azure Event Hubs. Event Hubs je nejpoužívanější službou řazení front v Azure. Díky integrované podpoře služby Event Hubs představují clustery Spark ve službě HDInsight ideální platformu pro vytvoření kanálu k analýze dat v reálném čase.

* [Přehled streamování Apache Spark](apache-spark-streaming-overview.md)
* [Přehled strukturovaného streamování Apache Spark](apache-spark-structured-streaming-overview.md)

## <a name="where-do-i-start"></a>Kde mám začít?

Další informace o Apache Spark ve službě HDInsight najdete v následujících článcích:

* [Úvodní příručka: Vytvoření clusteru Apache Spark v HDInsightu a spuštění interaktivního dotazu pomocí Jupyteru](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Kurz: Spuštění úlohy Apache Spark pomocí Jupyteru](./apache-spark-load-data-run-query.md)
* [Kurz: Analýza dat pomocí nástrojů BI](./apache-spark-use-bi-tools.md)
* [Výuka: Strojové učení pomocí Apache Spark](./apache-spark-ipython-notebook-machine-learning.md)
* [Kurz: Vytvoření aplikace Scala Maven pomocí Aplikace IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Další kroky

V tomto článku jste získali základní přehled Apache Sparku ve službě Azure HDInsight. V dalším článku se dozvíte, jak vytvořit cluster HDInsight Spark a spustit několik dotazů Spark SQL:

* [Vytvoření clusteru Apache Spark v HDInsightu](./apache-spark-jupyter-spark-sql-use-portal.md)
