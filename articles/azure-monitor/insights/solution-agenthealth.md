---
title: Řešení stavu agenta ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Tento článek je určen k pochopení, jak pomocí tohoto řešení sledovat stav agentů, kteří vykazují přímo log Analytics nebo System Center Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 7093e20473b799a3f05ddf30803721636732241e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663251"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Řešení stavu agenta ve službě Azure Monitor
Řešení stavu agenta v Azure vám pomůže pochopit, pro všechny agenty, kteří se hlásí přímo do pracovního prostoru Log Analytics v Azure Monitoru nebo ve skupině pro správu System Center Operations Manager připojené k Azure Monitoru, které nereagují a předkládání provozních údajů.  Můžete také sledovat, kolik agentů je nasazených a jak jsou geograficky distribuováni, a provádět další dotazy, abyste si udrželi přehled o distribuci agentů nasazených v Azure, dalších cloudových prostředích nebo místně.    

## <a name="prerequisites"></a>Požadavky
Před nasazením tohoto řešení potvrďte, že jste aktuálně podporovali [agenty systému Windows,](../../log-analytics/log-analytics-windows-agent.md) kteří se hlásí do pracovního prostoru Log Analytics nebo se hlásí do [skupiny správy nástroje Operations Manager](../../azure-monitor/platform/om-agents.md) integrované s vaším pracovním prostorem.

## <a name="solution-components"></a>Součásti řešení
Toto řešení se skládá z následujících prostředků, které se přidají do vašeho pracovního prostoru, a přímo připojených agentů nebo skupiny pro správu připojené k nástroji Operations Manager.

### <a name="management-packs"></a>Sady Management Pack
Pokud je vaše skupina pro správu Operations Manager centra systému připojena k pracovnímu prostoru Analýzy protokolů, jsou v nástroji Operations Manager nainstalovány následující sady Management Pack.  Tyto sady Management Pack se po přidání tohoto řešení nainstalují také na přímo připojené počítače s Windows. U těchto sad Management Pack není nutné nic konfigurovat ani spravovat.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Konfigurace
Přidejte řešení stavu agenta do pracovního prostoru Log Analytics pomocí procesu popsaného v [části Přidat řešení](solutions.md). Není nutná žádná další konfigurace.


## <a name="data-collection"></a>Shromažďování dat
### <a name="supported-agents"></a>Podporovaní agenti
Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podporuje se | Popis |
| --- | --- | --- |
| Agenti systému Windows | Ano | Události prezenčního signálu se shromažďují z přímých agentů systému Windows.|
| Skupina pro správu nástroje System Center Operations Manager | Ano | Události prezenčního signálu jsou shromažďovány od agentů, kteří se hlásí do skupiny pro správu každých 60 sekund a pak jsou předávány do Azure Monitoru. Přímé připojení agentů Operations Manageru k Azure Monitoru není vyžadováno. Data událostí prezenčního signálu se předávají ze skupiny pro správu do pracovního prostoru Log Analytics.|

