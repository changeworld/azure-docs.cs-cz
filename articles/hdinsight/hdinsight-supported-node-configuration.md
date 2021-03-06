---
title: Podporované konfigurace uzlů Azure HDInsight
description: Seznamte se s minimálními a doporučenými konfiguracemi uzlů clusteru HDInsight.
keywords: velikosti virtuálních počítačů, velikosti clusteru, konfigurace clusteru
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484782"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Jaké jsou výchozí a doporučené konfigurace uzlů pro Azure HDInsight?

Tento článek popisuje výchozí a doporučené konfigurace uzlů pro clustery Azure HDInsight.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Výchozí a minimální doporučená konfigurace uzlu a velikosti virtuálních počítačů pro clustery

V následujících tabulkách jsou uvedeny výchozí a doporučené velikosti virtuálních počítačů pro clustery HDInsight.  Tyto informace jsou nezbytné k pochopení velikosti virtuálních počítačů, které se mají použít při vytváření skriptů PowerShellnebo Azure CLI k nasazení clusterů HDInsight.

Pokud potřebujete více než 32 pracovních uzlů v clusteru, vyberte velikost hlavního uzlu s alespoň 8 jádry a 14 GB paměti RAM. 

Jediné typy clusterů, které mají datové disky, jsou clustery Kafka a HBase s povolenou funkcí Zrychlené zápisy. HDInsight podporuje velikosti disků P30 a S30 v těchto scénářích.

Specifikace všech minimálních doporučených typů virtuálních počítačů použitých v tomto dokumentu jsou shrnuty v následující tabulce.

| Velikost              | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Maximální počet síťových připojení / Očekávaná šířka pásma sítě (Mb/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16 / 16x500           | 4 / 3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32 / 32x500           | 8 / 6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64 / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2×500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4×500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8×500               | 4 / 1 000                     |

Další podrobnosti o specifikacích jednotlivých typů virtuálních počítačů najdete v následujících dokumentech:

* [Univerzální velikosti virtuálních strojů: Dv2 série 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Velikosti virtuálních strojů optimalizované pro paměť: Řada DV2 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Univerzální velikosti virtuálních strojů: Av2 série 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Všechny podporované regiony kromě Brazílie – jih a Japonsko – západ

> [!Note]
> Chcete-li získat identifikátor skladové položky pro použití `Standard_` v powershellu a dalších skriptech, přidejte na začátek všech skladových jednotek virtuálních zařízení v následujících tabulkách. Například `D12_v2` by `Standard_D12_v2`se stal .

| Typ clusteru | Hadoop | HBase | Interaktivní dotaz | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Hlava: výchozí velikost virtuálního počítače | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Hlava: minimální doporučené velikosti virtuálních počítače | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Pracovník: výchozí velikost virtuálního počítače | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 s 2 disky S30 na makléře |
| Pracovník: minimální doporučené velikosti virtuálních počítače | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: výchozí velikost virtuálního počítače |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: minimální doporučené velikosti virtuálních počítače |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Ml Services: výchozí velikost virtuálního počítače |  |  |  |  |  | D4_v2 |  |
| Ml Služby: minimální doporučená velikost virtuálního počítače |  |  |  |  |  | D4_v2 |  |

\*= Velikosti virtuálních zařízení pro clustery balíčků zabezpečení společnosti Spark Enterprise Package (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Pouze Brazílie na jih a Japonsko na západ

| Typ clusteru | Hadoop | HBase | Interaktivní dotaz | Storm | Spark | Služby ML |
|---|---|---|---|---|---|---|
| Hlava: výchozí velikost virtuálního počítače | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Hlava: minimální doporučené velikosti virtuálních počítače | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Pracovník: výchozí velikost virtuálního počítače | D4 | D4 | D14 | D3 | D13 | D4 |
| Pracovník: minimální doporučené velikosti virtuálních počítače | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: výchozí velikost virtuálního počítače |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: minimální doporučené velikosti virtuálních počítače |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Ml Services: výchozí velikosti virtuálních počítače |  |  |  |  |  | D4 |
| Ml Služby: minimální doporučené velikosti virtuálních počítače |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Hlava je známá jako *Nimbus* pro typ clusteru Storm.
> - Pracovník je označován jako *vedoucí* pro typ clusteru Storm.
> - Pracovník je označován jako *Oblast* pro typ clusteru HBase.

## <a name="next-steps"></a>Další kroky

* [Jaké jsou komponenty a verze Apache Hadoop dostupné v HDInsightu?](hdinsight-component-versioning.md)
