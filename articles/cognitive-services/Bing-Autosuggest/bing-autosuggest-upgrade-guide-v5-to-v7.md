---
title: Upgrade rozhraní API automatického návrhu bingu v5 na v7
titleSuffix: Azure Cognitive Services
description: Identifikuje části aplikace, které je třeba aktualizovat, abyste měli používat verzi 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 5249a3a1f51eea2ecd0999d71c6b08fdacf37a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68405412"
---
# <a name="autosuggest-api-upgrade-guide"></a>Průvodce automatickým návrhem upgradu rozhraní API

Tato příručka pro upgrade identifikuje změny mezi verzí 5 a verzí 7 rozhraní API automatického návrhu Bingu. V této příručce můžete aktualizovat aplikaci tak, aby používala verzi 7.

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="endpoints"></a>Koncové body

- Číslo verze koncového bodu se změnilo z verze v5 na v7. Například https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/Suggestions.

### <a name="error-response-objects-and-error-codes"></a>Objekty odpovědí na chybu a kódy chyb

- Všechny neúspěšné požadavky by `ErrorResponse` nyní měly obsahovat objekt v těle odpovědi.

- Do objektu byla `Error` přidána následující pole.  
  - `subCode`&mdash;Rozdělí kód chyby do samostatných bloků, pokud je to možné
  - `moreDetails`&mdash;Další informace o chybě popsané `message` v poli

- Kódy chyb v5 byly nahrazeny následujícími možnými `code` hodnotami a `subCode` hodnotami.

|kód|Dílčí kód|Popis
|-|-|-
|Chyba serveru|Neočekávaná chyba<br/>Chyba zdroje<br/>Není implementováno|Bing vrátí chybu serveru vždy, když dojde k některé z podmínek podkódu. Odpověď zahrnuje tyto chyby, pokud je stavový kód HTTP 500.
|Neplatný požadavek|ParametrMissing ParametrMissing ParametrMissing ParametrMissing<br/>ParametrInvalidValue<br/>HttpNotAllowed<br/>Blokované|Bing vrátí InvalidRequest vždy, když žádná část požadavku není platná. Například chybí požadovaný parametr nebo hodnota parametru není platná.<br/><br/>Pokud je chyba ParametrMissing nebo ParameterInvalidValue, je stavový kód HTTP 400.<br/><br/>Pokud je chyba HttpNotAllowed, stavový kód HTTP je 410.
|RateLimit překročena||Bing vrátí RateLimitExceeded vždy, když překročíte vaše dotazy za sekundu (QPS) nebo dotazy za měsíc (QPM) kvóta.<br/><br/>Bing vrátí stavový kód HTTP 429, pokud jste překročili QPS a 403, pokud jste překročili QPM.
|Neplatná autorizace|AutorizaceChybí<br/>Redundance autorizace|Bing vrátí InvalidAuthorization, když Bing nemůže ověřit volajícího. Například `Ocp-Apim-Subscription-Key` záhlaví chybí nebo klíč předplatného není platný.<br/><br/>K redundanci dochází, pokud zadáte více než jednu metodu ověřování.<br/><br/>Pokud je chyba InvalidAuthorization, stavový kód HTTP je 401.
|Nedostatečná autorizace|Autorizace zakázána.<br/>Platnost oprávnění vypršela.|Bing vrátí InsufficientAuthorization, pokud volající nemá oprávnění k přístupu k prostředku. Tato situace může nastat, pokud byl zakázán klíč předplatného nebo vypršela jeho platnost. <br/><br/>Pokud je chyba InsufficientAuthorization, stavový kód HTTP je 403.

- Následující mapy předchozí kódy chyb na nové kódy. Pokud jste přijali závislost na kódech chyb v5, aktualizujte kód odpovídajícím způsobem.

|Kód verze 5|Kód 7 verze.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|Neplatný parametr Invalid.Neplatný parametr
ResourceAccessDenied|Nedostatečná autorizace
ExceededVolume|RateLimit překročena
Limit překročil qpsLimit|RateLimit překročena
Zakázáno|InsufficientAuthorization.Authorization.AuthorizationDisabled
Neočekávaná chyba|Chyba_serveru_nechybě
Chyby zdroje dat|Chyba_serveru.ResourceError
AutorizaceChybí|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.httpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Není implementováno|ServerError.NotImplemented
Neplatná autorizace|Neplatná autorizace
Metoda Neplatnostauthorizationm|Neplatná autorizace
Metoda multipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
Platnost tokenu AuthorizationToken|InsufficientAuthorization.AuthorizationVypršela
Nedostatečný rozsah|Nedostatečná autorizace
Blokované|Neplatný požadavek.blokován

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Požadavky na použití a zobrazení](./UseAndDisplayRequirements.md)