## <a name="using-the-solution"></a>Použití řešení
Když přidáte řešení do pracovního prostoru Log Analytics, dlaždice **Stav agenta** se přidá na řídicí panel. Tato dlaždice ukazuje celkový počet agentů a počet nereagujících agentů za posledních 24 hodin.<br><br> ![Dlaždice řešení Agent Health na řídicím panelu](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Kliknutím na dlaždici **Agent Health** otevřete řídicí panel **Agent Health**.  Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec obsahuje seznam prvních deseti událostí podle počtu, které splňují kritéria sloupce pro zadaný časový rozsah. Výběrem možnosti **Zobrazit všechno** v pravé dolní části každého sloupce nebo kliknutím na záhlaví sloupce můžete spustit prohledávání protokolu, které vám poskytne úplný seznam.

| Sloupec | Popis |
|--------|-------------|
| Počet agentů v průběhu času | Trend vývoje počtu linuxových agentů a agentů systému Windows za posledních sedm dnů.|
| Počet nereagujících agentů | Seznam agentů, kteří za posledních 24 hodin neodeslali prezenční signál.|
| Distribuce podle typu operačního systému | Rozdělení agentů systému Windows a linuxových agentů ve vašem prostředí.|
| Distribuce podle verze agenta | Rozdělení různých verzí agentů nainstalovaných ve vašem prostředí a jejich počet.|
| Distribuce podle kategorie agenta | Rozdělení různých kategorií agentů, kteří odesílají události prezenčního signálu: přímí agenti, agenti nástroje Operations Manager nebo server pro správu nástroje Operations Manager.|
| Distribuce podle skupiny pro správu | Oddíl různých skupin správy nástroje Operations Manager ve vašem prostředí.|
| Geografické umístění agentů | Oddíl různých zemí nebo oblastí, kde máte agenty, a celkový počet agentů, kteří byli nainstalováni v každé zemi nebo oblasti.|
| Počet nainstalovaných bran | Počet serverů, které mají nainstalovanou bránu Log Analytics a seznam těchto serverů.|

![Ukázka řídicího panelu řešení Agent Health](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Záznamy protokolu Azure Monitor
Řešení vytvoří jeden typ záznamu v pracovním prostoru Log Analytics.  

### <a name="heartbeat-records"></a>Záznamy prezenčního signálu
Vytvoří se záznam typu **Prezenční signál**.  Vlastnosti záznamů tohoto typu uvádí následující tabulka.  

| Vlastnost | Popis |
| --- | --- |
| `Type` | *Heartbeat* (Prezenční signál)|
| `Category` | Hodnota je *Direct Agent* (Přímý agent), *SCOM Agent* (Agent nástroje SCOM) nebo *SCOM Management Server* (Server pro správu nástroje SCOM).|
| `Computer` | Název počítače.|
| `OSType` | Operační systém Windows nebo Linux.|
| `OSMajorVersion` | Hlavní verze operačního systému.|
| `OSMinorVersion` | Podverze operačního systému.|
| `Version` | Agent analýzy protokolů nebo verze agenta nástroje Operations Manager.|
| `SCAgentChannel` | Hodnota je *Direct* (Přímý) nebo *SCManagementServer* (Server pro správu nástroje SCOM).|
| `IsGatewayInstalled` | Pokud je nainstalována brána Log Analytics, hodnota je *true*, jinak hodnota je *false*.|
| `ComputerIP` | Veřejná IP adresa počítače. Na virtuálních počítačích Azure se zobrazí veřejná IP adresa, pokud je dostupná. Pro virtuální počítače pomocí privátních IP adres se zobrazí adresa Azure SNAT (nikoli privátní IP adresa). |
| `RemoteIPCountry` | Zeměpisné umístění, kde je počítač nasazený.|
| `ManagementGroupName` | Název skupiny pro správu nástroje Operations Manager.|
| `SourceComputerId` | Jedinečné ID počítače.|
| `RemoteIPLongitude` | Zeměpisná délka zeměpisného umístění počítače.|
| `RemoteIPLatitude` | Zeměpisná šířka zeměpisného umístění počítače.|

Každý agent, který se hlásí na server pro správu nástroje Operations Manager, odešle dva prezenční signály a hodnota vlastnosti SCAgentChannel bude zahrnovat **server Direct** i **SCManagementServer** v závislosti na zdrojích dat a řešeních monitorování, které jste povolili v předplatném. Pokud si najdete, data z řešení se odesílají buď přímo ze serveru pro správu Operations Manager u Azure Monitoru, nebo z důvodu objemu dat shromážděných na agenta, se odesílají přímo z agenta do Azure Monitoru. U událostí prezenčního signálu, které mají hodnotu **SCManagementServer**, je hodnota ComputerIP IP adresou serveru pro správu, protože ten data ve skutečnosti odesílá.  U prezenčních signálů, které mají vlastnost SCAgentChannel nastavenou na hodnotu **Direct**, to je veřejná IP adresa agenta.  

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech
V následující tabulce jsou uvedeny ukázky prohledávání protokolu pro záznamy shromážděné tímto řešením.

| Dotaz | Popis |
|:---|:---|
| Heartbeat &#124; distinct Computer |Celkový počet agentů |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Počet nereagujících agentů za posledních 24 hodin |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Počet nereagujících agentů za posledních 15 minut |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Online počítače (za posledních 24 hodin) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Celkový počet agentů, kteří byli v průběhu posledních 30 minut offline (za posledních 24 hodin) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Získání trendu vývoje počtu agentů v průběhu času podle typu operačního systému|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribuce podle typu operačního systému |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribuce podle verze agenta |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribuce podle kategorie agenta |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribuce podle skupiny pro správu |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geografické umístění agentů |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Počet nainstalovaných bran analýzy protokolů |




## <a name="next-steps"></a>Další kroky

* Další informace o [výstrahách v Azure Monitoru](../platform/alerts-overview.md) podrobnosti o generování výstrah z dotazů protokolu. 
