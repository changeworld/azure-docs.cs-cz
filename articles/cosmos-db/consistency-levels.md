---
title: Úrovně konzistence v Azure Cosmos DB
description: Azure Cosmos DB má pět úrovní konzistence, které pomáhají vyvážit případné konzistence, dostupnost a latence kompromisy.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e5966f142ece32f148c56edb5b0ef5dfd88603aa
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380082"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Úrovně konzistence v Azure Cosmos DB

Distribuované databáze, které spoléhají na replikaci pro vysokou dostupnost, nízkou latencí nebo obojí, aby základní kompromis mezi konzistence čtení vs dostupnost, latence a propustnost. Většina komerčně dostupných distribuovaných databází žádá vývojáře, aby si vybrali mezi dvěma modely extrémní konzistence: *silnou* konzistenci a *konečnou* konzistenci. Linearizovatelnost modelu silné konzistence je zlatým standardem programovatelnosti dat. Ale přidá cenu vyšší latence zápisu (v ustáleném stavu) a sníženou dostupnost (během selhání). Na druhou stranu, konečná konzistence nabízí vyšší dostupnost a lepší výkon, ale ztěžuje programování aplikací.

Azure Cosmos DB přistupuje k konzistenci dat jako spektrum možností namísto dvou extrémů. Vývojáři mohou tyto možnosti použít k přesným volbám a podrobným kompromisům s ohledem na vysokou dostupnost a výkon.

S Azure Cosmos DB si vývojáři můžou vybrat z pěti dobře definovaných úrovní konzistence na spektru konzistence. Tyto úrovně zahrnují *silnou*, *ohraničenou neaktuálnost*, *relaci*, *konzistentní předponu*a *konečnou* konzistenci. Úrovně jsou dobře definované a intuitivní a lze je použít pro konkrétní reálné scénáře. Každá úroveň poskytuje [přístupové a výkonnostní kompromisy](consistency-levels-tradeoffs.md) a jsou podporovány seznamy SLA. Následující obrázek znázorňuje různé úrovně konzistence jako spektrum.

![Konzistentnost jako spektrum](./media/consistency-levels/five-consistency-levels.png)

Úrovně konzistence jsou oblasti agnostik a jsou zaručeny pro všechny operace bez ohledu na oblast, ze které jsou obsluhovány čtení a zápisy, počet oblastí přidružených k účtu Azure Cosmos nebo zda je váš účet nakonfigurovaný s jedním nebo více oblastí zápisu.

## <a name="scope-of-the-read-consistency"></a>Rozsah konzistence čtení

Konzistence čtení platí pro jednu operaci čtení vymezenou v rámci logického oddílu. Operaci čtení může vydat vzdálený klient nebo uložená procedura.

## <a name="configure-the-default-consistency-level"></a>Konfigurace výchozí úrovně konzistence

