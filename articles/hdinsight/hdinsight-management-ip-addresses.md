---
title: IP adresy azure hdinsight pro správu
description: Zjistěte, ze kterých IP adres je nutné povolit příchozí provoz, abyste mohli správně nakonfigurovat skupiny zabezpečení sítě a uživatelem definované trasy pro virtuální sítě pomocí Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 5e8f39b58f258742108fe323d9395efd87bc288f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271810"
---
# <a name="hdinsight-management-ip-addresses"></a>IP adresy hdinsight pro správu

> [!Important]
> Ve většině případů můžete nyní použít funkci [servisního tagu](hdinsight-service-tags.md) pro skupiny zabezpečení sítě namísto ručního přidávání adres IP. Nové oblasti budou přidány pouze pro značky služeb a statické IP adresy budou nakonec zastaralé.

Pokud k řízení příchozího provozu do clusteru HDInsight používáte skupiny zabezpečení sítě (NSG) nebo uživatelem definované trasy (UDR), musíte zajistit, aby váš cluster mohl komunikovat s kritickými službami stavu a správy Azure.  Některé IP adresy pro tyto služby jsou specifické pro oblast a některé z nich platí pro všechny oblasti Azure. Možná budete muset povolit provoz také ze služby Azure DNS, pokud nepoužíváte vlastní DNS.

V následujících částech jsou popsány konkrétní adresy IP, které musí být povoleny.

## <a name="azure-dns-service"></a>Služba Azure DNS

Pokud používáte službu DNS poskytovanou službou Azure, povolte přístup z __168.63.129.16__ na portu 53. Další informace najdete v [dokumentu Překlad názvů pro virtuální uživatele a instance rolí.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) Pokud používáte vlastní DNS, tento krok přeskočte.

## <a name="health-and-management-services-all-regions"></a>Zdravotnictví a management: Všechny regiony

Povolte provoz z následujících IP adres pro služby stavu a správy Azure HDInsight, které platí pro všechny oblasti Azure:

| Zdrojová IP adresa | Cíl  | Směr |
| ---- | ----- | ----- |
| 168.61.49.99 | \*:443 | Příchozí |
| 23.99.5.239 | \*:443 | Příchozí |
| 168.61.48.131 | \*:443 | Příchozí |
| 138.91.141.162 | \*:443 | Příchozí |

## <a name="health-and-management-services-specific-regions"></a>Zdravotnictví a řízení: Specifické regiony

Povolte provoz z IP adres uvedených pro služby stavu a správy Azure HDInsight v konkrétní oblasti Azure, kde se nacházejí vaše prostředky:

> [!IMPORTANT]  
> Pokud oblast Azure, kterou používáte, není uvedena, použijte funkci [značky služby](hdinsight-service-tags.md) pro skupiny zabezpečení sítě.

| Země | Region (Oblast) | Povolené zdrojové IP adresy | Povolený cíl | Směr |
| ---- | ---- | ---- | ---- | ----- |
| Asie | Východní Asie | 23.102.235.122</br>52.175.38.134 | \*:443 | Příchozí |
| &nbsp; | Jihovýchodní Asie | 13.76.245.160</br>13.76.136.249 | \*:443 | Příchozí |
| Austrálie | Austrálie – východ | 104.210.84.115</br>13.75.152.195 | \*:443 | Příchozí |
| &nbsp; | Austrálie – jihovýchod | 13.77.2.56</br>13.77.2.94 | \*:443 | Příchozí |
| Brazílie | Brazílie – jih | 191.235.84.104</br>191.235.87.113 | \*:443 | Příchozí |
| Kanada | Kanada – východ | 52.229.127.96</br>52.229.123.172 | \*:443 | Příchozí |
| &nbsp; | Střední Kanada | 52.228.37.66</br>52.228.45.222 |\*: 443 | Příchozí |
| Čína | Čína – sever | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*:443 | Příchozí |
| &nbsp; | Čína – východ | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*:443 | Příchozí |
| &nbsp; | Čína Sever 2 | 40.73.37.141</br>40.73.38.172 | \*:443 | Příchozí |
| &nbsp; | Čína východ 2 | 139.217.227.106</br>139.217.228.187 | \*:443 | Příchozí |
| Evropa | Severní Evropa | 52.164.210.96</br>13.74.153.132 | \*:443 | Příchozí |
| &nbsp; | Západní Evropa| 52.166.243.90</br>52.174.36.244 | \*:443 | Příchozí |
| Francie | Francie – střed| 20.188.39.64</br>40.89.157.135 | \*:443 | Příchozí |
| Německo | Německo – střed | 51.4.146.68</br>51.4.146.80 | \*:443 | Příchozí |
| &nbsp; | Německo – severovýchod | 51.5.150.132</br>51.5.144.101 | \*:443 | Příchozí |
| Indie | Indie – střed | 52.172.153.209</br>52.172.152.49 | \*:443 | Příchozí |
| &nbsp; | Indie – jih | 104.211.223.67<br/>104.211.216.210 | \*:443 | Příchozí |
| Japonsko | Japonsko – východ | 13.78.125.90</br>13.78.89.60 | \*:443 | Příchozí |
| &nbsp; | Japonsko – západ | 40.74.125.69</br>138.91.29.150 | \*:443 | Příchozí |
| Jižní Korea | Jižní Korea – střed | 52.231.39.142</br>52.231.36.209 | \*:443 | Příchozí |
| &nbsp; | Jižní Korea – jih | 52.231.203.16</br>52.231.205.214 | \*:443 | Příchozí
| Spojené království | Spojené království – západ | 51.141.13.110</br>51.141.7.20 | \*:443 | Příchozí |
| &nbsp; | Spojené království – jih | 51.140.47.39</br>51.140.52.16 | \*:443 | Příchozí |
| Spojené státy | USA – střed | 13.89.171.122</br>13.89.171.124 | \*:443 | Příchozí |
| &nbsp; | USA – východ | 13.82.225.233</br>40.71.175.99 | \*:443 | Příchozí |
| &nbsp; | USA – středosever | 157.56.8.38</br>157.55.213.99 | \*:443 | Příchozí |
| &nbsp; | USA – středozápad | 52.161.23.15</br>52.161.10.167 | \*:443 | Příchozí |
| &nbsp; | USA – západ | 13.64.254.98</br>23.101.196.19 | \*:443 | Příchozí |
| &nbsp; | USA – západ 2 | 52.175.211.210</br>52.175.222.222 | \*:443 | Příchozí |
| &nbsp; | SAE Sever | 65.52.252.96</br>65.52.252.97 | \*:443 | Příchozí |

Informace o IP adresách, které se mají používat pro Azure Government, najdete v dokumentu [Azure Government Intelligence + Analytics.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics)

Další informace naleznete v části [Řízení síťového provozu.](hdinsight-plan-virtual-network-deployment.md#networktraffic)

Pokud používáte uživatelem definované trasy (UDR), měli byste zadat trasu a povolit odchozí provoz z virtuální sítě do výše uvedených IP služeb s dalším směrováním nastaveným na "Internet".

## <a name="next-steps"></a>Další kroky

* [Vytváření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md)
* [Značky služeb skupiny zabezpečení sítě (NSG) pro Azure HDInsight](hdinsight-service-tags.md)
