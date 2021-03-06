---
title: 'Proxy aplikace Azure AD: Historie verzí vydání | Dokumenty společnosti Microsoft'
description: Tento článek obsahuje seznam všech verzí proxy aplikací Azure AD a popisuje nové funkce a opravené problémy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2020
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: f027fbce66a73306165a0ad35d1ba3faa7a5c0bc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983887"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Proxy aplikace Azure AD: Historie verzí vydání
Tento článek uvádí verze a funkce proxy aplikací Azure Active Directory (Azure AD), které byly vydány. Tým Azure AD pravidelně aktualizuje proxy aplikace s novými funkcemi a funkcemi. Konektory proxy aplikací jsou automaticky aktualizovány při vydání nové verze. 

Doporučujeme zajistit, aby byly pro vaše konektory povoleny automatické aktualizace, abyste měli k dispozici nejnovější funkce a opravy chyb. Společnost Microsoft poskytuje přímou podporu pro poslední verzi konektoru a jednu verzi před.

Zde je seznam souvisejících zdrojů:

Prostředek |  Podrobnosti
--------- | --------- |
Jak povolit proxy aplikace | Předpoklady pro povolení proxy aplikace a instalaci a registraci konektoru jsou popsány v tomto [kurzu](application-proxy-add-on-premises-application.md).
Principy konektorů proxy aplikací Azure AD | Další informace o [správě konektorů](application-proxy-connectors.md) a o [automatickém upgradu](application-proxy-connectors.md#automatic-updates)konektorů .
Konektor proxy aplikace Azure AD ke stažení |  [Stáhněte si nejnovější konektor](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Stav verze

Duben 07, 2020: Vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
-   Konektory používají tls 1.2 pouze pro všechna připojení. Další podrobnosti najdete [v tématu Požadavky konektoru.](application-proxy-add-on-premises-application.md#before-you-begin)
- Vylepšená signalizace mezi konektorem a službami Azure. To zahrnuje podporu spolehlivé relace pro komunikaci WCF mezi konektorem a službami Azure a vylepšení ukládání DO mezipaměti DNS pro komunikaci WebSocket.
- Podpora konfigurace proxy mezi konektorem a back-endovou aplikací. Další informace naleznete v [tématu Práce s existujícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Oprava potíží
- Odebráno, když se vrátíte na port 8080 pro komunikaci ze služby Konektor do Služby Azure.
- Přidány trasování ladění pro komunikaci WebSocket. 
- Vyřešeno zachování atributu SameSite při nastavení souborů cookie back-endové aplikace.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Stav verze

Září 20, 2018: Vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

- Přidána podpora WebSocket pro aplikaci QlikSense. Další informace o integraci technologie QlikSense s proxy aplikací naleznete v tomto [návodu](application-proxy-qlik.md). 
- Byl vylepšen průvodce instalací, aby bylo snazší konfigurovat odchozí proxy server. 
- Nastavte TLS 1.2 jako výchozí protokol pro konektory. 
- Byla přidána nová licenční smlouva s koncovým uživatelem (EULA).  

### <a name="fixed-issues"></a>Oprava potíží

- Opravena chyba, která způsobovala nevracení paměti v konektoru.
- Byla aktualizována verze služby Azure Service Bus, která zahrnuje opravu chyb pro problémy s časovým časem konektoru.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Stav verze

Leden 19, 2018: Vydáno ke stažení

### <a name="fixed-issues"></a>Oprava potíží

- Byla přidána podpora pro vlastní domény, které potřebují překlad domény v souboru cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Stav verze 

Květen 25, 2017: Vydáno ke stažení 

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 

Vylepšená kontrola nad limity odchozího připojení konektorů. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Stav verze

15. dubna 2017: Vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

- Zjednodušené zařazování a správa s menším počtem požadovaných portů. Proxy aplikace nyní vyžaduje otevření pouze dvou standardních odchozích portů: 443 a 80. Proxy aplikace nadále používá pouze odchozí připojení, takže stále nepotřebujete žádné součásti v DMZ. Podrobnosti naleznete v naší [konfigurační dokumentaci](application-proxy-add-on-premises-application.md).  
- Pokud je to podporováno externím proxy serverem nebo bránou firewall, můžete nyní otevřít síť pomocí služby DNS namísto rozsahu IP adres. Služby proxy aplikací vyžadují pouze připojení k *.msappproxy.net a *.servicebus.windows.net.


## <a name="earlier-versions"></a>Dřívější verze

Pokud používáte konektor proxy aplikace verze starší než 1.5.36.0, aktualizujte na nejnovější verzi a ujistěte se, že máte nejnovější plně podporované funkce.

## <a name="next-steps"></a>Další kroky
- Další informace o [vzdáleném přístupu k místním aplikacím prostřednictvím proxy aplikací Azure AD](application-proxy.md).
- Informace o spuštění proxy aplikace naleznete v [tématu Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace](application-proxy-add-on-premises-application.md).
