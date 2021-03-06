---
title: Limity – LUIS
description: Tento článek obsahuje známá omezení azure kognitivní služby jazykové porozumění (LUIS). Služba LUIS má několik hraničních oblastí. Hranice modelu řídí záměry, entity a funkce v LUIS. Omezení kvót y na základě typu klíče. Kombinace klávesnice řídí web LUIS.
ms.topic: reference
ms.date: 04/02/2020
ms.openlocfilehash: 4aa69cb0fd36fe5bf4ea2928022aea602b8830d6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618869"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Hranice pro váš model luis a klíče
Služba LUIS má několik hraničních oblastí. První je [hranice modelu](#model-boundaries), která řídí záměry, entity a funkce v LUIS. Druhou oblastí jsou [limity kvót](#key-limits) y založené na typu klíče. Třetí oblastí hranic je [kombinace kláves pro](#keyboard-controls) ovládání webu LUIS. Čtvrtá oblast je [mapování oblasti světa](luis-reference-regions.md) mezi webem pro vytváření LUIS a [koncovým bodem](luis-glossary.md#endpoint) LUIS.


## <a name="model-boundaries"></a>Hranice modelu

Pokud vaše aplikace překračuje limity a hranice modelu LUIS, zvažte použití [aplikace odeslání LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) nebo pomocí [kontejneru LUIS](luis-container-howto.md).

|Oblast|Omezení|
|--|:--|
| [Název aplikace][luis-get-started-create-app] | *Výchozí znak max |
| Aplikace| 500 aplikací na vývojový prostředek Azure |
| [Dávkové testování][batch-testing]| 10 datových sad, 1000 projevy na datovou sadu|
| Explicitní seznam | 50 na aplikaci|
| Externí subjekty | bez omezení |
| [Záměry][intents]|500 na aplikaci: 499 vlastní záměry a požadované _Žádný_ záměr.<br>[Aplikace založená na odeslání](https://aka.ms/dispatch-tool) má odpovídající zdroje 500 odeslání.|
| [Entity seznamu](./luis-concept-entity-types.md) | Rodič: 50, dítě: 20 000 položek. Kanonický název je *výchozí znak max. Hodnoty synonyma nemají žádné omezení délky. |
| [Entity získané strojem + role](./luis-concept-entity-types.md):<br> Složené<br>Jednoduché<br>role entity|Limit 100 nadřazených entit nebo 330 entit, podle toho, co omezuje požadavky uživatele jako první. Role se počítá jako entita pro účely této hranice. Příkladem je složený s jednoduchou entitou, která má 2 role je: 1 složené + 1 jednoduché + 2 role = 4 z 330 entit.<br>Dílčí součásti mohou být vnořeny až do 5 úrovní.|
|Model jako prvek| Maximální počet modelů, které lze použít jako popisovač (funkce) pro konkrétní model, aby se 10 modelů. Maximální počet seznamů frází použitých jako deskriptor (funkce) pro konkrétní model má být 10 seznamů frází.|
| [Náhled – entity dynamického seznamu](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 seznamy ~1k na požadavek koncového bodu predikce dotazu|
| [Vzory](luis-concept-patterns.md)|500 vzorů na aplikaci.<br>Maximální délka vzorku je 400 znaků.<br>3 Pattern.any entity na vzorek<br>Maximálně 2 vnořené volitelné texty ve vzorku|
| [Pattern.any](./luis-concept-entity-types.md)|100 na aplikaci, 3 pattern.any entity na vzor |
| [Seznam frází][phrase-list]|500 seznamů frází. 10 globálních frázových seznamů vzhledem k modelu jako limitu funkce. Seznam nezaměnitelných frází má max 5 000 frází. Seznam zaměnitelných frází má max 50 000 frází. Maximální počet frází na aplikaci 500 000 frází.|
| [Předem připravené entity](./luis-prebuilt-entities.md) | bez omezení|
| [Entity regulárního výrazu](./luis-concept-entity-types.md)|20 subjektů<br>500 znaků max. podle vzoru entity regulárního výrazu|
| [Role](luis-concept-roles.md)|300 rolí na aplikaci. 10 rolí na entitu|
| [Promluva][utterances] | 500 znaků|
| [Projevy][utterances] | 15 000 na aplikaci – počet promluv na záměr není nijak omezen.|
| [Verze](luis-concept-version.md)| 100 verzí na aplikaci |
| [Název verze][luis-how-to-manage-versions] | 10 znaků omezených na alfanumerické a tečkové (.) |

*Výchozí znak max je 50 znaků.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Jedinečnost názvu

Použijte následující pravidla jedinečnosti pojmenování.

V aplikaci LUIS musí být jedinečné:

* název verze
* Záměr
* entita
* role

V použitém oboru musí být jedinečný následující:

* seznam frází

## <a name="object-naming"></a>Pojmenování objektů

V následujících názvech nepoužívejte následující znaky.

|Objekt|Vyloučit znaky|
|--|--|
|Názvy záměrů, entit a rolí|`:`<br>`$` <br> `&`|
|Název verze|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Využití prostředků a limity

Language Understand má samostatné prostředky, jeden typ pro vytváření a jeden typ pro dotazování koncový bod předpovědi. Další informace o rozdílech mezi typy klíčů naleznete v [tématu Vytváření a koncový bod pro předpověď dotazu klíče v LUIS](luis-concept-keys.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Vytváření limitů prostředků

Při _kind_filtrování `LUIS.Authoring`prostředků na webu Azure Portal použijte typ , při filtrování prostředků. Služba LUIS omezuje 500 aplikací na zdrojový zdroj Azure.

|Vytváření prostředků|Vytváření TPS|
|--|--|
|Starter|1 milion/měsíc, 5/s|
|F0 - Úroveň zdarma |1 milion/měsíc, 5/s|

* TPS = Transakce za sekundu

[Přečtěte si další informace o cenách.][pricing]

### <a name="query-prediction-resource-limits"></a>Limity prostředků předpovědi dotazu

Při _kind_filtrování `LUIS`prostředků na webu Azure Portal použijte typ , při filtrování prostředků. Prostředek koncového bodu prognózy dotazu LUIS, který se používá v běhu, je platný pouze pro dotazy koncového bodu.

|Prostředek předpovědi dotazu|Dotaz TPS|
|--|--|
|F0 - Úroveň zdarma |10 tisíc/měsíc, 5/s|
|S0 - Standardní úroveň|50/s|

### <a name="sentiment-analysis"></a>Analýza mínění

[Integrace analýzy mínění](luis-how-to-publish-app.md#enable-sentiment-analysis), která poskytuje informace o mínění, je k dispozici bez nutnosti dalšího prostředku Azure.

### <a name="speech-integration"></a>Integrace řeči

[Integrace řeči](../speech-service/how-to-recognize-intents-from-speech-csharp.md) poskytuje 1 tisíc požadavků koncového bodu na jednotkovou cenu.

[Přečtěte si další informace o cenách.][pricing]

## <a name="keyboard-controls"></a>Ovládací prvky klávesnice

|Vstup z klávesnice | Popis |
|--|--|
|Ovládací prvek+E|přepíná mezi tokeny a entitami v seznamu projevy|

## <a name="website-sign-in-time-period"></a>Časové období přihlášení k webu

Váš přístup k přihlášení je na **60 minut**. Po uplynutí této doby se zobrazí tato chyba. Musíte se znovu přihlásit.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
