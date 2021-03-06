---
title: Oprávnění v Azure Active Directory | Microsoft Docs
description: Seznamte se s oprávněními v Azure Active Directory a jejich použitím.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jesakowi
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 08def16f53cb0f544513c39a85f26e97c3606a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154470"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v10-endpoint"></a>Oprávnění a souhlas v koncovém bodě Služby Azure Active Directory v1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) ve velké míře využívá oprávnění v tocích OAuth i OpenID Connect (OIDC). Když vaše aplikace obdrží přístupový token ze služby Azure AD, bude tento přístupový token obsahovat deklarace identity popisující oprávnění vaší aplikace ve vztahu ke konkrétnímu prostředku.

*Oprávnění*, označované také jako *obory*, usnadňují autorizaci prostředku, protože prostředek potřebuje pouze zkontrolovat, zda token obsahuje příslušná oprávnění pro jakékoli volání rozhraní API, které aplikace volá.

## <a name="types-of-permissions"></a>Typy oprávnění

Azure AD definuje dva typy oprávnění:

* **Delegovaná oprávnění** – Používají je aplikace, ve kterých je přihlášený uživatel. V případě těchto aplikací uživatel nebo správce udělí souhlas s oprávněními, která aplikace požaduje, a aplikaci se deleguje oprávnění k tomu, aby při volání rozhraní API fungovala jako přihlášený uživatel. V závislosti na rozhraní API nemusí být uživatel schopen přímo souhlasit s rozhraním API a místo toho [by vyžadoval, aby správce poskytl "souhlas správce"](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).
* **Oprávnění aplikací** – Používají je aplikace spuštěné bez přihlášeného uživatele, například aplikace spuštěné jako služby na pozadí nebo procesy démon. Oprávnění aplikací mohou [správci udělit](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant) pouze proto, že jsou obvykle výkonná a umožňují přístup k datům přes hranice uživatelů nebo data, která by jinak byla omezena na správce. Uživatelé, kteří jsou definováni jako vlastníci aplikace prostředků (tj. rozhraní API, které publikuje oprávnění) mohou také udělit oprávnění aplikací pro rozhraní API, která vlastní.

Efektivní oprávnění jsou oprávnění, která bude mít vaše aplikace při posílání požadavků do rozhraní API. 

* V případě delegovaných oprávnění budou efektivní oprávnění vaší aplikace představovat průnik nejnižších oprávnění udělených aplikaci (prostřednictvím souhlasu) a oprávnění aktuálně přihlášeného uživatele. Aplikace nemůže mít nikdy více oprávnění než přihlášený uživatel. V rámci organizací je možné oprávnění přihlášeného uživatele určit pomocí zásady nebo členství v jedné nebo několika rolích správce. Informace o tom, které role správce mohou souhlasit s delegovanými oprávněními, najdete [v tématu Oprávnění rolí správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md).
    Předpokládejme například, že má vaše aplikace udělené delegované oprávnění `User.ReadWrite.All` v Microsoft Graphu. Toto oprávnění vaší aplikaci výslovně uděluje oprávnění ke čtení a aktualizaci profilu každého uživatele v organizaci. Pokud je přihlášený uživatel globální správce, vaše aplikace bude moct aktualizovat profil každého uživatele v organizaci. Pokud však přihlášený uživatel není v roli správce, vaše aplikace bude moct aktualizovat pouze profil přihlášeného uživatele. Nebude moct aktualizovat profily ostatních uživatelů v organizaci, protože uživatel, jehož jménem má aplikace oprávnění jednat, tato oprávnění nemá.
* V případě oprávnění aplikací představují efektivní oprávnění vaší aplikace plný rozsah implikovaných oprávnění. Například aplikace s oprávněním aplikace `User.ReadWrite.All` může aktualizovat profil každého uživatele v organizaci.

## <a name="permission-attributes"></a>Atributy oprávnění
Oprávnění ve službě Azure AD mají řadu vlastností, které uživatelům, správcům nebo vývojářům aplikací pomáhají kvalifikovaně rozhodovat o tom, k čemu oprávnění uděluje přístup.

> [!NOTE]
> Oprávnění zveřejněná aplikací nebo instančním objektem Azure AD můžete zobrazit pomocí webu Azure Portal nebo PowerShellu. Vyzkoušejte tento skript k zobrazení oprávnění zveřejněných Microsoft Graphem.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Název vlastnosti | Popis | Příklad |
| --- | --- | --- |
| `ID` | Hodnota GUID, která jednoznačně identifikuje toto oprávnění. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca |
| `IsEnabled` | Určuje, jestli je toto oprávnění k dispozici pro použití. | true |
| `Type` | Určuje, jestli toto oprávnění vyžaduje souhlas uživatele nebo souhlas správce. | Uživatel |
| `AdminConsentDescription` | Popis, který se zobrazí správcům při udělování souhlasu správce. | Umožňuje aplikaci číst e-maily v poštovních schránkách uživatelů. |
| `AdminConsentDisplayName` | Popisný název, který se zobrazí správcům při udělování souhlasu správce. | Čtení pošty uživatelů |
| `UserConsentDescription` | Popis, který se zobrazí uživatelům při udělování souhlasu uživatele. |  Umožňuje aplikaci číst e-maily ve vaší poštovní schránce. |
| `UserConsentDisplayName` | Popisný název, který se zobrazí uživatelům při udělování souhlasu uživatele. | Čtení vaší pošty |
| `Value` | Řetězec, který slouží k identifikaci oprávnění v autorizačních tocích OAuth 2.0. V kombinaci s řetězcem identifikátoru URI ID aplikace může `Value` také vytvořit plně kvalifikovaný název oprávnění. | `Mail.Read` |