Výchozí úroveň konzistence můžete kdykoli nakonfigurovat na svém účtu Azure Cosmos. Výchozí úroveň konzistence nakonfigurovaná ve vašem účtu platí pro všechny databáze a kontejnery Azure Cosmos v rámci tohoto účtu. Všechna čtení a dotazy vydané proti kontejneru nebo databázi používají zadanou úroveň konzistence ve výchozím nastavení. Další informace naleznete v [tématu konfigurace výchozí úrovně konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Záruky spojené s úrovněmi konzistence

Komplexní smlouvy SLA poskytované službou Azure Cosmos DB zaručují, že 100 procent požadavků na čtení splňuje záruku konzistence pro libovolnou úroveň konzistence, kterou zvolíte. Požadavek na čtení splňuje požadavky s požadavek sla konzistence, pokud jsou splněny všechny záruky konzistence spojené s úrovní konzistence. Přesné definice pěti úrovní konzistence v Azure Cosmos DB pomocí jazyka specifikace TLA+ jsou k dispozici v úložišti [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub.

Zde je popsána sémantika pěti úrovní konzistence:

- **Silná**: Silná konzistence nabízí záruku linearity. Linearizovatelnost odkazuje na obsluhu požadavků současně. Čtení je zaručeno, že vrátí nejnovější potvrzené verze položky. Klient nikdy neuvidí nepotvrzené nebo částečné zápisu. Uživatelé jsou vždy zaručeno, že číst nejnovější potvrzené zápisu.

  Následující obrázek znázorňuje silnou konzistenci s hudebními notami. Po zápisu dat do oblasti "Západní USA 2" při čtení dat z jiných oblastí získáte nejnovější hodnotu:

  ![video](media/consistency-levels/strong-consistency.gif)

- **Ohraničená neaktuálnost**: Čtení jsou zaručena dodržet záruku konzistentní předpony. Čtení může zaostávat za zápisy maximálně *"K"* verze (to znamená"aktualizace") položky nebo *"T"* časový interval. Jinými slovy, když zvolíte ohraničenou neaktuálnost, "neaktuálnost" lze nakonfigurovat dvěma způsoby:

- Počet verzí (*K*) položky
- Časový interval (*T),* o který může čtení zaostávat za zápisy

Ohraničená neaktuálnost nabízí celkové globální pořadí mimo "okno neaktuálnosti". Když klient provádí operace čtení v rámci oblasti, která přijímá zápisy, záruky poskytované ohraničené konzistence neaktuálnosti jsou shodné s těmito zárukami silné konzistence.

Uvnitř okna neaktuálnost ohraničené neaktuálnost poskytuje následující záruky konzistence:

- Konzistence pro klienty ve stejné oblasti pro jeden hlavní účet = Silný
- Konzistence pro klienty v různých oblastech pro jeden hlavní účet = konzistentní předpona
- Konzistence pro klienty zapisované do jedné oblasti pro vícehlavních účtů = konzistentní předpona
- Konzistence pro klienty, kteří píší do různých oblastí pro vícehlavních účtů = Eventuální

  Ohraničená neaktuálnost je často vybírána globálně distribuovanými aplikacemi, které očekávají nízkou latenci zápisu, ale vyžadují úplnou globální záruku objednávky. Ohraničená neaktuálnost je skvělá pro aplikace, které obsahují skupinovou spolupráci a sdílení, burzovní burzovní, publikovat -subscribe / queueing atd. Následující obrázek znázorňuje ohraničenou konzistenci neaktuálnosti s hudebními notami. Po zápisu dat do oblasti "Západní USA 2" budou oblasti "Východní USA 2" a "Austrálie – východ" číst zapsanou hodnotu na základě nakonfigurované maximální prodlevy nebo maximálních operací:

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **Relace**: V rámci jedné relace klienta čtení jsou zaručeny ctít konzistentní prefix, monotónní čtení, monotónní zápisy, čtení-vaše-zápisy a zápis-následuje-čte záruky. To předpokládá jednu relaci "zapisovač" nebo sdílení tokenu relace pro více zapisovačů.

Klientům mimo relaci provádějící zápisy se zobrazí následující záruky:

- Konzistence pro klienty ve stejné oblasti pro jeden hlavní účet = konzistentní předpona
- Konzistence pro klienty v různých oblastech pro jeden hlavní účet = konzistentní předpona
- Konzistence pro klienty zapisované do jedné oblasti pro vícehlavních účtů = konzistentní předpona
- Konzistence pro klienty zapisování do více oblastí pro vícehlavních účtů = Eventuální

  Konzistence relace je široce používaná úroveň konzistence pro jednotlivé oblasti i globálně distribuované aplikace. Poskytuje latence zápisu, dostupnost a propustnost pro čtení srovnatelné s konečnou konzistencí, ale také poskytuje záruky konzistence, které vyhovují potřebám aplikací zapsaných pro provoz v kontextu uživatele. Následující obrázek znázorňuje konzistenci relace s hudebními notami. "West US 2 spisovatel" a "West US 2 čtenář" používají stejnou relaci (Session A), takže oba číst stejná data ve stejnou dobu. Vzhledem k tomu, že oblast "Austrálie – východ" používá relaci B, takže přijímá data později, ale ve stejném pořadí jako zápisy.

  ![video](media/consistency-levels/session-consistency.gif)

- **Konzistentní předpona**: Aktualizace, které jsou vráceny obsahují některé předpony všech aktualizací, bez mezer. Konzistentní úroveň konzistence předpony zaručuje, že čtení nikdy vidět zápisy mimo pořadí.

Pokud se zápisy provedly v pořadí `A, B, C`, klientovi se zobrazí buď `A`, `A,B`, nebo `A,B,C`, ale nikdy položka mimo pořadí, jako je `A,C` nebo `B,A,C`. Konzistentní předpona poskytuje latence zápisu, dostupnost a propustnost pro čtení srovnatelné s konečnou konzistencí, ale také poskytuje záruky objednávky, které vyhovují potřebám scénářů, kde je důležité pořadí. 

Níže jsou uvedeny záruky konzistence pro konzistentní předponu:

- Konzistence pro klienty ve stejné oblasti pro jeden hlavní účet = konzistentní předpona
- Konzistence pro klienty v různých oblastech pro jeden hlavní účet = konzistentní předpona
- Konzistence pro klienty zapisované do jedné oblasti pro vícehlavních účtů = konzistentní předpona
- Konzistence pro klienty zapisování do více oblastí pro vícehlavních účtů = Eventuální

Následující obrázek znázorňuje konzistenci konzistence předpony konzistence s hudebními notami. Ve všech oblastech čtení nikdy vidět mimo pořadí píše:

  ![video](media/consistency-levels/consistent-prefix.gif)

- **Eventual**: Neexistuje žádná záruka objednávání pro čtení. V nepřítomnosti žádné další zápisy repliky nakonec sbíhají.  
Konečná konzistence je nejslabší forma konzistence, protože klient může číst hodnoty, které jsou starší než ty, které četl dříve. Případná konzistence je ideální tam, kde aplikace nevyžaduje žádné záruky objednávání. Příklady zahrnují počet retweetů, označení To se mi líbí nebo komentářů bez vláken. Následující obrázek znázorňuje konečnou konzistenci s notami.

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Dodatečné čtení

Další informace o konceptech konzistence naleznete v následujících článcích:

- [Specifikace TLA+ na vysoké úrovni pro pět úrovní konzistence nabízených službou Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikovaná konzistence dat vysvětlená prostřednictvím baseballu (video) dougem Terrym](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikovaná konzistence dat vysvětlená prostřednictvím baseballu (whitepaper) od Douga Terryho](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Záruky relace pro slabě konzistentní replikovaná data](https://dl.acm.org/citation.cfm?id=383631)
- [Kompromisy konzistence v návrhu moderních distribuovaných databázových systémů: SZP je jen část příběhu](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Pravděpodobnostní ohraničená zatuchlost (PBS) pro praktická částečná kvora](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Nakonec konzistentní - Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Další kroky

Další informace o úrovních konzistence v Azure Cosmos DB najdete v následujících článcích:

- [Zvolte správnou úroveň konzistence pro vaši aplikaci](consistency-levels-choosing.md)
- [Úrovně konzistence mezi api DB Služby Azure Cosmos](consistency-levels-across-apis.md)
- [Kompromisy dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
- [Konfigurace výchozí úrovně konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Přepsání výchozí úrovně konzistence](how-to-manage-consistency.md#override-the-default-consistency-level)
