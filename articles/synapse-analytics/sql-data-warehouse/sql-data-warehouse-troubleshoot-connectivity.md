---
title: Poradce při potížích s připojením
description: Řešení potíží s připojením ve fondu Synapse SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8aaca40961b1294336b236305ecdffcc92c5a4d9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742576"
---
# <a name="troubleshooting-connectivity-issues"></a>Řešení potíží s připojením

V tomto článku jsou uvedeny běžné techniky řešení potíží týkající se připojení k databázi SQL Analytics.

- [Zkontrolujte dostupnost služby](sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Zkontrolujte pozastavené operace nebo operace škálování](sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Zkontrolujte nastavení brány firewall](sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Zkontrolujte nastavení virtuální sítě / koncového bodu služby](sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Vyhledejte nejnovější ovladače](sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Zkontrolujte připojovací řetězec](sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problémy s přerušovaným připojením](sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Běžné chybové zprávy](sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Zkontrolujte dostupnost služby

Zkontrolujte, zda je služba k dispozici. Na webu Azure portal přejděte do fondu Synapse SQL, který se pokoušíte připojit. V levém panelu TOC klikněte na **Diagnostika a řešení problémů**.

![Vybrat stav zdroje](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Zde se zobrazí stav vašeho fondu Synapse SQL. Pokud se služba nezobrazuje jako **Dostupná**, zkontrolujte další kroky.

![Služba k dispozici](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Pokud váš stav prostředku ukazuje, že vaše synapse SQL fond instance je pozastavena nebo škálování, postupujte podle pokynů k obnovení instance.

![Pozastavená](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) služba Další informace o stavu prostředků naleznete zde.

## <a name="check-for-paused-or-scaling-operation"></a>Zkontrolujte pozastavené operace nebo operace škálování

Zkontrolujte portál a zjistěte, zda je vaše instance fondu SQL Synapse pozastavena nebo škálování.

![Služba pozastavena](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Pokud zjistíte, že vaše služba je pozastavena nebo škálování, zkontrolujte, zda není během plánu údržby. Na portálu pro váš přehled *Overview*fondu SYNApse SQL se zobrazí zvolený plán údržby.

![Přehled plánu údržby](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

V opačném případě se obraťte na správce IT a ověřte, zda tato údržba není naplánovanou událostí. Chcete-li obnovit instanci SQL Analytics, postupujte [takto](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>Zkontrolujte nastavení brány firewall

Databáze SQL Analytics komunikuje přes port 1433.Pokud se pokoušíte připojit z podnikové sítě, vaše brána firewall možná nepovoluje odchozí přenosy přes port 1433. V takovém případě se nebudete moct připojit k serveru služby Azure SQL Database, dokud vaše IT oddělení neotevře port 1433. Další informace o konfiguracích brány firewall naleznete [zde](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Zkontrolujte nastavení virtuální sítě / koncového bodu služby

Pokud se zobrazují chyby 40914 a 40615, přečtěte [si popis chyby a řešení zde](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Vyhledejte nejnovější ovladače

### <a name="software"></a>Software

Zkontrolujte, zda používáte nejnovější nástroje pro připojení k fondu Synapse SQL:

- SSMS
- Azure Data Studio
- Datové nástroje serveru SQL Server (Visual Studio)

### <a name="drivers"></a>Ovladače

Zkontrolujte, zda používáte nejnovější verze ovladačů.Použití starší verze ovladačů může mít za následek neočekávané chování, protože starší ovladače nemusí podporovat nové funkce.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Zkontrolujte připojovací řetězec

Zkontrolujte, jestli jsou správně nastavené připojovací řetězce.  Níže jsou uvedeny některé vzorky.  Další informace o [připojovacích řetězcích najdete tady](sql-data-warehouse-connection-strings.md).

Připojovací řetězec pro ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Připojovací řetězec ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Připojovací řetězec PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Připojovací řetězec JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problémy s přerušovaným připojením

Zkontrolujte, jestli je server hodně zatížený a existuje na něm vysoký počet požadavků ve frontě. Možná budete muset vertikálně navýšit kapacitu fondu SYNAPSE SQL pro další prostředky.

## <a name="common-error-messages"></a>Běžné chybové zprávy

Chyby 40914 a 40615 naleznete v [popisu chyby a řešení zde](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Stále máte problémy s připojením?

Vytvořte [lístek podpory,](sql-data-warehouse-get-started-create-support-ticket.md) aby vás technický tým mohl podpořit.
