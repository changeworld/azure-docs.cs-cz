---
title: Způsob uplatňování slev za rezervaci na Azure Database for MariaDB
description: Způsob uplatňování slev za rezervaci na Azure Database for MariaDB
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 340d395278eb924570e07d106308e63be3c2f9af
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75995517"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>Způsob uplatňování slev za rezervaci na Azure Database for MariaDB

Když si zakoupíte rezervovanou kapacitu Azure Database for MariaDB, na servery MariaDB, které vyhovují příslušným atributům a množství rezervace, se automaticky uplatňuje sleva za rezervaci. Rezervace pokrývá pouze náklady na výpočetní prostředky Azure Database for MariaDB. Poplatky za úložiště a sítě se vám budou účtovat za použití normálních sazeb. 

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Nevyužitá sleva za rezervaci se ***nenahrazuje***. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.</br>

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny přijdete.

## <a name="discount-applied-to-azure-database-for-mariadb"></a>Sleva uplatněná na Azure Database for MariaDB

Sleva za rezervovanou kapacitu Azure Database for MariaDB se na provoz serverů MariaDB uplatňuje po hodinách. Rezervace, kterou si zakoupíte, se přiřadí k využívání výpočetních prostředků spuštěnými servery Azure Database for MariaDB. V případě serverů MariaDB, které neběží celou hodinu, se rezervace automaticky využije pro jiný server Azure Database for MariaDB vyhovující atributům rezervace. Sleva se může uplatnit i na souběžně spuštěné servery Azure Database for MariaDB. Pokud nemáte server MariaDB, který běží celou hodinu a vyhovuje atributům rezervace, nezískáte pro danou hodinu celou výhodu slevy za rezervaci.

Následující příklady ukazují, jak se sleva za rezervovanou kapacitu Azure Database for MariaDB uplatňuje v závislosti na počtu zakoupených jader a na tom, kdy jsou tato jádra spuštěná.

* **Příklad 1:** Zakoupíte si rezervovanou kapacitu Azure Database for MariaDB pro 8 virtuálních jader. Pokud používáte server Azure Database for MariaDB s 16 virtuálními jádry, který vyhovuje ostatním atributům rezervace, za využití výpočetních prostředků pro 8 virtuálních jader serveru MariaDB se vám bude účtovat cena při průběžných platbách a na jednu hodinu využití výpočetních prostředků pro server MariaDB s 8 virtuálními jádry získáte slevu za rezervaci.</br>

U ostatních příkladů předpokládejme, že si zakoupíte rezervovanou kapacitu Azure Database for MariaDB pro 16 virtuálních jader a ostatní atributy rezervace odpovídají spuštěným izolovaným serverům MariaDB.

* **Příklad 2:** Na hodinu spustíte dva servery Azure Database for MariaDB, z nichž každý má 8 virtuálních jader. Sleva za rezervaci 16 virtuálních jader se uplatní na využití výpočetních prostředků pro oba servery Azure Database for MariaDB s 8 virtuálními jádry.

* **Příklad 3:** Od 13:00 do 13:30 spustíte jeden server Azure Database for MariaDB s 16 virtuálními jádry. Od 13:30 do 14:00 spustíte jiný server Azure Database for MariaDB s 16 virtuálními jádry. Na obě se bude vztahovat sleva za rezervaci.

* **Příklad 4:** Od 13:00 do 13:45 spustíte jeden server Azure Database for MariaDB s 16 virtuálními jádry. Od 13:30 do 14:00 spustíte jiný server Azure Database for MariaDB s 16 virtuálními jádry. Za 15 minut, kdy se doba chodu obou databází překrývá, zaplatíte průběžnou platbu. Po zbytek doby se na využití výpočetních prostředků uplatňuje sleva za rezervaci.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás
Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