## <a name="types-of-consent"></a>Typy souhlasu

Aplikace ve službě Azure AD získávají přístup k potřebným prostředkům nebo rozhraním API na základě souhlasu. Existuje několik druhů souhlasu, o kterých byste měli vědět, aby vaše aplikace byla úspěšná. Pokud definujete oprávnění, budete také muset porozumět způsobu, jakým vaši uživatelé budou získávat přístup k vaší aplikaci nebo rozhraní API.

* **Statický souhlas uživatele** – Uděluje se automaticky v rámci [autorizačního toku OAuth 2.0](v1-protocols-oauth-code.md#request-an-authorization-code) při zadávání prostředků, se kterými chce vaše aplikace pracovat. Ve scénáři využívajícím statický souhlas uživatele musí vaše aplikace již mít zadaná všechna potřebná oprávnění v konfiguraci aplikace na webu Azure Portal. Pokud uživatel (případně správce) neudělil souhlas pro tuto aplikaci, Azure AD v tuto chvíli zobrazí uživateli výzvu k poskytnutí souhlasu. 

    Další informace o registraci aplikace Azure AD, která vyžaduje přístup k statické sadě rozhraní API.
* **Dynamický souhlas uživatele** – Jedná se o funkci modelu aplikace Azure AD v2. V tomto scénáři vaše aplikace vyžaduje sadu potřebných oprávnění v [autorizačním toku OAuth 2.0 pro aplikace v2](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent). Pokud uživatel ještě neudělil souhlas, v tuto chvíli se zobrazí výzva k vyjádření souhlasu. [Další informace o dynamickém souhlasu](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent).

    > [!IMPORTANT]
    > Dynamický souhlas může být praktický, ale představuje velkou výzvu pro oprávnění vyžadující souhlas správce, protože prostředí pro udělení souhlasu správce v době vyjádření souhlasu nemá informace o těchto oprávněních. Pokud požadujete oprávnění správce nebo pokud vaše aplikace používá dynamický souhlas, musíte zaregistrovat všechna oprávnění na webu Azure Portal (nejen podmnožinu oprávnění, která vyžadují souhlas správce). To umožňuje správcům tenantů souhlasit jménem všech svých uživatelů.
  
* **Souhlas správce** – Vyžaduje se v případě, že vaše aplikace potřebuje přístup k určitým vysoce privilegovaným oprávněním. Souhlas správce zajistí, aby správci měli k dispozici několik dalších ovládacích prvků, než autorizují přístup aplikací nebo uživatelů k vysoce privilegovaným datům organizace. [Další informace o udělování souhlasu správce](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Osvědčené postupy

### <a name="client-best-practices"></a>Osvědčené postupy pro klienta

- Požadujte pouze oprávnění, která vaše aplikace potřebuje. Aplikace s příliš mnoha oprávněními se vystavují riziku zveřejnění dat uživatelů v případě napadení.
- V závislosti na scénáři, který vaše aplikace podporuje, si můžete vybrat mezi delegovanými oprávněními a oprávněními aplikace.
    - Pokud se volání provádí jménem uživatele, používejte vždy delegovaná oprávnění.
    - Oprávnění aplikace používejte pouze v případě, že aplikace není interaktivní a neprovádí volání jménem žádného konkrétního uživatele. Oprávnění aplikace jsou vysoce privilegovaná a měla by se používat jen tehdy, kdy je to zcela nezbytné.
- Pokud používáte aplikaci založenou na koncovém bodu v2.0, nastavte vždy statická oprávnění (oprávnění zadaná v registraci aplikace) na nadmnožinu dynamických oprávnění, která požadujete za běhu (oprávnění zadaná v kódu a odesílaná jako parametry dotazu v žádosti o autorizaci), aby scénáře jako udělení souhlasu správce fungovaly správně.

### <a name="resourceapi-best-practices"></a>Osvědčené postupy pro prostředky a rozhraní API

- Prostředky, které zveřejňují rozhraní API, by měly definovat konkrétní oprávnění k datům nebo akcím, které chrání. Tento osvědčený postup pomůže zajistit, že klienti neskončí s oprávněním k přístupu k datům, která nepotřebují, a že uživatelé budou dobře informovaní o tom, ke kterým datům udělují souhlas.
- Oprávnění `Read` a `ReadWrite` by prostředky měly definovat explicitně a zvlášť.
- Prostředky by měly všechna oprávnění povolující přístup k datům různých uživatelů označovat jako oprávnění `Admin`.
- Prostředky by měly dodržovat formát pojmenování `Subject.Permission[.Modifier]`, kde:
  - `Subject`odpovídá typu údajů, které jsou k dispozici
  - `Permission`odpovídá opatření, které může uživatel na základě této datové
  - `Modifier`se používá volitelně k popisu specializací jiného oprávnění
    
    Například:
  - Mail.Read – Umožňuje uživatelům číst poštu.
  - Mail.ReadWrite – Umožňuje uživatelům číst nebo psát poštu.
  - Mail.ReadWrite.All – Umožňuje správci nebo uživateli přístup k poště v organizaci.
