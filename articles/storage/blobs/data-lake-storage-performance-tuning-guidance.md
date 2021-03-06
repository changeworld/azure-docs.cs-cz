---
title: Optimalizace Azure Data Lake Storage Gen2 pro výkon | Dokumenty společnosti Microsoft
description: Pokyny pro optimalizaci výkonu úložiště datového jezera Azure
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f1a16228b72d7e0f45048669ade94a0c78d9ac52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327948"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>Optimalizace Azure Data Lake Storage Gen2 pro výkon

Azure Data Lake Storage Gen2 podporuje vysokou propustnost pro analýzy náročné na vstupně-up a přenos dat.  V data lake storage Gen2, pomocí všech dostupných propustnost – množství dat, které lze číst nebo zapisovat za sekundu – je důležité získat nejlepší výkon.  Toho je dosaženo provedením co nejvíce čtení a zápisy paralelně, jak je to možné.

![Výkon data lake storage gen2](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 můžete škálovat tak, aby poskytovalpotřebnou propustnost pro všechny scénáře analýzy. Ve výchozím nastavení poskytuje účet Data Lake Storage Gen2 automaticky dostatečnou propustnost, aby vyhovovala potřebám široké kategorie případů použití. V případech, kdy zákazníci spustit do výchozí limit, data lake storage gen2 účet lze nakonfigurovat tak, aby poskytovaly větší propustnost kontaktováním [podpory Azure](https://azure.microsoft.com/support/faq/).

## <a name="data-ingestion"></a>Přijímání dat

Při ingestování dat ze zdrojového systému do data Lake Storage Gen2, je důležité vzít v úvahu, že zdrojový hardware, zdrojový síťový hardware a připojení k síti k úložišti datového jezera Gen2 může být kritickým bodem.  

![Výkon data lake storage gen2](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Je důležité zajistit, aby pohyb dat nebyl těmito faktory ovlivněn.

### <a name="source-hardware"></a>Zdrojový hardware

Ať už používáte místní počítače nebo virtuální počítače v Azure, měli byste pečlivě vybrat příslušný hardware. U zdrojového hardwaru disku upřednostňujte disky SSD před pevnými disky a vyberte diskový hardware s rychlejšími vřeteny. U zdrojového síťového hardwaru použijte nejrychlejší možné síťové karty.  V Azure doporučujeme virtuální počítače Azure D14, které mají vhodně výkonný disk ový a síťový hardware.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Síťové připojení k úložišti Data Lake Storage Gen2

Síťové připojení mezi zdrojovými daty a datovým jezerem Gen2 může být někdy kritickým bodem. Pokud jsou vaše zdrojová data místní, zvažte použití vyhrazeného propojení s [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Pokud jsou vaše zdrojová data v Azure, výkon bude nejlepší, když jsou data ve stejné oblasti Azure jako účet Data Lake Storage Gen2.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurace nástrojů pro ingestování dat pro maximální paralelizaci

Jakmile vyřešíte výše uvedená problémová místa zdrojového hardwaru a připojení k síti, jste připraveni nakonfigurovat nástroje pro ingestování. Následující tabulka shrnuje klíčová nastavení pro několik oblíbených nástrojů pro ingestování a poskytuje podrobné články o ladění výkonu.  Další informace o tom, který nástroj použít pro váš scénář, naleznete v tomto [článku](data-lake-storage-data-scenarios.md).

| Nástroj               | Nastavení     | Další podrobnosti                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (mapovač)   | [Odkaz](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Odkaz](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper)    |   [Odkaz](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Strukturujte sadu dat

Pokud jsou data uložena v gen2 úložiště datového jezera, má velikost souboru, počet souborů a struktura složek vliv na výkon.  Následující část popisuje osvědčené postupy v těchto oblastech.  

### <a name="file-size"></a>Velikost souboru

Analytické motory, jako je HDInsight a Azure Data Lake Analytics, mají obvykle režii na jeden soubor. Pokud ukládáte data jako mnoho malých souborů, může to negativně ovlivnit výkon. Obecně platí, že uspořádejte data do větších souborů pro lepší výkon (velikost 256 MB až 100 GB). Některé moduly a aplikace mohou mít potíže s efektivním zpracováním souborů o velikosti větší než 100 GB.

Někdy mají datové kanály omezenou kontrolu nad nezpracovanými daty, která mají velké množství malých souborů. Doporučuje se mít proces "vaření", který generuje větší soubory pro následné aplikace.

### <a name="organizing-time-series-data-in-folders"></a>Uspořádání dat časových řad do složek

Pro úlohy Hive může vyřazování dat časových řad oddílů pomoci některým dotazům číst pouze podmnožinu dat, která zlepšuje výkon.    

Tyto kanály, které ingestují data časových řad, často umístí své soubory s velmi strukturovaným pojmenováním souborů a složek. Níže je velmi častý příklad vidíme pro data, která je strukturována podle data:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Všimněte si, že informace datetime se zobrazí jako složky i v názvu souboru.

Pro datum a čas je běžným vzorem

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Opět platí, že volba, kterou provedete s organizací složek a souborů, by měla optimalizovat pro větší velikosti souborů a přiměřený počet souborů v každé složce.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimalizace náročných úloh vstupně-no videa na zatížení hadoopu a sparku na HDInsightu

Pracovní místa spadají do jedné z následujících tří kategorií:

* **Cpu náročné.**  Tyto úlohy mají dlouhé výpočetní časy s minimálními časy vstupně-o.  Mezi příklady patří strojové učení a úlohy zpracování přirozeného jazyka.  
* **Náročná na paměť.**  Tyto úlohy používají velké množství paměti.  Příklady zahrnují pagerank a analýzy v reálném čase.  
* **I/O intenzivní.**  Tato pracovní místa tráví většinu svého času dělá I / O.  Běžným příkladem je úloha kopírování, která provádí pouze operace čtení a zápisu.  Mezi další příklady patří úlohy přípravy dat, které čtou velké množství dat, provádí některé transformace dat a pak zapíše data zpět do úložiště.  

Následující pokyny se vztahují pouze na úlohy náročné vstupně-in.

## <a name="general-considerations"></a>Obecné aspekty

Můžete mít úlohu, která čte nebo zapisuje až 100 MB v jedné operaci, ale vyrovnávací paměť této velikosti může ohrozit výkon.
Chcete-li optimalizovat výkon, pokuste se zachovat velikost vstupně-va operace mezi 4 MB a 16 MB.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Obecné aspekty clusteru HDInsight

* **HDInsight verze.** Nejlepší výkon můžete dosáhnout v nejnovější verzi služby HDInsight.
* **Regiony.** Umístěte účet Gen2 úložiště datového jezera do stejné oblasti jako cluster HDInsight.  

Cluster HDInsight se skládá ze dvou hlavních uzlů a některých pracovních uzlů. Každý pracovní uzel poskytuje určitý počet jader a paměti, který je určen typem virtuálního zařízení.  Při spuštění úlohy YARN je vyjednavač prostředků, který přiděluje dostupné paměti a jader k vytvoření kontejnerů.  Každý kontejner spustí úkoly potřebné k dokončení úlohy.  Kontejnery běží paralelně ke zpracování úloh rychle. Proto výkon je lepší spuštěním co nejvíce paralelní kontejnery, jak je to možné.

V rámci clusteru HDInsight jsou tři vrstvy, které lze vyladit tak, aby se zvýšil počet kontejnerů a využily veškerou dostupnou propustnost.  

* **Fyzická vrstva**
* **Vrstva Příze**
* **Vrstva pracovního vytížení**

### <a name="physical-layer"></a>Fyzická vrstva

**Spusťte cluster s více uzly nebo větší velikosti virtuálních měn.**  Větší cluster vám umožní spustit více kontejnerů YARN, jak je znázorněno na obrázku níže.

![Výkon data lake storage gen2](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Používejte virtuální míchací sítě s větší šířkou pásma sítě.**  Velikost šířky pásma sítě může být kritickým bodem, pokud je menší šířka pásma sítě než propustnost Data Lake Storage Gen2.  Různé virtuální počítače budou mít různé velikosti šířky pásma sítě.  Zvolte typ virtuálního připojení, který má největší možnou šířku pásma sítě.

### <a name="yarn-layer"></a>Vrstva příze

**Použijte menší kontejnery YARN.**  Zmenšete velikost každého kontejneru YARN a vytvořte více kontejnerů se stejným množstvím prostředků.

![Výkon data lake storage gen2](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

V závislosti na vaší pracovní zátěži bude vždy minimální velikost kontejneru YARN, která je potřebná. Pokud vyberete příliš malý kontejner, vaše úlohy se dostanou do problémů s nepaměti. Typicky YARN kontejnery by neměly být menší než 1 GB. Je běžné vidět kontejnery 3GB YARN. Pro některé úlohy můžete potřebovat větší kontejnery YARN.  

**Zvyšte jádra na kontejner YARN.**  Zvyšte počet jader přidělených každému kontejneru, abyste zvýšili počet paralelních úloh, které se spouštějí v každém kontejneru.  To funguje pro aplikace, jako je Spark, které spouštějí více úloh na kontejner.  Pro aplikace, jako je Hive, které běží jeden podproces v každém kontejneru, je lepší mít více kontejnerů, spíše než více jader na kontejner.

### <a name="workload-layer"></a>Vrstva pracovního vytížení

**Použijte všechny dostupné kontejnery.**  Nastavte počet úkolů, které mají být stejné nebo větší než počet dostupných kontejnerů tak, aby byly využity všechny prostředky.

![Výkon data lake storage gen2](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Neúspěšné úkoly jsou nákladné.** Pokud každý úkol má velké množství dat ke zpracování, pak selhání úkolu má za následek nákladné opakování.  Proto je lepší vytvořit více úkolů, z nichž každý zpracovává malé množství dat.

Kromě výše uvedených obecných pokynů má každá aplikace k dispozici různé parametry pro vyladění pro tuto konkrétní aplikaci. V následující tabulce jsou uvedeny některé parametry a odkazy, které můžete začít s laděním výkonu pro každou aplikaci.

| Úloha | Parametr pro nastavení úkolů |
|----------|------------------------|
| [Spark ve službě HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Num-vykonavatelé</li><li>Paměť exekutora</li><li>Exekutor-jádra</li></ul> |
| [Hive na HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>hive.tez.container.size</li></ul> |
| [MapReduce na HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>Mapreduce.map.paměť</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduje</li></ul> |
| [Storm v HDInsightu](data-lake-storage-performance-tuning-storm.md)| <ul><li>Počet pracovních procesů</li><li>Počet instancí vykonavatele výtoku</li><li>Počet instancí vykonavatele šroubů </li><li>Počet úloh výtoku</li><li>Počet úkolů šroubu</li></ul>|

## <a name="see-also"></a>Viz také
* [Přehled Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
