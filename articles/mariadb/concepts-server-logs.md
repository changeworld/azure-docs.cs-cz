---
title: Pomalé protokoly dotazů – databáze Azure pro MariaDB
description: Popisuje protokoly dostupné v Azure Database pro MariaDB a dostupné parametry pro povolení různých úrovní protokolování.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: ffd4ab463080001dbab5b0ed9ece69c4b5f91382
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272079"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Pomalé protokoly dotazů v Azure Database pro MariaDB
V Azure Database pro MariaDB, pomalý protokol dotazů je k dispozici uživatelům. Přístup k transakčnímu protokolu není podporován. Pomalý protokol dotazů lze použít k identifikaci kritických bodů výkonu pro řešení potíží.

Další informace o protokolu pomalých dotazů naleznete v dokumentaci MariaDB pro [protokol pomalých dotazů](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="configure-slow-query-logging"></a>Konfigurace pomalého protokolování dotazů
Ve výchozím nastavení je pomalý protokol dotazů zakázán. Chcete-li jej `slow_query_log` povolit, nastavte možnost ZAPNUTO. To lze povolit pomocí portálu Azure nebo azure cli. 

Mezi další parametry, které můžete upravit, patří:

- **long_query_time**: Pokud dotaz trvá déle než long_query_time (v sekundách), je dotaz protokolován. Výchozí hodnota je 10 sekund.
- **log_slow_admin_statements**: pokud ON obsahuje správní prohlášení, jako jsou ALTER_TABLE a ANALYZE_TABLE, do výkazů zapsaná slow_query_log.
- **log_queries_not_using_indexes**: určuje, zda dotazy, které nepoužívají indexy jsou protokolovány do slow_query_log
- **log_throttle_queries_not_using_indexes**: Tento parametr omezuje počet dotazů bez indexu, které lze zapsat do protokolu pomalýdotaz. Tento parametr se projeví, když je log_queries_not_using_indexes nastavena na zapnuto.
- **log_output**: Pokud "Soubor", umožňuje pomalé protokol dotazu, které mají být zapsány do úložiště místního serveru a do diagnostických protokolů monitorování Azure. Pokud "Žádný", protokol pomalé dotazu se zapíše pouze do protokolů diagnostiky monitorování Azure. 

> [!IMPORTANT]
> Pokud vaše tabulky nejsou indexovány, nastavení parametrů `log_queries_not_using_indexes` a `log_throttle_queries_not_using_indexes` na ZAPNUTO může ovlivnit výkon MariaDB, protože všechny dotazy spuštěné v těchto neindexovaných tabulkách budou zapsány do protokolu pomalých dotazů.<br><br>
> Pokud plánujete protokolování pomalé dotazy na delší dobu, je `log_output` doporučeno nastavit na "Žádný". Pokud je nastavena na "Soubor", tyto protokoly jsou zapsány do úložiště místního serveru a může ovlivnit výkon MariaDB. 

Úplný popis parametrů protokolu pomalých dotazů naleznete v [dokumentaci k pomalému dotazu](https://mariadb.com/kb/en/library/slow-query-log-overview/) MariaDB.

## <a name="access-slow-query-logs"></a>Přístup k protokolům pomalých dotazů
Existují dvě možnosti pro přístup k protokolům pomalých dotazů v Azure Database for MariaDB: místní úložiště serveru nebo diagnostické protokoly azure monitoru. To je nastaveno pomocí parametru. `log_output`

Pro místní úložiště serveru můžete vypsat a stáhnout pomalé protokoly dotazů pomocí portálu Azure nebo rozhraní příkazového příkazového příkazu Kontu Azure. Na webu Azure Portal přejděte na svůj server na webu Azure Portal. V záhlaví **Sledování** vyberte stránku **Protokoly serveru.** Další informace o rozhraní příkazového příkazového odlohovky a řízení o Azure najdete [v tématu Konfigurace a přístup k protokolům serveru pomocí rozhraní příkazového příkazového příkazu Kontu Azure](howto-configure-server-logs-cli.md). 

Diagnostické protokoly monitorování Azure umožňuje kanál pomalé protokoly dotazů do protokolů monitorování Azure (Log Analytics), Azure Storage nebo Centra událostí. Více informací naleznete [níže.](concepts-server-logs.md#diagnostic-logs)

## <a name="local-server-storage-log-retention"></a>Uchovávání protokolu úložiště místního serveru
Při protokolování do místního úložiště serveru protokoly jsou k dispozici po dobu až sedmi dnů od jejich vytvoření. Pokud celková velikost dostupných protokolů přesáhne 7 GB, nejstarší soubory budou odstraněny, dokud není k dispozici místo.

Protokoly se otáčejí každých 24 hodin nebo 7 GB podle toho, co nastane dříve.

> [!Note]
> Výše uvedené uchovávání protokolu se nevztahuje na protokoly, které jsou kanálové pomocí protokolů diagnostiky monitorování Azure. Můžete změnit dobu uchování pro jímky dat vyzařované do (např. Azure Storage).

## <a name="diagnostic-logs"></a>Diagnostické protokoly
Azure Database for MariaDB je integrovaná s diagnostickými protokoly azure monitoru. Jakmile povolujete pomalé protokoly dotazů na serveru MariaDB, můžete je nechat vyzařovat do protokolů Azure Monitor, centra událostí nebo do úložiště Azure. Další informace o povolení diagnostických protokolů naleznete v části Jak v dokumentaci k [diagnostickým protokolům](../azure-monitor/platform/platform-logs-overview.md).

Následující tabulka popisuje, co je v každém protokolu. V závislosti na výstupní metodě se zahrnutá pole a pořadí, ve kterém se zobrazují, se mohou lišit.

| **Vlastnost** | **Popis** |
|---|---|
| `TenantId` | ID vašeho klienta |
| `SourceSystem` | `Azure` |
| `TimeGenerated`To je v pořádku. | Časové razítko při zaznamenání protokolu v utc |
| `Type` | Typ protokolu. Vždy`AzureDiagnostics` |
| `SubscriptionId` | Identifikátor GUID pro předplatné, do kterého server patří |
| `ResourceGroup` | Název skupiny prostředků, do které server patří |
| `ResourceProvider` | Název zprostředkovatele prostředků. Vždy`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Identifikátor URI prostředku |
| `Resource` | Název serveru |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Název serveru |
| `start_time_t`To je v pořádku. | Čas zahájení dotazu |
| `query_time_s` | Celkový čas, který dotaz trval a byl proveden |
| `lock_time_s` | Celkový čas uzamčení dotazu |
| `user_host_s` | Uživatelské jméno |
| `rows_sent_s` | Počet odeslaných řádků |
| `rows_examined_s` | Počet zkoumaných řádků |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | ID vložení |
| `sql_text_s` | Úplný dotaz |
| `server_id_s` | ID serveru |
| `thread_id_s` | ID vlákna |
| `\_ResourceId` | Identifikátor URI prostředku |

> [!Note]
> Pro `sql_text`protokol bude zkrácen, pokud překročí 2048 znaků.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analýza protokolů v protokolech monitorování Azure

Jakmile jsou vaše pomalé protokoly dotazů jsou kanály do protokolů monitorování Azure prostřednictvím diagnostických protokolů, můžete provést další analýzu pomalé dotazy. Níže jsou uvedeny některé ukázkové dotazy, které vám pomohou začít. Nezapomeňte aktualizovat níže s názvem serveru.

- Dotazy delší než 10 sekund na konkrétním serveru

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Seznam 5 nejdelších dotazů na konkrétním serveru

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Shrneme pomalé dotazy podle minimální, maximální, průměrné a standardní odchylky dotazovací ho času na konkrétním serveru

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Graf pomalé distribuce dotazů na konkrétním serveru

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Zobrazení dotazů delších než 10 sekund na všech serverech MariaDB s povolenými diagnostickými protokoly

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Další kroky
- [Jak nakonfigurovat pomalé protokoly dotazů z webu Azure Portal](howto-configure-server-logs-portal.md)
- [Konfigurace protokolů pomalých dotazů z rozhraní příkazového příkazu Azure](howto-configure-server-logs-cli.md)
