---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881335"
---
#### <a name="built-in-log-streaming"></a>Integrované streamování protokolů

Pomocí `logstream` této možnosti můžete začít přijímat protokoly datových proudů konkrétní aplikace pro spuštění v Azure, jako v následujícím příkladu:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Můžete také zobrazit [Živé metriky Stream](../articles/azure-monitor/app/live-stream.md) pro vaši aplikaci funkce `--browser` v novém okně prohlížeče zahrnutím možnost, jako v následujícím příkladu:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
