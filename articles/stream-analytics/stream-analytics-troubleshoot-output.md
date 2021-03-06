---
title: Poradce při potížích s výstupy Azure Stream Analytics
description: Tento článek popisuje techniky řešení potíží s výstupními připojeními v azure stream analytics úlohy.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 305632a0faa1eb7e217e86d36c5159e557df7aaf
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409255"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Poradce při potížích s výstupy Azure Stream Analytics

Tento článek popisuje běžné problémy s výstupními připojeními Azure Stream Analytics, jak řešit problémy s výstupem a jak problémy opravit. Mnoho kroků řešení potíží vyžaduje, aby byly pro úlohu Stream Analytics povoleny diagnostické protokoly. Pokud nemáte povolené diagnostické protokoly, [přečtěte si článek Poradce při potížích s Azure Stream Analytics pomocí diagnostických protokolů](stream-analytics-job-diagnostic-logs.md).

## <a name="output-not-produced-by-job"></a>Výstup nevytvořený úlohou

1.  Ověřte připojení k výstupům pomocí tlačítka **Testovat připojení** pro každý výstup.

2.  Podívejte se na [**metriky monitorování**](stream-analytics-monitoring.md) na kartě **Monitor.** Vzhledem k tomu, že hodnoty jsou agregovány, metriky jsou zpožděny o několik minut.
   * Pokud vstupní události jsou větší než 0, úloha je schopen číst vstupní data. Pokud vstupní události nejsou větší než 0, pak je problém se vstupem úlohy. Informace o řešení potíží se vstupními připojeními najdete v [tématu Poradce při potížích](stream-analytics-troubleshoot-input.md) se vstupním připojením.
   * Pokud jsou chyby převodu dat větší než 0 a lezení, najdete v tématu [Chyby dat Azure Stream Analytics](data-errors.md) pro podrobné informace o chybách převodu dat.
   * Pokud jsou chyby modulu runtime větší než 0, úloha může přijímat data, ale při zpracování dotazu generuje chyby. Chcete-li najít chyby, přejděte do [protokolů auditu](../azure-resource-manager/management/view-activity-logs.md) a filtrujte stav *Selhání.*
   * Pokud InputEvents je větší než 0 a OutputEvents se rovná 0, je splněna jedna z následujících:
      * Zpracování dotazu mělo za výsledek nulu výstupních událostí.
      * Události nebo pole mohou být poškozeny, výsledkem je nulový výstup po zpracování dotazu.
      * Úloha nemohla vysunout data do výstupního jímky z důvodů připojení nebo ověřování.

   Ve všech výše uvedených chybových případech zprávy protokolu operací vysvětlují další podrobnosti (včetně toho, co se děje), s výjimkou případů, kdy logika dotazu odfiltrovala všechny události. Pokud zpracování více událostí generuje chyby, chyby jsou agregovány každých 10 minut.

## <a name="job-output-is-delayed"></a>Výstup úlohy je zpožděný

### <a name="first-output-is-delayed"></a>První výstup je zpožděn

Po spuštění úlohy Stream Analytics se načtou vstupní události, ale v některých případech může dojít ke zpoždění generování výstupu.

Velké časové hodnoty v prvcích časového dotazu mohou přispět ke zpoždění výstupu. Chcete-li vytvořit správný výstup v rozsáhlých časových oknech, úloha streamování se spustí čtením dat z nejnovějšího možného času (až před sedmi dny) k vyplnění časového okna. Během této doby žádný výstup je vyroben, dokud catch-up čtení nevyřízené vstupní události je kompletní. Tento problém může povrch při upgradu systému úlohy streamování, tedy restartování úlohy. Takové upgrady se obvykle vyskytují jednou za pár měsíců.

Při navrhování dotazu Stream Analytics se proto můžete uvážit podle vlastního uvážení. Pokud použijete velké časové okno (více než několik hodin, až sedm dní) pro časové prvky v syntaxi dotazu úlohy, může to vést ke zpoždění na prvním výstupu při spuštění nebo restartování úlohy.  

Jedním z omezení pro tento druh první zpoždění výstupu je použití techniky paralelizace dotazu (rozdělení dat) nebo přidat další jednotky streamování ke zlepšení propustnost, dokud úloha dožene.  Další informace najdete [v tématu Důležité informace při vytváření úloh Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)

Tyto faktory ovlivňují aktuálnost prvního generovaného výstupu:

