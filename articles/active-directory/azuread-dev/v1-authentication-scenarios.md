---
title: Azure AD pro vývojáře (v1.0) | Azure
description: Naučte se základy ověřování pro Azure AD pro vývojáře (v1.0), jako je model aplikace, rozhraní API, zřizování a nejběžnější scénáře ověřování.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 36b39f3706db615e40ebfadebf36be4d8b29c33e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154725"
---
# <a name="what-is-authentication"></a>Co je ověřování?

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

*Ověřování* je vyzvání jedné strany k dodání legitimních přihlašovacích údajů, čímž vzniká základ pro vytvoření objektu zabezpečení sloužícího k řízení identity a přístupu. Jednodušeji řečeno jde o proces prokázání, že jste skutečně tím, kým tvrdíte, že jste. V angličtině se pro ověřování někdy používá zkrácené slovo AuthN.

*Autorizace* je udělení oprávnění ověřenému objektu zabezpečení, aby mohl něco provést. Určuje, ke kterým datům máte povolený přístup a co s nimi můžete dělat. V angličtině se pro autorizaci někdy používá zkrácené slovo AuthZ.

Azure Active Directory pro vývojáře (v1.0) (Azure AD) zjednodušuje ověřování pro vývojáře aplikací tím, že poskytuje identitu jako službu, s podporou oborových standardních protokolů, jako je OAuth 2.0 a OpenID Connect, a také open source knihoven pro různé platformy, které vám pomohou začít rychle kódovat.

V modelu programování Azure AD existují dva hlavní případy použití:

* Během toku udělení autorizace OAuth 2.0 – když vlastník prostředku udělí autorizaci klientské aplikaci, čímž klientovi umožní přístup k prostředkům vlastníka prostředku.
* Během přístupu k prostředkům ze strany klienta – podle implementace serverem prostředků, využívání hodnot deklarace identity přítomných v přístupovém tokenu k rozhodování o řízení přístupu na jejich základě.

## <a name="authentication-basics-in-azure-ad"></a>Základy ověřování ve službě Azure AD

Představme si nejzákladnější scénář, ve kterém se vyžaduje identita: uživatel ve webovém prohlížeči se musí ověřit pro webovou aplikaci. Tento scénář je znázorněný na následujícím diagramu:

