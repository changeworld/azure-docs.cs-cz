---
title: 'Kurz: Extrahujte podrobnosti o obrázku pomocí rozhraní REST API a jazyka C# – vyhledávání obrázků Bingem'
titleSuffix: Azure Cognitive Services
description: Tento kurz slouží k vytvoření aplikace Jazyka C#, která extrahuje podrobnosti o obrázku pomocí rozhraní API pro vyhledávání obrázků Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 12/06/2019
ms.author: aahi
ms.openlocfilehash: 69c5fc9805bed8fdfde3dd208e7fb66254e82c3c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75383871"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>Kurz: Extrahování podrobností pomocí rozhraní API Bingu pro vyhledávání obrázků a C#

Existuje více [koncových bodů](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint) dostupných prostřednictvím rozhraní API Bingu pro vyhledávání obrázků. Koncový bod `/details` přijímá s obrázkem požadavek POST a může vrátit různé informace o obrázku. Tato aplikace C# odešle obrázek pomocí tohoto rozhraní API a zobrazí podrobnosti vrácené Bingem, což jsou objekty JSON, například následující:

![[Výsledky JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

Tento kurz vysvětluje následující postupy:

> [!div class="checklist"]
> * Použití koncového bodu `/details` vyhledávání obrázků v požadavku `POST`
> * Zadání hlaviček pro požadavek
> * Specifikace výsledků pomocí parametrů adresy URL
> * Odeslání dat obrázku a odeslání požadavku `POST`
> * Tisk výsledků JSON v konzole

Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs).

## <a name="prerequisites"></a>Požadavky

* Libovolné vydání [Visual studia 2017 nebo novější](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>Vytvoření žádosti o vyhledávání podrobností o snímku

Níže je uvedený koncový bod `/details`, který přijímá požadavky POST s údaji o obrázku v těle požadavku. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

Při vytváření adresy URL žádosti o vyhledávání se parametr `modules` řídí výše uvedeným koncovým bodem a určuje typy podrobností, které budou výsledky obsahovat:

* `modules=All`
* `modules=RecognizedEntities` (lidé nebo místa na obrázku)

Zadáním `modules=All` v požadavku POST získáte text JSON, který obsahuje následující informace:

* `bestRepresentativeQuery` – dotaz Bingu, který vrátí obrázky podobné odeslanému obrázku
* `detectedObjects` – objekty nalezené v obrázku
* `image` – metadata pro obrázek
* `imageInsightsToken` – token pro pozdější požadavky GET, které získají `RecognizedEntities` (lidé nebo místa na obrázku) z obrázku.
* `imageTags` – značky pro obrázek
* `pagesIncluding` – webové stránky, které obsahují daný obrázek
* `relatedSearches` – hledání na základě podrobností v obrázku
* `visuallySimilarImages` – podobné obrázky na webu

Zadejte `modules=RecognizedEntities` v požadavku POST, pokud chcete získat pouze `imageInsightsToken`, který je možné použít v následujícím požadavku GET k identifikaci osob nebo míst na obrázku.

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>Vytvoření objektu WebClient a nastavení hlaviček pro požadavek rozhraní API

Vytvořte objekt `WebClient` a nastavte hlavičky. Všechny požadavky na rozhraní API Bingu pro vyhledávání vyžadují `Ocp-Apim-Subscription-Key`. Požadavek `POST` k nahrání obrázku musí také určovat `ContentType: multipart/form-data`.

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>Odeslání obrázku a zobrazení výsledků

Metoda `UpLoadFile()` třídy `WebClient` formátuje data pro požadavek `POST`, včetně formátování `RequestStream` a volání `HttpWebRequest`.

Volejte `WebClient.UpLoadFile()` s koncovým bodem `/details` a souborem obrázku, který chcete nahrát. Pomocí odpovědi JSON inicializujte instanci struktury `SearchResult` a uložte odpověď.

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
Tato odpověď JSON pak může být vytištěna v konzole.

## <a name="use-an-image-insights-token-in-a-request"></a>Použití tokenu přehledu obrázků v požadavku

Pokud chcete použít token `ImageInsightsToken` vrácený s výsledky požadavku `POST`, můžete ho přidat do požadavku `GET`. Například:

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

Pokud obrázek obsahuje identifikovatelné osoby nebo místa, tento požadavek o nich vrátí informace.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazení obrázků a možnosti vyhledávání v jednostránkové webové aplikaci ](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Viz také

* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
