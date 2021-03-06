---
title: Přehled škálovacích sad virtuálních strojů Azure
description: Seznamte se se škálovacími sadami virtuálních počítačů Azure a způsobem automatického škálování aplikací.
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: overview
ms.custom: mvc
ms.date: 09/26/2019
ms.author: mimckitt
ms.openlocfilehash: 03e3c7b5c0696069729d3067faad8ceb91fc611f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272538"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Co jsou škálovací sady virtuálních počítačů?
Škálovací sady virtuálních počítačů Azure umožňují vytvářet a spravovat skupiny identických virtuálních počítačů s vyrovnáváním zatížení. Počet instancí virtuálních počítačů se může automaticky zvyšovat nebo snižovat s ohledem na požadavky nebo definovaný plán. Škálovací sady zajišťují vysokou dostupnost aplikací a umožňují centrálně spravovat, konfigurovat a aktualizovat velký počet virtuálních počítačů. S využitím škálovacích sad virtuálních počítačů můžete sestavovat rozsáhlé služby pro oblasti, jako jsou výpočty, velké objemy dat a úlohy kontejneru.


## <a name="why-use-virtual-machine-scale-sets"></a>Proč používat škálovací sady virtuálních počítačů?
Za účelem zajištění redundance a vyššího výkonu se aplikace obvykle distribuují na více instancí. Zákazníci můžou k vaší aplikaci přistupovat přes nástroj pro vyrovnávání zatížení, který distribuuje požadavky na jednu z instancí aplikace. V případě, že potřebujete provést údržbu nebo aktualizaci instance aplikace, musí se vaši zákazníci distribuovat do jiné dostupné instance aplikace. Abyste udrželi krok s dalšími požadavky zákazníků, můžete zvýšit počet instancí aplikace, na kterých se vaše aplikace spouští.

Škálovací sady virtuálních počítačů Azure poskytují možnosti správy pro aplikace, které se spouštějí na více virtuálních počítačích, [automatické škálování prostředků](virtual-machine-scale-sets-autoscale-overview.md) a vyrovnávání zatížení provozu. Škálovací sady poskytují následující klíčové výhody:

- **Snadné vytváření a správa několika virtuálních počítačů**
    - Pokud máte mnoho virtuálních počítačů, na kterých se vaše aplikace spouští, je důležité v celém prostředí udržovat konzistentní konfiguraci. Pro zajištění spolehlivého výkonu aplikace by všechny virtuální počítače měly mít stejnou velikost, konfiguraci disků a instalace aplikace.
    - V případě škálovacích sad se všechny instance virtuálních počítačů vytvářejí s využitím stejné základní image operačního systému a konfigurace. Tento přístup umožňuje snadnou správu stovek virtuálních počítačů bez dalších úloh konfigurace nebo správy sítě.
    - Škálovací sady podporují použití [Azure balancer](../load-balancer/load-balancer-overview.md) pro základní distribuci provozu vrstvy 4 a [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) pro pokročilejší distribuci provozu vrstvy 7 a ukončení TLS.

- **Zajištění vysoké dostupnosti a odolnosti aplikací**
    - Škálovací sady slouží ke spouštění více instancí vaší aplikace. Pokud u jedné z těchto instancí virtuálních počítačů dojde k problému, zákazníci můžou pokračovat v přístupu k aplikaci přes některou z dalších instancí virtuálních počítačů, a to s minimálním přerušením.
    - Pokud potřebujete větší dostupnost, můžete použít [zóny dostupnosti](../availability-zones/az-overview.md) k automatické distribuci instancí virtuálních počítačů ve škálovací sadě v rámci jednoho datacentra nebo do více datacenter.

- **Možnost automatického škálování aplikace s ohledem na měnící se požadavky na prostředky**
    - Požadavky zákazníků na vaši aplikaci se v průběhu dne nebo týdne můžou měnit. S ohledem na požadavky zákazníků můžou škálovací sady automaticky zvýšit počet instancí virtuálních počítačů při nárůstu poptávky a při snížení poptávky pak počet instancí virtuálních počítačů snížit.
    - Automatické škálování také minimalizuje počet nepotřebných instancí virtuálních počítačů s vaší aplikací při nízké poptávce, zatímco zákazníci mají stále k dispozici přijatelnou úroveň výkonu díky automatickému přidávání dalších instancí virtuálních počítačů, když se poptávka zvýší. Tato schopnost pomáhá snižovat náklady a efektivně vytvářet prostředky Azure podle potřeby.

