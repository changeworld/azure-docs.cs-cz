---
title: 'Úvodní příručka: Provedení webového vyhledávání pomocí jazyka C# – rozhraní REST API pro vyhledávání na webu Bing'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start slouží k odesílání požadavků do rozhraní REST API webového vyhledávání Bingu pomocí jazyka C# a obdržíte odpověď JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: cf12b279cf7bcb20aa655646ce34fb9df2bda016
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167670"
---
# <a name="quickstart-search-the-web-using-the-bing-web-search-rest-api-and-c"></a>Úvodní příručka: Hledání na webu pomocí rozhraní REST API pro vyhledávání na webu Bingu a c #

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní API pro vyhledávání na webu Bingu a obdržet odpověď JSON. Tato aplikace Jazyka C# odešle požadavek na hledání do rozhraní API a zobrazí odpověď. Aplikace je sice napsaná v C#, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

Tady je pár věcí, které budete potřebovat na začátku tohoto rychlého startu:

* Windows: [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/)
* Linux/macOS: [Mono](https://www.mono-project.com/)  
* Klíč předplatného

V tomto ukázkovém programu jsou použité jenom třídy .NET Core.

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-declare-dependencies"></a>Vytvoření projektu a deklarace závislostí

V sadě Visual Studio nebo Mono vytvořte nový projekt. Pak použijte tento kód k importu potřebných oborů názvů a typů.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
```

## <a name="declare-a-namespace-and-class-for-your-program"></a>Deklarace oboru názvů a třídy programu

V tomto rychlém startu dáme většinu kódu do třídy `Program`. Začněte v projektu vytvářet obor názvů `BingSearchApiQuickstart` a třídu `Program`.  

```csharp
namespace BingSearchApisQuickstart
{
    class Program
    {
        // The code in the following sections goes here.
    }
}
```

## <a name="define-variables"></a>Definování proměnných

Abychom mohli pokračovat, musíme nastavit několik proměnných.  `uriBase`může být globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek. Ověřte, že hodnota `uriBase` je platná a nahraďte hodnotu `accessKey` platným klíčem předplatného ze svého účtu Azure. Vyhledávací dotaz můžete přizpůsobit. Stačí místo `searchTerm` zadat jinou hodnotu. Nezapomeňte přidat tento kód `Program` do třídy, jak je uvedeno výše.

```csharp
// Enter a valid subscription key.
const string accessKey = "enter key here";
/*
 * If you encounter unexpected authorization errors, double-check this value
 * against the endpoint for your Bing Web search instance in your Azure
 * dashboard.
 */
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/search";
const string searchTerm = "Microsoft Cognitive Services";
```

## <a name="declare-the-main-method"></a>Deklarace hlavní metody

Metoda `Main()` je vyžadována a je to první metoda vyvolána při spuštění programu. V této aplikaci hlavní metoda ověří `accessKey`, vytvoří požadavek a vytiskne odpověď.

Nezapomeňte, že metoda `main()` závisí na metodách vytvořených v následujících několika oddílech.

```csharp
static void Main()
{
    Console.OutputEncoding = System.Text.Encoding.UTF8;
    if (accessKey.Length == 32)
    {
        Console.WriteLine("Searching the Web for: " + searchTerm);
        SearchResult result = BingWebSearch(searchTerm);
        Console.WriteLine("\nRelevant HTTP Headers:\n");
        foreach (var header in result.relevantHeaders)
            Console.WriteLine(header.Key + ": " + header.Value);

        Console.WriteLine("\nJSON Response:\n");
        Console.WriteLine(JsonPrettyPrint(result.jsonResult));
    }
    else
    {
        Console.WriteLine("Invalid Bing Search API subscription key!");
        Console.WriteLine("Please paste yours into the source code.");
    }
    Console.Write("\nPress Enter to exit ");
    Console.ReadLine();
}
```

## <a name="create-a-struct-for-search-results"></a>Vytvoření struktury výsledků hledání

Tato struktura vrátí výsledky hledání s příslušnými hlavičkami. Nazývá se při vytváření požadavku na rozhraní API pro vyhledávání na webu Bing k vytvoření výsledného objektu.

```csharp
// Returns search results with headers.
struct SearchResult
{
    public String jsonResult;
    public Dictionary<String, String> relevantHeaders;
}
```

## <a name="construct-a-request"></a>Vytvoření požadavku

Tento kód použijte k vytvoření vyhledávacího dotazu, provedení požadavku GET a zpracování odpovědi.

```csharp
/// <summary>
/// Makes a request to the Bing Web Search API and returns data as a SearchResult.
/// </summary>
static SearchResult BingWebSearch(string searchQuery)
{
    // Construct the search request URI.
    var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

    // Perform request and get a response.
    WebRequest request = HttpWebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

    // Create a result object.
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers.
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
}
```

## <a name="format-the-response"></a>Formátování odpovědi

Tato metoda naformátuje odpověď JSON, především doplní odsazení a přidá konce řádků.

```csharp
/// <summary>
/// Formats the JSON string by adding line breaks and indents.
/// </summary>
/// <param name="json">The raw JSON string.</param>
/// <returns>The formatted JSON string.</returns>
static string JsonPrettyPrint(string json)
{
    if (string.IsNullOrEmpty(json))
        return string.Empty;

    json = json.Replace(Environment.NewLine, "").Replace("\t", "");

    StringBuilder sb = new StringBuilder();
    bool quote = false;
    bool ignore = false;
    char last = ' ';
    int offset = 0;
    int indentLength = 2;

    foreach (char ch in json)
    {
        switch (ch)
        {
            case '"':
                if (!ignore) quote = !quote;
                break;
            case '\\':
                if (quote && last != '\\') ignore = true;
                break;
        }

        if (quote)
        {
            sb.Append(ch);
            if (last == '\\' && ignore) ignore = false;
        }
        else
        {
            switch (ch)
            {
                case '{':
                    case '[':
                        sb.Append(ch);
                        sb.Append(Environment.NewLine);
                        sb.Append(new string(' ', ++offset * indentLength));
                        break;
                    case ']':
                    case '}':
                        sb.Append(Environment.NewLine);
                        sb.Append(new string(' ', --offset * indentLength));
                        sb.Append(ch);
                        break;
                    case ',':
                        sb.Append(ch);
                        sb.Append(Environment.NewLine);
                        sb.Append(new string(' ', offset * indentLength));
                        break;
                    case ':':
                        sb.Append(ch);
                        sb.Append(' ');
                        break;
                    default:
                        if (quote || ch != ' ') sb.Append(ch);
                        break;
            }
        }
        last = ch;
    }
    return sb.ToString().Trim();
}
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je spuštění kódu. Pokud chcete porovnat svůj kód s naším, najdete [ukázkový kód na GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingWebSearchv7.cs).

## <a name="sample-response"></a>Ukázková odpověď

Odpovědi rozhraní API Bingu pro vyhledávání na webu se vrátí jako objekt JSON. Ukázková odpověď je zkrácená, aby zobrazovala jenom jeden výsledek.  

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using the Face service from Microsoft Azure. ... Cognitive Services; Face service;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/en-us/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové vyhledávání Bingu – kurz jednostránkové aplikace](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
