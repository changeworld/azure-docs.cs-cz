---
title: 'Úvodní příručka: Provedení webového vyhledávání pomocí javy – rozhraní REST API pro vyhledávání na webu Bingu'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start slouží k odeslání požadavku do rozhraní REST API hledání zpráv bingu pomocí jazyka Java a obdržíte odpověď JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 1a3e98afacf85bde8180253078cb53eae9a03d2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75383608"
---
# <a name="quickstart-perform-a-news-search-using-java-and-the-bing-news-search-rest-api"></a>Úvodní příručka: Provádění vyhledávání zpráv pomocí javy a rozhraní REST API pro vyhledávání zpráv Bingu

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní API pro vyhledávání zpráv Bingu a zobrazit odpověď JSON. Tato jednoduchá java aplikace odešle dotaz na vyhledávání zpráv do rozhraní API a zobrazí odpověď.

Aplikace je sice napsaná v Javě, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Zdrojový kód pro tuto ukázku je k dispozici [na GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingNewsSearchv7.java) 

## <a name="prerequisites"></a>Požadavky

* [Java Development Kit (JDK) 7 nebo 8](https://aka.ms/azure-jdks)

* [Knihovna Gson](https://github.com/google/gson)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java a naimportujte následující knihovny.

    ```java
    import java.net.*;
    import java.util.*;
    import java.io.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Vytvořte novou třídu s proměnnými pro koncový bod rozhraní API, klíč předplatného a vyhledávací termín. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

    ```java
    public static SearchResults SearchNews (String searchQuery) throws Exception {
        static String subscriptionKey = "enter key here";
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/news/search";
        static String searchTerm = "Microsoft";
    //...
    }
    ```

## <a name="construct-the-search-request-and-receive-a-json-response"></a>Sestavení požadavku na hledání a přijetí odpovědi JSON

1. Pomocí proměnných z posledního kroku naformátujte vyhledávací adresu URL pro požadavek rozhraní API. Hledaný termín musí být zakódovaný do adresy URL předtím, než se připojí k této žádosti.

    ```java
    public static SearchResults SearchNews (String searchQuery) throws Exception {
        // construct the search request URL (in the form of URL + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    }
    ```

2. Získejte odpověď JSON z rozhraní API pro vyhledávání zpráv Bingu a vytvořte výsledný objekt.

    ```java
    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();
    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
    ```

## <a name="process-the-json-response"></a>Zpracování odpovědi JSON

1. Oddělte hlavičky HTTP související se službou Bing od těla JSON, zavřete datový proud a vraťte odpověď rozhraní API.
    ```java
    // extract Bing-related HTTP headers
    Map<String, List<String>> headers = connection.getHeaderFields();
    for (String header : headers.keySet()) {
        if (header == null) continue;      // may have null key
        if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
            results.relevantHeaders.put(header, headers.get(header).get(0));
        }
    }
    stream.close();
    return results;
    ```

2. Vytvoření metody analýzy a reserializace json
    ```java
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

3. V hlavní metodě aplikace volejte metodu hledání a zobrazte výsledky.
    ```csharp
   public static void main (String[] args) {
       System.out.println("Searching the Web for: " + searchTerm);
       SearchResults result = SearchNews(searchTerm);
    
       System.out.println("\nRelevant HTTP Headers:\n");
       for (String header : result.relevantHeaders.keySet())
             System.out.println(header + ": " + result.relevantHeaders.get(header));  
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    }
    ```

## <a name="json-response"></a>Odpověď JSON

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .
      
      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
