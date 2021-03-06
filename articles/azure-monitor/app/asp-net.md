---
title: Nastavení analýzy webových aplikací pro ASP.NET pomocí Azure Application Insights | Dokumentace Microsoftu
description: Nakonfigurujte nástroje pro analýzu výkonu, dostupnosti a chování uživatelů pro váš ASP.NET web, hostovaný místně nebo v Azure.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 0843d6c04bf6fc9bab07207072990fb3fb8f1844
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665914"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Nastavení Application Insights pro web ASP.NET

Tímto postupem je možné konfigurovat webovou aplikaci ASP.NET tak, aby odesílala telemetrická data do služby [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Funguje pro aplikace ASP.NET, které jsou hostované buď na vašem vlastním serveru služby IIS v místním prostředí, nebo v cloudu. Můžete získat grafy a výkonný dotazovací jazyk, který vám pomůže porozumět výkonu vaší aplikace a způsobu, jakým ji uživatelé používají, a také automatické výstrahy v případě selhání nebo problémů s výkonem. Celá řada vývojářů považuje tyto funkce za skvělé (a ony jsou), v případě potřeby ale můžete telemetrická data také rozšířit a přizpůsobit.

Nastavení je otázkou několika kliknutí v sadě Visual Studio. Máte možnost vyhnout se placení poplatků, pokud objem telemetrických dat omezíte. Tato funkce umožňuje experimentovat a ladit nebo sledovat web s ne mnoha uživateli. Pokud se později rozhodnete, že chcete pokračovat a monitorovat svůj provozní server, můžete limit snadno zvýšit.

## <a name="prerequisites"></a>Požadavky
Pro přidání Application Insights na web ASP.NET potřebujete:

- Nainstalujte [Visual Studio 2019 pro Windows](https://www.visualstudio.com/downloads/) s následujícími úlohami:
    - ASP.NET a vývoj webových aplikací (Neodškrtávejte volitelné součásti)
    - Vývoj pro Azure

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="step-1-add-the-application-insights-sdk"></a><a name="ide"></a>Krok 1: Přidání sady Application Insights SDK

> [!IMPORTANT]
> Snímky obrazovky v tomto příkladu jsou založeny na Visual Studio 2017 verze 15.9.9 a novější. Prostředí pro přidání přehledů aplikací se liší v různých verzích sady Visual Studio i podle typu šablony ASP.NET. Starší verze mohou mít alternativní text, například "Konfigurovat application insights".

Klikněte pravým tlačítkem myši na název webové aplikace v Průzkumníku řešení a zvolte **Přidat** > **telemetrii Přehledy aplikací.**

![Snímek obrazovky Průzkumníka řešení se zvýrazněnou možností Konfigurovat Application Insights](./media/asp-net/add-telemetry-new.png)

(V závislosti na verzi Application Insights SDK se může zobrazit výzva k upgradu na nejnovější verzi SDK. Pokud se tato výzva zobrazí, vyberte **Aktualizovat sadu SDK**.)

![Snímek obrazovky: Je dostupná nová verze sady Microsoft Application Insights SDK. Zvýrazněná možnost Aktualizovat sadu SDK](./media/asp-net/0002-update-sdk.png)

Obrazovka Konfigurace Application Insights:

Vyberte **Možnost Začínáme**.

![Snímek obrazovky stránky registrace vaší aplikace v Application Insights](./media/asp-net/00004-start-free.png)

Pokud chcete nastavit skupinu prostředků nebo umístění, kde jsou vaše data uložená, klikněte na **Konfigurovat nastavení**. Skupiny prostředků slouží k řízení přístupu k datům. Pokud například máte několik aplikací, které tvoří součást stejného systému, můžete jejich data Application Insights ukládat do stejné skupiny prostředků.

 Vyberte **Zaregistrovat**.

![Snímek obrazovky stránky registrace vaší aplikace v Application Insights](./media/asp-net/00005-register-ed.png)

 Vyberte **Project** > **Manage NuGet Packages** > **Package source: nuget.org** > Confirm that you have the latest stable release of the Application Insights SDK.

 Telemetrie se bude posílat na web [Azure Portal](https://portal.azure.com), jak během ladění aplikace, tak po jejím publikování.
> [!NOTE]
> Pokud během ladění nechcete na portál odesílat telemetrická data, stačí přidat do aplikace sadu SDK Application Insights, ale nekonfigurovat prostředek na portálu. Během ladění se telemetrická data zobrazují v sadě Visual Studio. Později se můžete na tuto stránku konfigurace vrátit, nebo počkat až po nasazení aplikace a [přepnout na telemetrie za běhu](../../azure-monitor/app/monitor-performance-live-website-now.md).

## <a name="step-2-run-your-app"></a><a name="run"></a>Krok 2: Spuštění aplikace
Spusťte aplikaci pomocí F5. Otevřete různé stránky k vygenerování nějaké telemetrie.

V sadě Visual Studio se zobrazí počet zaprotokolovaných událostí.

![Snímek obrazovky sady Visual Studio. Během ladění se zobrazí tlačítko Application Insights.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Krok 3: Zobrazení vašich telemetrických dat
Telemetrii můžete zobrazit v sadě Visual Studio nebo na webovém portálu Application Insights. Hledáním v rámci telemetrických dat v sadě Visual Studio si můžete usnadnit ladění aplikace. Když bude váš systém v provozu, můžete monitorovat výkon a využití na webovém portálu. 

### <a name="see-your-telemetry-in-visual-studio"></a>Zobrazení telemetrických dat v sadě Visual Studio

Pokud chcete v sadě Visual Studio zobrazit data Application Insights:  Vyberte **Průzkumník** > řešení**připojené služby** > pravým tlačítkem myši klepněte na **položku Application Insights**a potom klepněte na příkaz Hledat živé **telemetrie**.

V okně Visual Studio Application Insights Search se zobrazí telemetrie vygenerovaná na straně serveru vaší aplikace. Experimentujte s filtry a klikněte na události, které chcete zobrazit podrobněji.

![Snímek obrazovky zobrazení Data z relace ladění v okně Application Insights.](./media/asp-net/55.png)

> [!Tip]
> Pokud se žádná data nezobrazí, ujistěte se, že je časový rozsah správný, a klikněte na ikonu Search.

[Další informace týkající se nástrojů Application Insights v sadě Visual Studio](../../azure-monitor/app/visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Zobrazení telemetrických dat na webovém portálu

Telemetrická data můžete zobrazit také na webovém portálu Application Insights (pokud jste se nerozhodli nainstalovat pouze sadu SDK). Portál obsahuje více grafů, analytických nástrojů a zobrazení nad několika součástmi než sada Visual Studio. Portál poskytuje také výstrahy.

Otevřete prostředek Application Insights. Buď se přihlaste na [Azure Portal](https://portal.azure.com/) a najděte ho tady, nebo vyberte **Průzkumník řešení** > **Připojené služby** > klikněte pravým tlačítkem na **Application Insights** > **Otevřít portál Application Insights** a přejděte na něj.

Portál otevře zobrazení telemetrie z vaší aplikace.

![Snímek obrazovky stránky s přehledem Application Insights](./media/asp-net/007.png)

Po kliknutí na kteroukoli dlaždici nebo graf se zobrazí podrobnější údaje.

## <a name="step-4-publish-your-app"></a>Krok 4: Publikování aplikace
Publikování aplikace na serveru služby IIS nebo do Azure. Sledujte [Živé vysílání metrik](../../azure-monitor/app/metrics-explorer.md#live-metrics-stream) a ověřte, zda vše běží hladce.

Vaše telemetrie se hromadí na portálu Application Insights, kde můžete sledovat metriky, prohledávat telemetrii. Výkonný [dotazovací jazyk Kusto](/azure/kusto/query/) můžete také použít k analýze využití a výkonu nebo k vyhledání konkrétních událostí.

Můžete také dále analyzovat telemetrii v sadě [Visual Studio](../../azure-monitor/app/visual-studio.md) pomocí nástrojů jako vyhledávání diagnostiky a [Trendy](../../azure-monitor/app/visual-studio-trends.md).

> [!NOTE]
> Pokud vaše aplikace odesílá dostatek telemetrie k dosažení [limitů omezení](../../azure-monitor/app/pricing.md#limits-summary), zapne se automatické [vzorkování](../../azure-monitor/app/sampling.md). Vzorkování snižuje množství telemetrie odesílané z vaší aplikace při zachování korelovaných dat k diagnostickým účelům.
>
>

## <a name="youre-all-set"></a><a name="land"></a>Jste připraveni.

Blahopřejeme! Nainstalovali jste do aplikace balíček Application Insights a nakonfigurovali jste pro ni odesílání telemetrických dat do služby Application Insights v Azure.

Prostředek Azure, který přijímá telemetrická data aplikace, je určen *instrumentačním klíčem*. Tento klíč najdete v souboru ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Upgrade na budoucí verze sady SDK
Pokud chcete upgradovat na [novou verzi sady SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), otevřete **Správce balíčků NuGet** a filtrujte nainstalované balíčky. Vyberte **microsoft.applicationinsights.web**a zvolte **Upgrade**.

Pokud jste provedli jakékoli úpravy souboru ApplicationInsights.config, před upgradem si uložte jeho kopii. Potom slučte změny do nové verze.

## <a name="video"></a>Video

* Externí podrobné video o [konfiguraci Application Insights pomocí aplikace .NET od začátku](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="next-steps"></a>Další kroky

K dispozici jsou i alternativní témata, na která se v případě zájmu můžete podívat:

* [Instrumentace webové aplikace za běhu](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>Další telemetrická data

* **[Údaje o prohlížečích a o načítání stránek](../../azure-monitor/app/javascript.md)** – Vložte do svých webových stránek fragment kódu.
* **[Dosažení podrobnějšího monitorování závislostí a výjimek](../../azure-monitor/app/monitor-performance-live-website-now.md)** – Nainstalujte si na server Monitorování stavu.
* **[Kód vlastní události](../../azure-monitor/app/api-custom-events-metrics.md)** počítat, čas nebo měřit akce uživatele.
* **[Získání dat protokolu](../../azure-monitor/app/asp-net-trace-logs.md)** – Zjišťujte korelaci dat protokolu s telemetrickými daty.

### <a name="analysis"></a>Analýza

* **[Práce s Application Insights v sadě Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Zahrnuje informace o ladění pomocí telemetrie, diagnostických hledáních a podrobném procházení kódem.
* **[Analytics](../../azure-monitor/log-query/get-started-portal.md)** – Výkonný dotazovací jazyk.

### <a name="alerts"></a>Výstrahy

* [Testy dostupnosti:](../../azure-monitor/app/monitor-web-app-availability.md) Vytvářejte testy, abyste ověřili viditelnost svého webu na internetu.
* [Inteligentní diagnostika:](../../azure-monitor/app/proactive-diagnostics.md) Tyto testy se spouštějí automaticky, takže je nemusíte nijak nastavovat. Upozorní vás, pokud má aplikace nezvykle velký podíl neúspěšných požadavků.
* [Upozornění na metriky](../../azure-monitor/app/alerts.md): Nastavte výstrahy, které vás upozorní, pokud metrika překročí prahovou hodnotu. Upozornění můžete nastavit u vlastních metrik, které v aplikaci naprogramujete.

### <a name="automation"></a>Automatizace

* [Automatizace vytvoření prostředku Application Insights](../../azure-monitor/app/powershell.md)