- **Práce ve velkém měřítku**
    - Škálovací sady podporují až 1 000 instancí virtuálních počítačů. Pokud vytvoříte a nahrajete vlastní image virtuálních počítače, limit je 600 instancí virtuálních počítače.
    - Nejlepší výkon s produkčními úlohami nawebu využijte [spravované disky Azure](../virtual-machines/windows/managed-disks-overview.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Rozdíly mezi virtuálními počítači a škálovacími sadami
Škálovací sady se sestavují z virtuálních počítačů. Škálovací sady poskytují vrstvy správy a automatizace pro spouštění a škálování aplikací. Místo toho můžete ručně vytvářet a spravovat jednotlivé virtuální počítače nebo integrovat stávající nástroje a vytvořit tak podobnou úroveň automatizace. Následující tabulka popisuje výhody škálovacích sad v porovnání s ruční správou několika instancí virtuálních počítačů.

| Scénář                           | Ručně vytvořená skupina virtuálních počítačů                                                                    | Škálovací sada virtuálních počítačů |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Přidání dalších instancí virtuálních počítačů        | Ruční proces vytvoření, konfigurace a zajištění dodržování předpisů                             | Automatické vytvoření s využitím centrální konfigurace |
| Vyrovnávání a distribuce provozu | Ruční proces vytvoření a konfigurace nástroje pro vyrovnávání zatížení Azure nebo služby Application Gateway      | Možnost automatického vytvoření nástroje pro vyrovnávání zatížení Azure nebo služby Application Gateway a integrace s nimi |
| Vysoká dostupnost a redundance   | Ruční vytvoření skupiny dostupnosti nebo distribuce a sledování virtuálních počítačů napříč zónami dostupnosti | Automatická distribuce instancí virtuálních počítačů napříč zónami dostupnosti nebo skupinami dostupnosti |
| Škálování virtuálních počítačů                     | Ruční monitorování a služba Azure Automation                                                 | Automatické škálování na základě metrik hostitele, metrik hosta, Application Insights nebo plánu |

Za použití škálovacích sad se neúčtují žádné další poplatky. Platíte pouze za základní výpočetní prostředky, jako jsou instance virtuálních počítačů, nástroj pro vyrovnávání zatížení nebo spravované diskové úložiště. Za funkce správy a automatizace, jako jsou automatické škálování a redundance, se neúčtují žádné další poplatky nad rámec použití virtuálních počítačů.

## <a name="how-to-monitor-your-scale-sets"></a>Jak sledovat škálovací sady

Použijte [Azure Monitor pro virtuální počítače](../azure-monitor/insights/vminsights-overview.md), který má jednoduchý proces registrace a bude automatizovat shromažďování důležitých čítačů výkonu procesoru, paměti, disku a sítě z virtuálních počítačů ve vaší škálovací sadě. Obsahuje také další možnosti monitorování a předdefinované vizualizace, které vám pomohou zaměřit se na dostupnost a výkon škálovacích sad.

Povolte monitorování [pro aplikaci škálovací sady virtuálních strojů](../azure-monitor/app/azure-vm-vmss-apps.md) pomocí Application Insights a shromažďujte podrobné informace o vaší aplikaci, včetně zobrazení stránek, požadavků aplikací a výjimek. Dále ověřte dostupnost aplikace konfigurací [testu dostupnosti](../azure-monitor/app/monitor-web-app-availability.md) pro simulaci provozu uživatelů.

## <a name="next-steps"></a>Další kroky
Začněte vytvořením své první škálovací sady virtuálních počítačů na webu Azure Portal.

> [!div class="nextstepaction"]
> [Vytvoření škálovací sady na webu Azure Portal](quick-create-portal.md)
