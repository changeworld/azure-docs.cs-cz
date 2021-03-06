---
title: 'Kurz: Integrace více zdrojů aplikace Immersive Reader'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci Node.js, která spustí immersive Reader pomocí více prostředků Immersive Reader.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: 3912d55b13f3977818e8d898efa651ffeb1a798a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046272"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>Kurz: Integrace více zdrojů aplikace Immersive Reader

V [přehledu](./overview.md)jste se dozvěděli o tom, co je Immersive Reader a jak implementuje osvědčené techniky ke zlepšení porozumění čtení pro studenty jazyků, začínající čtenáře a studenty s rozdíly v učení. V [node.js rychlý start](./quickstart-nodejs.md), jste se naučili používat immersive Reader s jedním prostředkem. Tento kurz popisuje, jak integrovat více prostředků immersive Reader ve stejné aplikaci. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření více prostředků aplikace Immersive Reader v rámci existující skupiny prostředků
> * Spuštění immersive readeru s využitím více zdrojů

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Pomocí [rychlého startu](./quickstart-nodejs.md) vytvořte webovou aplikaci, která spustí immersive Reader s NodeJS. V tomto rychlém startu nakonfigurujete jeden prostředek immersive reader. Budeme stavět na vrcholu, že v tomto tutoriálu.

## <a name="create-the-immersive-reader-resources"></a>Vytvoření zdrojů aplikace Immersive Reader

Podle [těchto pokynů](./how-to-create-immersive-reader.md) vytvořte každý prostředek aplikace Immersive Reader. Skript **Create-ImmersiveReaderResource** `ResourceName`má `ResourceSubdomain`, `ResourceLocation` a jako parametry. Ty by měly být jedinečné pro každý prostředek, který je vytvořen. Zbývající parametry by měly být stejné jako ty, které jste použili při nastavování prvního prostředku immersive reader. Tímto způsobem každý prostředek lze propojit se stejnou skupinou prostředků Azure a aplikací Azure AD.

Následující příklad ukazuje, jak vytvořit dva prostředky, jeden v WestUS a druhý v EastUS. Všimněte si `ResourceName`jedinečných hodnot pro , `ResourceSubdomain`a `ResourceLocation`.

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Přidání prostředků do konfigurace prostředí

V rychlém startu jste vytvořili konfigurační `ClientId` `ClientSecret`soubor `Subdomain` `TenantId`prostředí, který obsahuje , , a parametry. Vzhledem k tomu, že všechny vaše prostředky používají stejnou aplikaci `ClientId`Azure `ClientSecret`AD, můžeme použít stejné hodnoty pro `TenantId`, a . Jedinou změnou, kterou je třeba provést, je vypsat každou subdoménu pro každý prostředek.

Nový soubor __ENV__ by nyní měl vypadat nějak takto:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Ujistěte se, že tento soubor není potvrzena do správy zdrojového kódu, protože obsahuje tajné klíče, které by neměly být zveřejněny.

Dále upravíme soubor _routes\index.js,_ který jsme vytvořili, aby podporoval naše více prostředků. Nahraďte jeho obsah následujícím kódem.

Stejně jako dříve tento kód vytvoří koncový bod rozhraní API, který získá ověřovací token Azure AD pomocí hesla instančního objektu. Tentokrát umožňuje uživateli zadat umístění prostředku a předat jej jako parametr dotazu. Potom vrátí objekt obsahující token a odpovídající subdoménu.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            },
            url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: process.env.CLIENT_ID,
                client_secret: process.env.CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

Koncový bod rozhraní API **getimmersivereaderlaunchparams** by měl být zabezpečen za nějakou formou ověřování (například [OAuth),](https://oauth.net/2/)aby se zabránilo neoprávněným uživatelům získat tokeny pro použití proti službě Immersive Reader a fakturaci; že práce je nad rámec tohoto kurzu.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Spuštění čtečky Immersive Reader s ukázkovým obsahem

1. Otevřete _zobrazení\index.pug_a nahraďte jeho obsah následujícím kódem. Tento kód naplní stránku s některé ukázkový obsah a přidá dvě tlačítka, která spustí Immersive Reader. Jeden pro spuštění Immersive Reader pro prostředek EastUS a další pro prostředek WestUS.

    ```pug
    doctype html
    html
        head
            title Immersive Reader Quickstart Node.js

            link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

            // A polyfill for Promise is needed for IE11 support.
            script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

            script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
            script(src='https://code.jquery.com/jquery-3.3.1.min.js')

            style(type="text/css").
                .immersive-reader-button {
                background-color: white;
                margin-top: 5px;
                border: 1px solid black;
                float: right;
                }
        body
            div(class="container")
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

                h1(id="ir-title") About Immersive Reader
                div(id="ir-content" lang="en-us")
                p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

                    ul
                        li Shows content in a minimal reading view
                        li Displays pictures of commonly used words
                        li Highlights nouns, verbs, adjectives, and adverbs
                        li Reads your content out loud to you
                        li Translates your content into another language
                        li Breaks down words into syllables

                h3 The Immersive Reader is available in many languages.

                p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
                p(lang="zh-cn") 沉浸式阅读器支持许多语言
                p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
                p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

    script(type="text/javascript").
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
                    // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }

        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    ```

3. Naše webová aplikace je nyní připravena. Spuštění aplikace spuštěním:

    ```bash
    npm start
    ```

4. Otevřete prohlížeč a [http://localhost:3000](http://localhost:3000)přejděte na . Měli byste vidět výše uvedený obsah na stránce. Klikněte buď na tlačítko **EastUS Immersive Reader** nebo **WestUS Immersive Reader** tlačítko pro spuštění Immersive Reader pomocí těchto příslušných zdrojů.

## <a name="next-steps"></a>Další kroky

* Seznamte se s [sadou Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) a [referenční sadou Immersive Reader SDK](./reference.md)
* Zobrazení ukázek kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)