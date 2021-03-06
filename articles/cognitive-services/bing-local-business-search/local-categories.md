---
title: Hledání kategorií rozhraní API pro vyhledávání v místní chodidel Bingu
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak určit kategorie hledání pro koncový bod rozhraní API rozhraní API pro místní firmy Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 56b94d66eb0929d2fd0ca74a1a631d229330adfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "69906400"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Hledání kategorií rozhraní API pro vyhledávání v místní chodidel Bingu

Rozhraní API pro vyhledávání místních společností Bingu umožňuje vyhledávat místní obchodní entity v různých kategoriích, přičemž prioritou je uzavření umístění uživatele. Tato hledání můžete zahrnout do vyhledávání `localCircularView` spolu `localMapView` s [parametry](specify-geographic-search.md)a .


## <a name="toplevel-categories"></a>Kategorie nejvyšší úrovně 

Následující typy definují hlavní kategorie vyhledávání.  Více než jednu kategorii lze zadat pomocí seznamu odděleného `localCategories` čárkami přiřazeného k parametru.  
- EatDrink 
- SeeDo 
- Obchod 
- HotelyAndMotels 
- Bankyaúvěrové svazy 
- Parkování 
- Nemocnice 

## <a name="sub-categories"></a>Dílčí kategorie
Podkategorie jsou předávány `localCategories`stejným způsobem jako . Podkategorie jsou konkrétnější kategorie. Jsou podřízené v tom smyslu, že pokud zadáte kategorii C a jednu z jejích podkategorií S ve stejném seznamu odděleném čárkami, obdržíte stejné výsledky, jako kdybyste zadali c samostatně.

### <a name="eat-drink"></a>Jezte drink 
|  |  |  |  |
| - | - | - | - |
| PivovaryAndBrewPubs | Koktejlsalónky | AfricanRestaurants |
| AmericanRestaurace | Bagels | Restaurace pro grilování |
| Taverny | Sportovní bary | Bary |
| BaryGrilyA Hospody | BufetRestaurace| BelgianRestaurants | 
| BritskéRestaurace | KavárnyRestaurace | KaribikRestaurace |
| ChineseRestaurants | KávaAndTea | Delikatesy | 
| Služba doručení | Diners | Diskontní obchody | 
| Koblihy | Rychlé občerstvení | FrancouzskéRestaurace | 
| Mražený jogurt | NěmeckéRestaurace | Supermarkety | 
| ŘeckéRestaurace | Potraviny | HavajskéRestaurace | 
| HungarianRestaurace | IceCreamAndFrozenDezerty | IndianRestaurace | 
| ItalskéRestaurace | JaponskéRestaurace | Šťávy | 
| KoreanRestaurace | Obchody s alkoholem | MexičankyRestaurace |
| Střední A Střední Toje | Pizza | PolskéRestaurace | 
| PortugaleRestaurace | Preclíky | Restaurace | 
| RusandUkrainianRestaurants | Sendviče | Mořské plodyRestaurace | 
| ŠpanělštinaRestaurace | SteakhouseRestaurace | SushiRestaurace | 
| Stánek s jídlem | ThajskéRestaurace | TureckéRestaurace | 
| VegetarianAndVeganRestaurace | VietnamskéRestaurace|  |
 
### <a name="see-do"></a>Viz Do 
|  |  |  |
| -- | -- | -- |
| Zábavní parky | Atrakcí | Karnevaly |
| Kasina | PamátkyA Historické Weby | Minigolfhřiště |
| MovieTheaters | Muzeí | Parky |
| Prohlídky památek | TuristickéInformace | Zoologické zahrady |
 
### <a name="shop"></a>Obchod 
|  |  |  |
| -- | -- | -- |
| Starožitnictví | Knihkupectví | CDAndRecordStores |
| DětskéOblečeníObchody | CigarAndTobaccoShops | Obchody s komiksy |
| DepartmentStoresObchody | Diskontní obchody | FleaMarketsAndBazary |
| FurnitureStores Obchody | HomeImprovementObchody | JewelryAndWatchesStores |
| Kuchyňské potřebyObchody | Obchody s alkoholem | Nákupní centraAnákupní centra |
| PánskéOblečeníObchody | Hudební obchody | OutletStores Obchody |
| PetShops | PetSupplyObchody | SchoolandOfficeSupplyStores |
| Obchody s obuví | SportingGoodsObchody | ToyAndGameObchody |
| VitaminAndSupplementStores | DámskéoblečeníObchody |  |


## <a name="examples-of-local-categories-search"></a>Příklady vyhledávání v místních kategoriích

Následující příklady GET výsledky `localCategories` podle parametru:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

Následující dotaz omezuje počet výsledků "nemocnice" na první tři vrácené z rozhraní API pro vyhledávání místních společností Bingu:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

Následující příklad JSON odpověď zahrnuje tři nemocnice ve větší oblasti Seattle:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>Další kroky
- [Hranice geografického vyhledávání](specify-geographic-search.md)
- [Dotaz a odpověď](local-search-query-response.md)
- [Rychlý start v C #](quickstarts/local-quickstart.md)