1. Použití okenních agregátů (GROUP BY of Tumbling, Hopping a Sliding windows)
   - Pro omílání nebo přeskakování okno agregáty, výsledky jsou generovány na konci časového rámce okna.
   - Pro posuvné okno jsou výsledky generovány, když událost vstoupí nebo ukončí posuvné okno.
   - Pokud plánujete použít velké velikosti okna (> 1 hodinu), je nejlepší zvolit hopping nebo posuvné okno, takže můžete vidět výstup častěji.

2. Použití časových spojení (SPOJENÍ s DATEDIFF)
   - Shody jsou generovány, jakmile dorazí obě strany odpovídajících událostí.
   - Data, která postrádá shodu (LEFT OUTER JOIN) je generována na konci okna DATEDIFF s ohledem na každou událost na levé straně.

3. Použití časových analytických funkcí (ISFIRST, LAST a MAS s LIMIT DURATION)
   - Pro analytické funkce je výstup generován pro každou událost, není žádné zpoždění.

### <a name="output-falls-behind"></a>Výstup zaostává

Při normálním provozu úlohy, pokud zjistíte, že výstup úlohy zaostává (delší a delší latence), můžete určit hlavní příčiny kontrolou těchto faktorů:
- Zda je dolní jímka omezena
- Zda je zdroj proti proudu omezen
- Zda je logika zpracování v dotazu náročná na výpočetní prostředky

Pokud chcete zobrazit tyto podrobnosti, vyberte na webu Azure Portal úlohu streamování a vyberte **diagram úlohy**. Pro každý vstup je metrika nevyřízených položek oddílu události. Pokud se metrika událostí nevyřízených položek neustále zvyšuje, je indikátorem, že systémové prostředky jsou omezené. Potenciálně, že je z důvodu omezení jímky výstupu nebo vysoké procesoru. Další informace o použití diagramu úloh naleznete v [tématu Ladění řízené daty pomocí diagramu úloh](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Upozornění na narušení zabezpečení klíče s výstupem databáze Azure SQL

Když nakonfigurujete databázi Azure SQL jako výstup pro úlohu Stream Analytics, hromadně vloží záznamy do cílové tabulky. Obecně platí, že Azure stream analytics zaručuje [alespoň jednou doručení](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) do výstupníjím jímky, jeden může ještě dosáhnout přesně jednou [doručení]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) do výstupu SQL, když sql tabulka má definované jedinečné omezení.

Jakmile jsou v tabulce SQL nastavena jedinečná omezení klíče a do tabulky SQL se vkládají duplicitní záznamy, Azure Stream Analytics odebere duplicitní záznam. Rozdělí data na dávky a rekurzivně vloží dávky, dokud nebude nalezen jeden duplicitní záznam. Pokud úloha streamování má značný počet duplicitních řádků, tento proces rozdělení a vložení musí ignorovat duplikáty jeden po druhém, což je méně efektivní a časově náročné. Pokud se v protokolu aktivit během poslední hodiny zobrazí více zpráv upozornění na porušení zásad klíče, je pravděpodobné, že výstup SQL zpomaluje celou úlohu.

Chcete-li tento problém vyřešit, měli byste [nakonfigurovat index,]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) který je příčinou narušení klíče povolením možnosti IGNORE_DUP_KEY. Povolení této možnosti umožňuje duplicitní hodnoty, které mají být ignorovány SQL během hromadné vložení a SQL Azure jednoduše vytvoří varovnou zprávu namísto chyby. Azure Stream Analytics už nevytváří chyby narušení primárního klíče.

Všimněte si následující chod pozorování při konfiguraci IGNORE_DUP_KEY pro několik typů indexů:

* Nelze nastavit IGNORE_DUP_KEY na primární klíč nebo jedinečné omezení, které používá INDEX ALTER, je třeba přetáhnout a znovu vytvořit index.  
* Můžete nastavit možnost IGNORE_DUP_KEY pomocí ALTER INDEX pro jedinečný index, který se liší od omezení PRIMÁRNÍ KLÍČ/JEDINEČNÝ a vytvořený pomocí definice VYTVOŘIT INDEX nebo INDEX.  

* IGNORE_DUP_KEY se nevztahuje na indexy úložiště sloupců, protože nelze vynutit jedinečnost na tyto indexy.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Názvy sloupců jsou malá písmena podle Azure Stream Analytics
Při použití původní úrovně kompatibility (1.0) se Azure Stream Analytics používá ke změně názvů sloupců na malá písmena. Toto chování bylo opraveno v pozdějších úrovních kompatibility. Abychom případ zachovali, doporučujeme zákazníkům přejít na úroveň kompatibility 1.1 a novější. Další informace o [úrovni kompatibility pro úlohy Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)najdete.

## <a name="get-help"></a>Podpora

Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
