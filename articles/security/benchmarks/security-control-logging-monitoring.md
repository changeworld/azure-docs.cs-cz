---
title: Řízení zabezpečení Azure – protokolování a monitorování
description: Protokolování a monitorování řízení zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545496"
---
# <a name="security-control-logging-and-monitoring"></a>Řízení zabezpečení: Protokolování a monitorování

Protokolování a monitorování zabezpečení se zaměřuje na aktivity související s povolením, získáním a ukládáním protokolů auditu pro služby Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Použití schválených zdrojů synchronizace času

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft udržuje zdroje času pro prostředky Azure, ale máte možnost spravovat nastavení synchronizace času pro vaše výpočetní prostředky.

Jak nakonfigurovat synchronizaci času pro výpočetní prostředky Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2: Konfigurace správy centrálního protokolu zabezpečení

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 2,2 | 6.5, 6.6 | Zákazník |

Ingestování protokolů prostřednictvím Azure Monitoru pro agregaci dat zabezpečení generovaných koncovými zařízeními, síťovými prostředky a dalšími systémy zabezpečení. V rámci Azure Monitoru můžete pomocí pracovního prostoru Log Analytics dotazovat a provádět analýzy a používat účty úložiště Azure pro dlouhodobé nebo archivní úložiště.

Případně můžete povolit a na palubě data do Azure Sentinelu nebo siem třetí strany. Jak napalubě Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Jak shromažďovat protokoly platformy a metriky pomocí Azure Monitoru:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Jak shromažďovat protokoly interních hostitelů Virtuálního počítače Azure pomocí Azure Monitoru:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Jak začít s Azure Monitorem a integrací SIEM od jiných stran:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Povolení protokolování auditování prostředků Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 2.3 | 6.2, 6.3 | Zákazník |

Povolte nastavení diagnostiky prostředků Azure pro přístup k protokolům auditování, zabezpečení a diagnostiky. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

Jak shromažďovat protokoly platformy a metriky pomocí Azure Monitoru:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Principy protokolování a různé typy protokolů v Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Shromažďování protokolů zabezpečení z operačních systémů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 2.4 | 6.2, 6.3 | Zákazník |

Pokud výpočetní prostředek vlastní společnost Microsoft, je společnost Microsoft zodpovědná za jeho sledování. Pokud výpočetní prostředek vlastní vaše organizace, je vaší odpovědností ho sledovat. Centrum zabezpečení Azure můžete použít ke sledování operačního systému. Data shromážděná Security Center z operačního systému zahrnuje typ operačního systému a verzi, Protokoly operačního systému (Protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy a přihlášený uživatel. Agent analýzy protokolů také shromažďuje soubory výpisu stavu systému.

Jak shromažďovat protokoly interních hostitelů Virtuálního počítače Azure pomocí Azure Monitoru:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Seznamte se s shromažďováním dat Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurace uchovávání úložiště protokolu zabezpečení

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 2.5 | 6.4 | Zákazník |

V rámci Azure Monitoru nastavte dobu uchovávání pracovního prostoru Analýzy protokolů podle předpisů vaší organizace o dodržování předpisů. Účty úložiště Azure používejte pro dlouhodobé nebo archivní úložiště.

Jak nastavit parametry uchovávání protokolu pro pracovní prostory Analýzy protokolů:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6: Sledování a kontrola protokolů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 2,6 | 6.7 | Zákazník |

Analyzujte a monitorujte protokoly pro neobvyklé chování a pravidelně kontrolujte výsledky. Pracovní prostor Služby Analýzy protokolů azure monitoru slouží ke kontrole protokolů a provádění dotazů na data protokolu.

Případně můžete povolit a na palubě data azure sentinelu nebo třetí strany SIEM. 

Jak napalubě Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Pochopit pracovní prostor analýzy protokolů:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak provádět vlastní dotazy v Azure Monitoru:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Povolit upozornění na anomální aktivitu

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 2.7 | 6.8 | Zákazník |

Centrum zabezpečení Azure s pracovním prostorem analýzy protokolů slouží k monitorování a upozorňování na neobvyklé aktivity nalezené v protokolech zabezpečení a událostech.

Případně můžete povolit a na palubě data do Azure Sentinelu.

Jak napalubě Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Jak spravovat výstrahy v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Jak upozornit na data protokolu analytics protokolu:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizovat protokolování antimalwaru

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 2,8 | 8.6 | Zákazník |

Povolte shromažďování antimalwarových událostí pro virtuální počítače Azure a cloudové služby.

Konfigurace antimalwaru microsoftu pro virtuální počítače:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Jak nakonfigurovat antimalwarový program Microsoft pro cloudové služby:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Seznamte se s antimalwarovým programem společnosti Microsoft:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9: Povolení protokolování dotazů DNS

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 2.9 | 8.7 | Zákazník |

Implementujte řešení jiného výrobce pro protokolování DNS.

## <a name="210-enable-command-line-audit-logging"></a>2.10: Povolení protokolování auditu příkazového řádku

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 2.1 | 8.8 | Zákazník |

Ručně nakonfigurujte protokolování konzoly a přepis prostředí PowerShell na základě počtu uzlů.

## <a name="next-steps"></a>Další kroky

Podívejte se na další ovládací prvek zabezpečení: [Identita a řízení přístupu](security-control-identity-access-control.md)
