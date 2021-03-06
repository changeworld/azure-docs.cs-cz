---
title: Přehled – Databáze Azure pro MySQL
description: Seznamte se se službou Azure Database for MySQL, což je relační databázová služba v cloudu Microsoftu založená na mysql community edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 8f49811ad0d40c70933d32227cfb17a5144b857a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067822"
---
# <a name="what-is-azure-database-for-mysql"></a>Co je Azure Database for MySQL?

Azure Database for MySQL je relační databázová služba v cloudu Microsoftu založená na databázovém stroji [MySQL Community Edition](https://www.mysql.com/products/community/) (dostupném pod licencí GPLv2), verze 5.6, 5.7 a 8.0. Azure Database for MySQL poskytuje:

- Integrovanou vysokou dostupnost bez dalších poplatků.
- Předvídatelný výkon a všeobecné ceny při průběžných platbách.
- Škálování podle potřeby během několika sekund.
- Zabezpečení pro ochranu neaktivních uložených dat i přenášených dat.
- Automatické zálohování a obnovení k určitému bodu v čase po dobu až 35 dnů.
- Zabezpečení a dodržování předpisů na podnikové úrovni.

Tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Díky tomu se můžete zaměřit na rychlý vývoj aplikací a zkrácení doby dodání produktu na trh, namísto vynakládání prostředků a drahocenného času na správu virtuálních počítačů a infrastruktury. Kromě toho můžete pokračovat ve vývoji aplikací pomocí libovolných opensourcových nástrojů a platforem a dodávat je tak rychle a efektivně, jak váš podnik vyžaduje – a to vše, aniž byste se museli učit nové dovednosti.

![Koncepční diagram Azure Database for MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Tento článek je úvod do Azure Database for MySQL základní koncepty a funkce týkající se výkonu, škálovatelnost a možnosti správy, s odkazy na prozkoumání podrobností. Tyto rychlé starty vám pomůžou začít:

- [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MySQL pomocí Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

Řadu ukázek v Azure CLI najdete tady:

- [Ukázky v Azure CLI pro službu Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Úprava výkonu a škálování během několika sekund
Služba Azure Database for MySQL nabízí několik úrovní služeb: Základní, obecné účely a Optimalizované pro paměť. Každá úroveň nabízí různý výkon a možnosti pro podporu databázových úloh od zcela nenáročných až po velmi náročné. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. Podrobnosti [najdete v tématu Cenové úrovně.](concepts-service-tiers.md)

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování
Jak se rozhodnout, kdy přidat a kdy odebrat výkon? Můžete využít integrované funkce monitorování výkonu a upozorňování v kombinaci s hodnocením výkonu na základě virtuálních jader. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity virtuálních jader v závislosti na stávajících nebo předpokládaných požadavcích. Podrobnosti najdete v tématu [Upozornění](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Udržujte své aplikace a podnikáni v chodu
Dostupnost služby Azure se smlouvou o úrovní služeb (SLA) dosahuje špičkové hodnoty 99,99 %, protože staví na globální síti Microsoftem spravovaných datových center. Může tedy udržet vaše aplikace v nepřetržitém provozu každý den po celý rok. S každou databází Azure pro MySQL server můžete využít integrované zabezpečení, odolnost proti chybám a ochranu dat, které byste jinak museli kupovat nebo navrhovat, vytvářet a spravovat. S Azure Database for MySQL můžete použít obnovení v čase k obnovení serveru do dřívějšího stavu, již za 35 dní.

## <a name="secure-your-data"></a>Zabezpečení dat
Databázové služby Azure mají tradici zabezpečení dat, kterou Azure Database for MySQL podporuje, s funkcemi, které omezují přístup, chrání data v klidovém stavu a v pohybu a pomáhají sledovat aktivitu. Informace o zabezpečení platformy Azure najdete v [Centru zabezpečení Azure](https://www.microsoft.com/trustcenter/security). Další informace o funkcích zabezpečení Azure Database for MySQL najdete v přehledu [zabezpečení](concepts-security.md).

## <a name="contacts"></a>Kontakty
V případě jakýchkoli dotazů nebo návrhů, které můžete mít k práci s Databází Azure pro MySQL, pošlete e-mail do Azure Database for MySQL Team[ @Ask (Azure DB for MySQL).](mailto:AskAzureDBforMySQL@service.microsoft.com) Tato e-mailová adresa není alias technické podpory.

Můžete také využít tyto komunikační kanály:

- Pro podporu Azure [vytvořte lístek podpory na webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Další kroky
Teď, když jste si přečetli úvod do databáze Azure pro MySQL a odpověděli na otázku "Co je databáze Azure pro MySQL?" jste připraveni:

- Na stránce s cenami najdete porovnání nákladů a kalkulačky. [Ceny](https://azure.microsoft.com/pricing/details/mysql/)
- Začněte vytvářet první server. [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Vytvořte si svou první aplikaci pomocí preferovaného jazyka: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