![Přehled přihlášení do webové aplikace](./media/v1-authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Zde je to, co potřebujete vědět o různých součástech uvedených v diagramu:

* Azure AD je zprostředkovatelem identity. Zprostředkovatel identity je zodpovědný za ověření identity uživatelů a aplikací, které existují v adresáři organizace, a vydává tokeny zabezpečení po úspěšném ověření těchto uživatelů a aplikací.
* Aplikace, která chce zadávat ověřování do Azure AD, musí být registrovaná ve službě Azure Active Directory (Azure AD). Azure AD aplikaci zaregistruje a jednoznačně identifikuje v adresáři.
* Vývojáři můžou využívat opensourcové knihovny ověřování Azure AD, které ověřování usnadňují tím, že podrobnosti protokolu zpracují za vás. Další informace naleznete v knihovnách ověřování platformy Microsoft identit [v2.0](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) a [v1.0 ověřovacích knihoven](active-directory-authentication-libraries.md).
* Jakmile je uživatel ověřen, aplikace musí ověřit token zabezpečení uživatele, aby bylo zajištěno, že ověřování bylo úspěšné. K dispozici jsou rychlé starty, kurzy a ukázky kódu v různých jazycích a architekturách, které ukazují, co musí aplikace dělat.
  * Pokud chcete rychle vytvořit aplikaci a přidat funkce, jako jsou získání tokenů, aktualizace tokenů, přihlášení uživatele, zobrazení některých informací o uživateli a další, projděte si v dokumentaci sekci **Rychlé starty**.
  * Pokud chcete získat podrobné postupy založené na scénářích pro hlavní ověřovací úkoly pro vývojáře, jako jsou získání přístupových tokenů a jejich používání ve volání rozhraní API Microsoft Graph a dalších rozhraní API, implementace přihlášení s Microsoftem v tradiční aplikaci založené na webovém prohlížeči pomocí OpenID Connect a další úkoly, projděte si v dokumentaci sekci **Kurzy**.
  * Pokud si chcete stáhnout ukázky kódu, přejděte na [GitHub](https://github.com/Azure-Samples?q=active-directory).
* Tok požadavků a odpovědí pro proces ověřování závisí na použitém ověřovacím protokolu, jako je OAuth 2.0, OpenID Connect, WS-Federation nebo SAML 2.0. Další informace o protokolech naleznete v dokumentaci v části **Koncepty > ověřovací protokol.**

Ve výše popsaném příkladu scénáře můžete aplikace klasifikovat podle těchto dvou rolí:

* Aplikace, které potřebují zabezpečený přístup k prostředkům
* Aplikace, které hrají roli samotného prostředku

### <a name="how-each-flow-emits-tokens-and-codes"></a>Jak každý tok vyzařuje tokeny a kódy

V závislosti na tom, jak je váš klient sestaven, může použít jeden (nebo několik) toků ověřování podporovaných službou Azure AD. Tyto toky můžete vytvářet různé tokeny (id_tokens, obnovovací tokeny, přístupové tokeny) a také autorizační kódy a vyžadují různé tokeny, aby fungovaly. Tento graf poskytuje přehled:

|Tok | Vyžaduje | id_token | přístupový token | obnovovací token | autorizační kód | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Tok autorizačního kódu](v1-protocols-oauth-code.md) | | x | x | x | x|  
|[Implicitní tok](v1-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybridní TOK OIDC](v1-protocols-openid-connect-code.md#get-access-tokens)| | x  | |          |            x   |
|[Obnovení uplatnění tokenu](v1-protocols-oauth-code.md#refreshing-the-access-tokens) | obnovovací token | x | x | x| |
|[Tok On-Behalf-Of](v1-oauth2-on-behalf-of-flow.md) | přístupový token| x| x| x| |
|[Přihlašovací údaje klienta](v1-oauth2-client-creds-grant-flow.md) | | | x (pouze aplikace)| | |

Tokeny vydané prostřednictvím implicitního režimu mají omezení délky kvůli předání `response_mode` `query` zpět `fragment`do prohlížeče prostřednictvím adresy URL (kde je nebo ).  Některé prohlížeče mají limit na velikost url, které mohou být uvedeny v panelu prohlížeče a selhání, když je příliš dlouhá.  Proto tyto tokeny nemají `groups` `wids` nebo deklarace identity. 

Když teď máte přehled o základech, pokračujte ve čtení, abyste se seznámili s aplikačním modelem identity a rozhraním API, fungováním zřizování v Azure AD a odkazy na podrobné informace o běžných scénářích, které Azure AD podporuje.

## <a name="application-model"></a>Aplikační model

Azure AD zastupuje aplikace podle konkrétního modelu, který je navržený k plnění dvou hlavních funkcí:

* **Identifikovat aplikaci podle ověřovacího protokolu, který podporuje** – to zahrnuje vytvoření výčtu všech identifikátorů, adres URL, tajných kódů a souvisejících informací, které jsou potřeba během ověřování. Azure AD tady:

    * Obsahuje všechna data potřebná pro podporu ověřování v době běhu.
    * Obsahuje všechna data pro rozhodování o tom, k jakým prostředkům může aplikace potřebovat přístup a jestli by se měl daný požadavek splnit a za jakých okolností.
    * Poskytuje infrastrukturu pro implementaci zřizování aplikace v rámci tenanta vývojáře aplikace a do jakéhokoli jiného tenanta Azure AD.

* **Zpracovat souhlas uživatele během žádosti o token a usnadnit dynamické zřizování aplikací mezi tenanty** – tady Azure AD:

    * Umožňuje uživatelům a správcům dynamicky udělovat nebo odepírat souhlas s tím, aby aplikace jejich jménem měla přístup k prostředkům.
    * Umožňuje správcům nakonec rozhodnout, co můžou aplikace provádět a kteří uživatelé můžou konkrétní aplikace používat a jak se přistupuje k prostředkům adresáře.

**Objekt aplikace** v Azure AD popisuje aplikaci jako abstraktní entitu. Vývojáři pracují s aplikacemi. Azure AD v době nasazení používá daný objekt aplikace jako podrobný plán k vytvoření **instančního objektu**, který představuje konkrétní instanci aplikace v rámci adresáře a tenanta. Právě instanční objekt definuje, co aplikace v konkrétním cílovém adresáři ve skutečnosti může dělat, kdo ji může používat, k jakým prostředkům má přístup a tak dále. Azure AD vytváří instanční objekt z objektu aplikace prostřednictvím **souhlasu**.

Následující diagram znázorňuje zjednodušený tok zřizování v Azure AD s využitím souhlasu.  V něm existují dva klienti (A a B), kde tenant A vlastní aplikaci a tenant B je vytváření instancí aplikace prostřednictvím instančního objektu.  

![Zjednodušený tok zřizování s využitím souhlasu](./media/v1-authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

V tomto toku zřizování:

1. Uživatel z klienta B se pokusí přihlásit pomocí aplikace, koncový bod autorizace požaduje token pro aplikaci.
1. Pověření uživatele jsou získána a ověřena pro ověření
1. Uživatel je vyzván k poskytnutí souhlasu pro aplikaci získat přístup k klientovi B
1. Azure AD používá aplikační objekt v tenantovi A jako podrobný plán pro vytvoření instančního objektu v tenantovi B
1. Uživatel obdrží požadovaný token.

Tento proces můžete libovolně opakovat pro další klienty (C, D a tak dále). Tenant A zachová podrobný plán pro aplikaci (aplikační objekt). Uživatelé a správci všech ostatních tenantů, kde aplikace obdrží souhlas, si zachovají kontrolu nad tím, co aplikace může dělat, prostřednictvím odpovídajícího instančního objektu v každém tenantovi. Další informace naleznete [v tématu Application and service principal objects in Microsoft identity platform .](../develop/app-objects-and-service-principals.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

## <a name="claims-in-azure-ad-security-tokens"></a>Deklarace identity v tokenech zabezpečení Azure AD

Tokeny zabezpečení (přístupové a ID tokeny) vydané službou Azure AD obsahují deklarace identity neboli tvrzení informací o subjektu, který byl ověřen. Aplikace můžou deklarace identity používat pro různé úkoly, včetně:

* Ověření tokenu
* Identifikace tenanta adresáře subjektu
* Zobrazení informací o uživateli
* Určení autorizace subjektu

Deklarace identity přítomné v jakémkoli tokenu zabezpečení jsou závislé na typu tokenu, typu přihlašovacích údajů pro ověření uživatele a konfiguraci aplikace.

Stručný popis každého typu deklarace identity vygenerovaného službou Azure AD najdete v následující tabulce. Podrobnější informace najdete v tématu [přístupové tokeny](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) a [ID tokeny](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) vydané Azure AD.

| Deklarovat | Popis |
| --- | --- |
| ID aplikace | Identifikuje aplikaci, která token používá. |
| Cílová skupina | Identifikuje přijímající prostředek, pro který je token určený. |
| Reference třídy kontextu ověřování aplikace | Určuje, jak byl klient ověřen (veřejný klient nebo důvěrný klient). |
| Okamžik ověření | Zaznamenává datum a čas, kdy k ověření došlo. |
| Metoda ověřování | Určuje, jak byl subjekt tokenu ověřen (heslo, certifikát atd.). |
| Jméno | Poskytuje křestní jméno uživatele, jak je nastavené v Azure AD. |
| Skupiny | Obsahuje ID objektů skupin Azure AD, kterých je uživatel členem. |
| Zprostředkovatel identity | Zaznamenává zprostředkovatele identity, který ověřil subjekt tokenu. |
| Vystaveno | Zaznamená čas, kdy byl token vystaven, což se často používá pro aktuálnost tokenu. |
| Vystavitel | Identifikuje službu tokenů zabezpečení, která token vygenerovala, a také tenanta Azure AD. |
| Příjmení | Poskytuje příjmení uživatele, jak je nastavené v Azure AD. |
| Name (Název) | Poskytuje lidsky čitelnou hodnotu, která identifikuje subjekt tokenu. |
| ID objektu | Obsahuje neměnný a jedinečný identifikátor subjektu v Azure AD. |
| Role | Obsahuje popisné názvy aplikačních rolí Azure AD, které byly uživateli uděleny. |
| Rozsah | Určuje oprávnění udělená klientské aplikaci. |
| Subjekt | Určuje objekt zabezpečení, o kterém token prosazuje informace. |
| ID tenanta | Obsahuje neměnný a jedinečný identifikátor tenanta adresáře, který token vydal. |
| Živostnost tokenu | Definuje časový interval, ve kterém je token platný. |
| Hlavní název uživatele | Obsahuje hlavní název uživatele subjektu. |
| Version | Obsahuje číslo verze tokenu. |

## <a name="next-steps"></a>Další kroky

* Informace o [typech aplikací a scénářích podporovaných v platformě Microsoft identit](app-types.md)
