---
title: 'Získat model s voláním REST v C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 96129b9141b4759fd61b539fa08354f02af3af7b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80151218"
---
## <a name="prerequisites"></a>Požadavky

* Azure Language Understanding – vytváření klíče 32 znaků prostředku a vytváření adresy URL koncového bodu. Vytvořte pomocí [portálu Azure nebo](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) [příkazového příkazového příkazu Kontu Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importujte aplikaci [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z úložiště GitHub, které rozumí kognitivním službám.
* ID aplikace LUIS pro naimportovanou aplikaci TravelAgent ID aplikace je uvedené na řídicím panelu aplikace.
* ID verze aplikace, která přijímá promluvy. Výchozí ID je 0.1.
* [.NET Jádro 3.1](https://dotnet.microsoft.com/download)
* [Kód visual studia](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Programová změna modelu

1. Vytvořte novou konzolovou aplikaci, která cílí na `model-with-rest`jazyk C# s názvem projektu a složky .

    ```console
    dotnet new console -lang C# -n model-with-rest
    ```

1. Nainstalujte požadované závislosti pomocí následujících příkazů rozhraní příkazu příkazu příkazu příkazu příkazu dotnet.

    ```console
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```
1. Soubor Program.cs přepište následujícím kódem:

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;

    // 3rd party NuGet packages
    using JsonFormatterPlus;

    namespace AddUtterances
    {
        class Program
        {
            // NOTE: use your LUIS authoring key - 32 character value
            static string authoringKey = "YOUR-KEY";

            // NOTE: Replace this endpoint with your authoring key endpoint
            // for example, your-resource-name.api.cognitive.microsoft.com
            static string endpoint = "YOUR-ENDPOINT";

            // NOTE: Replace this with the ID of your LUIS application
            static string appID = "YOUR-APP-ID";

            // NOTE: Replace this your version number
            static string appVersion = "0.1";

            static string host = String.Format("https://{0}/luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);

            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);

                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }

                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";

                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";

                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
                [
                    {
                    'text': 'go to Seattle today',
                    'intentName': 'BookFlight',
                    'entityLabels': [
                        {
                        'entityName': 'Location::LocationTo',
                        'startCharIndex': 6,
                        'endCharIndex': 12
                        }
                    ]
                    },
                    {
                        'text': 'a barking dog is annoying',
                        'intentName': 'None',
                        'entityLabels': []
                    }
                ]
                ";
                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
        }
    }
    ```

1. Nahraďte hodnoty `YOUR-` začínající vlastními hodnotami.

    |Informace|Účel|
    |--|--|
    |`YOUR-KEY`|Váš 32 znak ový klíč.|
    |`YOUR-ENDPOINT`| Koncový bod adresy URL pro vytváření. Například, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Při vytváření prostředku nastavíte název prostředku.|
    |`YOUR-APP-ID`| ID aplikace LUIS. |

    Přiřazené klíče a prostředky jsou viditelné na portálu LUIS v části Spravovat na stránce **prostředků Azure.** ID aplikace je k dispozici ve stejném oddílu Správa na stránce **Nastavení aplikace.**

1. Sestavte konzolovou aplikaci.

    ```console
    dotnet build
    ```

1. Spusťte konzolovou aplikaci. Výstup konzoly zobrazuje stejný json, který jste viděli dříve v okně prohlížeče.

    ```console
    dotnet run
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu odstraňte soubor ze systému souborů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Doporučené postupy pro aplikaci](../luis-concept-best-practices.md)
