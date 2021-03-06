---
title: Diagnostika problémů s výkonem pomocí Azure Application Insights | Dokumentace Microsoftu
description: Kurz popisující, jak v aplikaci vyhledat a diagnostikovat problémy s výkonem pomocí Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/13/2019
ms.custom: mvc
ms.openlocfilehash: 98d7c1552a7b1f2b02ae4df1cad24e20f7ac76e1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239592"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Vyhledání a diagnostika problémů s výkonem pomocí Azure Application Insights

Azure Application Insights shromažďuje telemetrii z vaší aplikace a pomáhá tak analyzovat její provoz a výkon.  Tyto informace můžete využít k identifikaci problémů, ke kterým může docházet, nebo k identifikaci zlepšení aplikace, která by měla největší dopad na uživatele.  Tento kurz vás provede procesem analýzy výkonu serverových komponent vaší aplikace i z pohledu uživatele.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Identifikace výkonu operací na straně serveru
> * Analýza operací serveru a určení původní příčiny pomalého výkonu
> * Identifikace nejpomalejších operací na straně klienta
> * Analýza podrobností o zobrazení stránek pomocí dotazovacího jazyka


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/downloads/) s následujícími úlohami:
    - Vývoj pro ASP.NET a web
    - Vývoj pro Azure
- Nasadit do Azure aplikaci .NET a [povolit sadu Application Insights SDK](../../azure-monitor/app/asp-net.md).
- [Povolit Application Insights Profiler](../../azure-monitor/app/profiler.md#installation) pro vaši aplikaci.

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="identify-slow-server-operations"></a>Identifikace pomalých operací serveru
Application Insights shromažďuje podrobnosti o výkonu různých operací ve vaší aplikaci. Díky identifikaci operací, které trvají nejdéle, můžete diagnostikovat potenciální problémy nebo lépe cílit probíhající vývoj a vylepšit tak celkový výkon aplikace.

1. Vyberte **Application Insights** a pak vyberte své předplatné.  
1. Panel **Výkon** otevřete výběrem možnosti **Výkon** v nabídce **Prozkoumat** nebo kliknutím na graf **Doba odezvy serveru**.

    ![Výkon](media/tutorial-performance/1-overview.png)

2. Na panelu **Výkon** se zobrazí počet a průměrná doba trvání jednotlivých operací aplikace.  Tyto informace můžete využít k identifikaci operací, které mají největší dopad na uživatele. V tomto příkladu jsou vzhledem k dlouhé době trvání a počtu volání vhodnými kandidáty na prošetření operace **GET Customers/Details** a **GET Home/Index**.  Jiné operace můžou mít delší dobu trvání, ale volají se jen zřídka, takže by jejich vylepšení mělo minimální vliv.  

    ![Panel výkonnostního serveru](media/tutorial-performance/2-server-operations.png)

3. Graf aktuálně ukazuje průměrnou dobu trvání vybraných operací v průběhu času. Pokud chcete najít problémy s výkonem, můžete přepnout na 95. percentil. Připnutím na graf přidejte operace, které vás zajímají.  Ukazuje se, že by bylo vhodné prošetřit několik vrcholů.  Proveďte další izolaci zúžením časového okna grafu.

    ![Připnutí operací](media/tutorial-performance/3-server-operations-95th.png)

4.  Panel Výkon na pravé straně ukazuje distribuci doby trvání různých požadavků pro vybranou operaci.  Zmenšete časové okno tak, aby začínalo přibližně na 95. percentilu. Na kartě přehledu Nejčastější 3 závislosti můžete na první pohled zjistit, že na pomalé transakce mají pravděpodobně vliv externí závislosti.  Kliknutím na tlačítko s počtem ukázek zobrazíte seznam ukázek. Pak můžete vybrat libovolnou ukázku a zobrazit podrobnosti o transakci.

5.  Na první pohled vidíte, na celkovou dobu trvání transakce má největší vliv volání tabulky Azure Fabrikamaccount. Také vidíte, že výjimka způsobila jeho selhání. Kliknutím na libovolnou položku v seznamu můžete na pravé straně zobrazit její podrobnosti. [Další informace o prostředí pro diagnostiku transakcí](../../azure-monitor/app/transaction-diagnostics.md)

    ![Podrobnosti o operaci od konce](media/tutorial-performance/4-end-to-end.png)
    

6.  **Profiler** pomáhá s podrobnější diagnostikou na úrovni kódu díky zobrazení skutečného kódu spuštěného pro příslušnou operaci a času, který zabraly jednotlivé kroky. Vzhledem k tomu, že se profiler spouští pravidelně, některé operace nemusejí mít trasování.  V průběhu času by trasování mělo mít více operací.  Pokud chcete pro operaci spustit profiler, klikněte na **Trasování Profileru**.
5.  Trasování zobrazí pro každou operaci jednotlivé události, takže můžete diagnostikovat původní příčinu v průběhu celé operace.  Klikněte na jeden z horních příkladů s nejdelší dobou trvání.
6.  Klepnutím na **tlačítko Horká cesta** zvýrazněte konkrétní cestu událostí, které nejvíce přispívají k celkové době trvání operace.  V tomto příkladu vidíte, že nejpomalejší volání pochází z metody *FabrikamFiberAzureStorage.GetStorageTableData*. Částí, která trvá nejdéle, je metoda *CloudTable.CreateIfNotExist*. Pokud se tento řádek kódu provádí při každém volání funkce, budou se spotřebovávat zbytečná síťová volání a prostředky procesoru. Nejlepším způsobem, jak kód opravit, je vložit tento řádek do některé metody po spuštění, která se provede pouze jednou.

    ![Podrobnosti v profileru](media/tutorial-performance/5-hot-path.png)

7.  **Tip k výkonu** v horní části obrazovky podporuje vyhodnocení, že důvodem nadměrné doby trvání je čekání.  Kliknutím na odkaz **čekáním** otevřete dokumentaci k interpretaci různých typů událostí.

    ![Tip k výkonu](media/tutorial-performance/6-perf-tip.png)

8.   Pro další analýzu můžete kliknutím na **stáhnout trasování** stáhnout trasování. Tato data můžete zobrazit pomocí [perfview](https://github.com/Microsoft/perfview#perfview-overview).

## <a name="use-logs-data-for-server"></a>Použití dat protokolů pro server
 Protokoly poskytuje rozšířený dotazovací jazyk, který umožňuje analyzovat všechna data shromážděná application insights. Můžete ho použít k provádění hloubkové analýzy dat o požadavcích a výkonu.

1. Návrat do panelu podrobností operace a klepněte na zobrazení ![ikony protokoly v](media/tutorial-performance/app-viewinlogs-icon.png)**protokolech (Analytics)**

2. Otevře se protokoly s dotazem pro každý pohled v panelu.  Tyto dotazy můžete spustit tak, jak jsou, nebo je upravit podle vlastních potřeb.  První dotaz zobrazí dobu trvání této operace v průběhu času.

    ![protokoly dotazu](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Identifikace pomalých operací klienta
Kromě identifikace procesů serveru, které je potřeba optimalizovat, dokáže Application Insights provádět analýzu i z pohledu klientských prohlížečů.  To vám může pomoct identifikovat potenciální vylepšení komponent klienta a dokonce i identifikovat problémy s různými prohlížeči nebo umístěními.

1. V části **Prozkoumat** vyberte možnost **Prozkoumat,** potom klikněte na **Výkon** **prohlížeče** nebo v části **Prozkoumat** vyberte možnost Výkon a přepněte na kartu **Prohlížeč** klepnutím na přepínací tlačítko server/prohlížeč v pravém horním rohu a otevřete souhrn výkonu prohlížeče. Ten poskytuje vizuální souhrn různých telemetrií vaší aplikace z pohledu prohlížeče.

    ![Souhrn prohlížeče](media/tutorial-performance/8-browser.png)

2. Vyberte na jednom z názvů operací, klepněte na modré tlačítko ukázky v pravém dolním rohu a vyberte operaci. Tím se zobrazí podrobnosti o transakcích mezi koncovými soubory a na pravé straně můžete zobrazit **vlastnosti zobrazení stránky**. To vám umožní zobrazit podrobnosti o klientovi požadující stránku včetně typu prohlížeče a jeho umístění. Tyto informace vám můžou pomoct s určením, jestli neexistují problémy s výkonem souvisejíc s konkrétními typy klientů.

    ![Zobrazení stránky](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Použití dat protokolů pro klienta
Stejně jako data shromážděná pro výkon serveru, Application Insights zpřístupňuje všechna data klienta pro hloubkovou analýzu pomocí protokolů.

1. Návrat do souhrnu ![prohlížeče a](media/tutorial-performance/app-viewinlogs-icon.png) klikněte na zobrazit ikonu Protokoly **v protokolech (Analytics)**

2. Otevře se protokoly s dotazem pro každý pohled v panelu. První dotaz zobrazí dobu trvání různých zobrazení stránek v průběhu času.

    ![Dotaz na protokoly](media/tutorial-performance/10-page-view-logs.png)

3.  Inteligentní diagnostika je funkce protokolů identifikuje jedinečné vzory v datech. Když kliknete na tečku inteligentní diagnostiky ve spojnicovém grafu, spustí se stejný dotaz bez záznamů, které anomálii způsobily. Podrobnosti o těchto záznamech se zobrazí v části dotazu s komentáři, takže můžete identifikovat vlastnosti zobrazení stránek, které způsobují nadměrnou dobu trvání.

    ![Protokoly s inteligentní diagnostikou](media/tutorial-performance/11-page-view-logs-dsmart.png)


## <a name="next-steps"></a>Další kroky
Nyní, když jste se naučili, jak identifikovat výjimky za běhu, přejděte k dalšímu kurzu, ve kterém zjistíte, jak jako reakci na chyby vytvářet upozornění.

> [!div class="nextstepaction"]
> [Upozornění na stav aplikace](../../azure-monitor/learn/tutorial-alert.md)
