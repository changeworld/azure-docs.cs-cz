---
title: Doporučení zabezpečení pro úložiště fronty
titleSuffix: Azure Storage
description: Přečtěte si o doporučeních zabezpečení pro úložiště front. Implementace těchto pokynů vám pomůže splnit vaše bezpečnostní povinnosti, jak je popsáno v našem modelu sdílené odpovědnosti.
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 8bb56db9eed962ac8f8202c61a7446527c15dfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060895"
---
# <a name="security-recommendations-for-queue-storage"></a>Doporučení zabezpečení pro úložiště fronty

Tento článek obsahuje doporučení zabezpečení pro úložiště fronty. Implementace těchto doporučení vám pomůže splnit vaše bezpečnostní povinnosti, jak je popsáno v našem modelu sdílené odpovědnosti. Další informace o tom, co společnost Microsoft dělá pro plnění povinností poskytovatele služeb, našlápne [na sdílené odpovědnosti za cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Některá doporučení zahrnutá v tomto článku můžete automaticky sledovat pomocí Azure Security Center. Azure Security Center je první obranná linie ochrany vašich prostředků v Azure. Informace o Azure Security Center najdete v tématu [Co je Azure Security Center?](../../security-center/security-center-intro.md).

Azure Security Center pravidelně analyzuje stav zabezpečení prostředků Azure k identifikaci potenciálních chyb zabezpečení. Poté poskytuje doporučení, jak je řešit. Další informace o doporučeních Azure Security Center najdete [v tématu Doporučení zabezpečení v Azure Security Center](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Ochrana dat

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Použití modelu nasazení Azure Resource Manageru | Vytvořte nové účty úložiště pomocí modelu nasazení Azure Resource Manager pro důležitá vylepšení zabezpečení, včetně vynikajícího řízení přístupu (RBAC) a auditování, nasazení a zásad správného řízení na základě Správce prostředků, přístupu ke spravovaným identitám, přístupu k Azure Key Vault pro tajné klíče a ověřování a autorizace na základě Azure AD pro přístup k datům a prostředkům Azure Storage. Pokud je to možné, migrujte existující účty úložiště, které používají klasický model nasazení k použití Správce prostředků Azure. Další informace o Azure Resource Manager, najdete [v tématu Přehled Správce prostředků Azure](/azure/azure-resource-manager/resource-group-overview). | - |
| Povolení možnosti **Zabezpečení přenosu na** všech účtech úložiště | Pokud povolíte možnost **Zabezpečené přenos požadované,** všechny požadavky proti účtu úložiště musí probíhat přes zabezpečené připojení. Všechny požadavky provedené přes protokol HTTP se nezdaří. Další informace najdete [v tématu Require secure transfer in Azure Storage](../common/storage-require-secure-transfer.md). | [Ano](../../security-center/security-center-sql-service-recommendations.md) |
| Povolení pokročilé ochrany před hrozbami pro všechny účty úložiště | Pokročilá ochrana před hrozbami pro Azure Storage poskytuje další vrstvu inteligence zabezpečení, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo zneužití účtů úložiště. Výstrahy zabezpečení se aktivují v Centru zabezpečení Azure, když dojde k anomáliím v aktivitě a jsou také odesílány e-mailem správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeními, jak vyšetřovat a napravou hrozeb. Další informace najdete [v tématu Pokročilá ochrana před hrozbami pro Azure Storage](../common/storage-advanced-threat-protection.md). | [Ano](../../security-center/security-center-sql-service-recommendations.md) |
| Omezení tokenů sdíleného přístupového podpisu (SAS) pouze na připojení HTTPS | Vyžadování protokolu HTTPS, když klient používá token SAS pro přístup k datům fronty, pomáhá minimalizovat riziko odposlechu. Další informace najdete v tématu [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Autorizace přístupu k datům fronty pomocí služby Azure Active Directory (Azure AD) | Azure AD poskytuje vynikající zabezpečení a snadné použití přes sdílený klíč pro autorizaci požadavků na úložiště fronty. Další informace najdete [v tématu Autorizace přístupu k objektům BLOB azure a frontám pomocí služby Azure Active Directory](../common/storage-auth-aad.md). | - |
| Mějte na paměti, jistiny nejnižší oprávnění při přiřazování oprávnění k objektu zabezpečení Azure AD prostřednictvím RBAC | Při přiřazování role uživateli, skupině nebo aplikaci udělte tomuto objektu zabezpečení pouze ta oprávnění, která jsou pro ně nezbytná k provádění jejich úkolů. Omezení přístupu k prostředkům pomáhá zabránit neúmyslnému i škodlivému zneužití vašich dat. | - |
| Zabezpečení přístupových klíčů účtu pomocí azure key vaultu | Microsoft doporučuje používat Azure AD k autorizaci požadavků na Azure Storage. Pokud však musíte použít autorizaci sdíleného klíče, zabezpečte klíče účtů pomocí azure key vault. Klíče můžete načíst z trezoru klíčů za běhu, namísto jejich uložení s vaší aplikací. | - |
| Pravidelně obnovovat klíče účtu | Pravidelné otáčení klíčů účtu snižuje riziko vystavení dat škodlivým objektům. | - |
| Při přiřazování oprávnění sas mějte na paměti objekt zabezpečení s nejnižšími oprávněními. | Při vytváření SAS zadejte pouze oprávnění, která jsou vyžadována klientem k provedení jeho funkce. Omezení přístupu k prostředkům pomáhá zabránit neúmyslnému i škodlivému zneužití vašich dat. | - |
| Mít plán odvolání na místě pro všechny SAS, které vydáváte klientům | Pokud je sas ohrožena, budete chtít odvolat, že SAS co nejdříve. Chcete-li odvolat uživatelské delegování SAS, odvolejte klíč delegování uživatele, abyste rychle zrušili platnost všech podpisů přidružených k tomuto klíči. Chcete-li odvolat službu SAS, která je přidružena k uložené zásadě přístupu, můžete odstranit uložené zásady přístupu, přejmenovat zásadu nebo změnit dobu vypršení platnosti na čas, který je v minulosti. Další informace najdete v tématu [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md).  | - |
| Pokud služba SAS není přidružena k uloženým zásadám přístupu, nastavte dobu vypršení platnosti na jednu hodinu nebo méně | Službu SAS, která není přidružena k uložené zásady přístupu nelze odvolat. Z tohoto důvodu se doporučuje omezit dobu vypršení platnosti tak, aby sas je platný po dobu jedné hodiny nebo méně. | - |

## <a name="networking"></a>Síťové služby

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Povolení pravidel brány firewall | Nakonfigurujte pravidla brány firewall tak, aby byl přístup k účtu úložiště omezen na požadavky, které pocházejí ze zadaných IP adres nebo rozsahů, nebo ze seznamu podsítí ve virtuální síti Azure (Virtuální síť). Další informace o konfiguraci pravidel brány firewall naleznete v tématu [Azure File Sync proxy a nastavení brány firewall](../files/storage-sync-files-firewall-and-proxy.md). | - |
| Povolení přístupu důvěryhodných služeb Společnosti Microsoft k účtu úložiště | Zapnutí pravidel brány firewall pro váš účet úložiště blokuje příchozí požadavky na data ve výchozím nastavení, pokud požadavky pocházejí ze služby pracující v rámci virtuální sítě (VNet) Azure nebo z povolených veřejných IP adres. Mezi blokované požadavky patří požadavky z jiných služeb Azure, z portálu Azure, ze služeb protokolování a metrik a tak dále. Žádosti z jiných služeb Azure můžete povolit přidáním výjimky, která umožní důvěryhodným službám Microsoftu přístup k účtu úložiště. Další informace o přidání výjimky pro důvěryhodné služby Microsoftu najdete v [tématu Azure File Sync proxy a nastavení brány firewall](../files/storage-sync-files-firewall-and-proxy.md).| - |
| Použití soukromých koncových bodů | Privátní koncový bod přiřadí privátní IP adresu z vaší virtuální sítě Azure (VNet) k účtu úložiště. Zabezpečuje veškerý provoz mezi vaší virtuální sítí a účtem úložiště přes privátní odkaz. Další informace o privátní koncové body, najdete v [tématu připojení soukromě k účtu úložiště pomocí Azure Private Endpoint](../../private-link/create-private-endpoint-storage-portal.md). | - |
| Omezit přístup k síti na určité sítě | Omezení přístupu k síti na sítě hostující klienty vyžadující přístup snižuje vystavení prostředků síťovým útokům. | [Ano](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>Protokolování/monitorování

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Sledování způsobu autorizace požadavků | Povolte protokolování azure storage sledovat, jak byl každý požadavek proti Azure Storage autorizován. Protokoly označují, zda byl požadavek podán anonymně pomocí tokenu OAuth 2.0, pomocí sdíleného klíče nebo pomocí sdíleného přístupového podpisu (SAS). Další informace najdete v tématu [protokolování analýzy azure storage](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Další kroky

- [Dokumentace zabezpečení Azure](https://docs.microsoft.com//azure/security/)
- [Zabezpečte vývojovou dokumentaci](https://docs.microsoft.com/azure/security/develop/).
