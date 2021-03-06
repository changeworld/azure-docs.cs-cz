---
title: Odkaz na rozhraní API agenta Azure Application Insights
description: Odkaz na rozhraní API agenta Application Insights. Get-ApplicationInsightsMonitoringConfig. Sledujte výkon webových stránek bez opětovného nasazení webu. Funguje s ASP.NET webových aplikací hostovaných místně, ve virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 93ab8f613e5634b2eabe7c02189e223d3dfbb0a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671269"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Rozhraní API agenta Application Insights: Get-ApplicationInsightsMonitoringConfig

Tento článek popisuje rutinu, která je členem [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Získá konfigurační soubor a vytiskne hodnoty do konzoly.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci prostředí PowerShell s oprávněními správce.

## <a name="examples"></a>Příklady

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parametry

Nejsou vyžadovány žádné parametry.

## <a name="output"></a>Výstup


#### <a name="example-output-from-reading-the-config-file"></a>Příklad výstupu ze čtení konfiguračního souboru

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Další kroky

  Zobrazení telemetrických dat:
 - [Prozkoumejte metriky](../../azure-monitor/app/metrics-explorer.md) a sledujte výkon a využití.
- [Hledat události a protokoly](../../azure-monitor/app/diagnostic-search.md) diagnostikovat problémy.
- Pro pokročilejší dotazy používejte [analýzy.](../../azure-monitor/app/analytics.md)
- [Vytvořte řídicí panely](../../azure-monitor/app/overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
- [Přidejte telemetrickou službu webového klienta,](../../azure-monitor/app/javascript.md) chcete-li zobrazit výjimky z kódu webové stránky a povolit volání trasování.
- [Přidejte do kódu sadou SDK application insights,](../../azure-monitor/app/asp-net.md) abyste mohli vkládat volání trasování a protokolování.
 
 S agentem Application Insights toho zvládnete víc:
 - Použijte náš průvodce [k řešení potíží s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
 - Proveďte změny konfigurace pomocí rutiny [Nastavit konfiguraci.](status-monitor-v2-api-set-config.md)
