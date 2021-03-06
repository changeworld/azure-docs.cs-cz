---
title: Použití rozšířené ochrany před hrozbami – databáze Azure pro PostgreSQL – jeden server
description: Ochrana před hrozbami detekuje neobvyklé databázové aktivity označující potenciální ohrožení zabezpečení databáze.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 8b7f52ea318432e97a450a54526f6481b14139c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74776139"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql---single-server"></a>Pokročilá ochrana před hrozbami pro databázi Azure pro PostgreSQL – jeden server

Advanced Threat Protection pro službu Azure Database for PostgreSQL detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

Advanced Threat Protection je součástí nabídky Advanced Data Security, což je jednotný balíček pro pokročilé možnosti zabezpečení. Pokročilá ochrana před internetovými hrozbami je přístupná a spravovaná prostřednictvím [portálu Azure](https://portal.azure.com) a je aktuálně ve verzi Preview.

> [!NOTE]
> Funkce Advanced Threat Protection **není** dostupná v následujících oblastech vlády Azure a suverénního cloudu: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Obecné informace o dostupnosti produktů naleznete [na produktech dostupných podle regionu.](https://azure.microsoft.com/global-infrastructure/services/)
>

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde se Azure Database for PostgreSQL nasazuje pro servery optimalizované pro obecné účely a paměť.

## <a name="set-up-threat-detection"></a>Nastavení detekce hrozeb
1. Spusťte portál [https://portal.azure.com](https://portal.azure.com)Azure na webu .
2. Přejděte na konfigurační stránku serveru Azure Database for PostgreSQL, který chcete chránit. V nastavení zabezpečení vyberte **Upřesnit ochranu před internetovými hrozbami (Náhled).**
3. Na konfigurační stránce **Advanced Threat Protection (Preview):**

   - Povolte rozšířenou ochranu před internetovými hrozbami na serveru.
   - V **rozšířené nastavení ochrany před internetovými zprávami**, v poli **Odeslat výstrahy do** textového pole, zadejte seznam e-mailů pro příjem výstrah zabezpečení při zjišťování neobvyklých databázových aktivit.
  
   ![Nastavení detekce hrozeb](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Prozkoumejte neobvyklé databázové aktivity

Obdržíte e-mailové oznámení po zjištění neobvyklé databázové aktivity. E-mail obsahuje informace o podezřelé události zabezpečení, včetně povahy anomální chaktivity, název databáze, název serveru, název aplikace a čas události. Kromě toho e-mail poskytuje informace o možných příčinách a doporučených akcích k prošetření a zmírnění potenciálního ohrožení databáze.
    
1. Kliknutím na odkaz **Zobrazit poslední výstrahy** v e-mailu spusťte portál Azure portal a zobrazte stránku výstrah Centra zabezpečení Azure, která poskytuje přehled aktivních hrozeb zjištěných v databázi SQL.
    
    ![Sestava anomální aktivity](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Zobrazit aktivní hrozby:

    ![Aktivní hrozby](./media/howto-database-threat-protection-portal/active-threats.png)

2. Kliknutím na konkrétní výstrahu získáte další podrobnosti a akce pro vyšetřování této hrozby a nápravu budoucích hrozeb.
    
    ![Konkrétní výstraha](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Prozkoumejte výstrahy detekce hrozeb

Pokročilá ochrana před internetovými hrozbami integruje své výstrahy do [Azure Security Center](https://azure.microsoft.com/services/security-center/). 

Kliknutím na **výstrahy zabezpečení** v části **OCHRANA PŘED HROZBOU** spusťte stránku výstrah Centra zabezpečení Azure a získejte přehled o aktivních hrozbách SQL zjištěných v databázi.

  ![Ochrana před hrozbami asc](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Další informace o cenách najdete na [stránce Azure Database for PostgreSQL Pricing .](https://azure.microsoft.com/pricing/details/postgresql/)  
