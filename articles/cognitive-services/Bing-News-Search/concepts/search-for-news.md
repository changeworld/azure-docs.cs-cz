---
title: Hledání novinek pomocí rozhraní API pro vyhledávání zpráv Bingu
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak odesílat vyhledávací dotazy na obecné zprávy, aktuální témata a nadpisy.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 6fa12febe99e77efde45bcd2d538de78f618e641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220322"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>Hledání novinek pomocí rozhraní API pro vyhledávání zpráv Bingu

Rozhraní API pro vyhledávání obrázků Bingu usnadňuje integraci funkcí vyhledávání kognitivních zpráv bingu do vašich aplikací.

Zatímco rozhraní API pro vyhledávání zpráv Bingu primárně vyhledává a vrací relevantní zpravodajské články, poskytuje několik funkcí pro inteligentní a cílené získávání zpráv na webu.

## <a name="suggest-and-use-search-terms"></a>Navrhnout a používat hledané výrazy

Pokud nabízíte vyhledávací pole, do kterého může uživatel zadat hledaný termín, můžete hledání vylepšit s využitím [rozhraní API pro automatické návrhy Bingu](../../bing-autosuggest/get-suggested-search-terms.md). Toto rozhraní API vrací navrhované řetězce dotazů na základě částečné shody hledaných termínů zadávaných uživatelem.

Jakmile uživatel zadá hledaný termín, před nastavením parametru dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query) ho zakódujte pomocí kódování URL. Pokud uživatel například zadá *sailing dinghies*, nastavte parametr `q` na hodnotu `sailing+dinghies` nebo `sailing%20dinghies`.

## <a name="get-general-news"></a>Získejte obecné novinky

Pokud chcete z webu získat obecné zprávy související s hledaným termínem uživatele, odešlete tento požadavek GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Pokud voláte některé z rozhraní API Bingu poprvé, nezahrnujte do volání hlavičku ID klienta. ID klienta zahrňte pouze v případě, že jste již dříve volali rozhraní API Bingu a Bing vrátil ID klienta pro příslušnou kombinaci uživatele a zařízení.

Pokud chcete získat zprávy z konkrétní domény, použijte operátor dotazu [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Následující ukázka JSON ukazuje odpověď na předchozí dotaz. Jako součást [požadavky na použití a zobrazení pro](../useanddisplayrequirements.md) vyhledávací api Bing, je nutné zobrazit každý zpravodajský článek v pořadí uvedeném v odpovědi. Pokud článek obsahuje seskupené články, měli byste označit, že existují související články a zobrazit je na požádání.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

Odpověď [news](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#news) (zprávy) vypíše zpravodajské články, které jsou podle Bingu k dotazu relevantní. Pole `totalEstimatedMatches` obsahuje odhadovaný počet článků, které je možné zobrazit. Informace o procházení článků po stránkách najdete v článku o [stránkování zpráv](../paging-news.md).

Každý [zpravodajský článek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#newsarticle) v seznamu obsahuje název, popis a adresu URL článku na webu hostitele. Pokud článek obsahuje obrázek, zahrnuje objekt miniaturu obrázku. Pomocí `name` a `url` vytvořte hypertextový odkaz, který uživatele přenese na zpravodajský článek na webu hostitele. Pokud článek obsahuje obrázek, pomocí `url` umožněte, aby se na obrázek dalo kliknout. Nezapomeňte uvést zdroj článku pomocí `provider`.

Pokud Bing dokáže určit kategorii zpravodajského článku, obsahuje článek pole `category`.

## <a name="get-todays-top-news"></a>Získejte dnešní nejlepší novinky

Chcete-li získat dnešní nejlepší zpravodajské články, můžete odeslat stejný požadavek na obecné zprávy jako dříve a ponechat `q` parametr odstavit.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Odpověď na získání top novinky je téměř stejný jako jeden pro získání obecné zprávy. Odpověď `news` ale neobsahuje pole `totalEstimatedMatches`, protože existuje stanovený počet výsledků. Počet nejčtenějších zpravodajských článků se může lišit v závislosti na zpravodajském cyklu. Nezapomeňte použít `provider` pole k atributu článku.

## <a name="get-news-by-category"></a>Získejte novinky podle kategorií

Pokud chcete získat zpravodajské články podle kategorií, třeba hlavní sportovní zprávy nebo zprávy ze zábavního průmyslu, pošlete do Bingu tento požadavek GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Pomocí parametru dotazu [category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) (kategorie) určete kategorii článků, které chcete získat. Seznam možných zpravodajských kategorií, které můžete určit, najdete ve [zpravodajských kategoriích podle trhu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-categories-by-market).

Odpověď pro získání zpráv podle kategorie je skoro stejná jako pro získání obecných zpráv. Články jsou ale všechny ze zadané kategorie.

## <a name="get-headline-news"></a>Získejte hlavní zprávy

Pokud chcete požádat o hlavní zpravodajské články a získat články ze všech zpravodajských kategorií, pošlete do Bingu tento požadavek GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Nezahrnujte parametr dotazu [category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category).

Odpověď pro získání hlavních zpráv je skoro stejná jako pro získání dnešních nejčtenějších zpráv. Pokud je článek hlavním článkem, jeho pole `headline` je nastavené na **true**.

Ve výchozím nastavení odpověď obsahuje až 12 hlavních článků. Pokud chcete počet hlavních článků k vrácení změnit, zadejte parametr dotazu [headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#headlinecount). Odpověď obsahuje také až čtyři nehlavní články v každé kategorii zpráv.

Odpověď počítá shluky článků jako jeden článek. Protože shluk článků může obsahovat několik článků, může odpověď obsahovat více než 12 hlavních článků a více než čtyři nehlavní články v každé kategorii.

## <a name="get-trending-news"></a>Získejte trendy novinky

Pokud chcete získat zpravodajská témata, která jsou na vzestupu na sociálních sítích, pošlete do Bingu tento požadavek GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> Témata na vzestupu jsou dostupná jenom na americkém a čínském trhu.

Odpovědí na předchozí požadavek je následující kód JSON. Každý zpravodajský článek na vzestupu obsahuje související obrázek, příznak mimořádné zprávy a adresu URL výsledků hledání článku na Bingu. Použijte adresu URL v poli `webSearchUrl` k přenesení uživatele na stránku s výsledky hledání Bingu. Nebo použijte text dotazu k volání [rozhraní API Bingu pro vyhledávání na webu](../../bing-web-search/search-the-web.md), abyste výsledky sami zobrazili.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>Získání souvisejících zpráv

Pokud existují další články, které se zpravodajským článkem souvisejí, může zpravodajský článek obsahovat pole [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle-clusteredarticles). Následuje ukázka článku se shlukem takových článků.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>Omezování požadavků

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jak stránkovat s výsledky vyhledávání zpráv Bing](../paging-news.md)
