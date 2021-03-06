---
title: Načíst rozhraní API pro operace | Azure Marketplace
description: Načte všechny operace v nabídce nebo získat konkrétní operaci pro zadané operationId.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 93b2ca700a987b86aedfdae55d58540c8ffe84ed
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255868"
---
# <a name="retrieve-operations"></a>Načtení operací

> [!NOTE]
> Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte změny uvedené v [referenčním rozhraní API portálu cloudových partnerů a](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) ujistěte se, že váš kód bude fungovat i po migraci do Centra partnerů.

Načte všechny operace v nabídce nebo získat konkrétní operaci pro zadané operationId. Klient může použít parametry dotazu k filtrování spuštěných operací.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


## <a name="uri-parameters"></a>Parametry identifikátoru URI

|  **Název**          |      **Popis**                                                                                           | **Datový typ** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  id vydavatele       |  Identifikátor vydavatele, například`Contoso`                                                                   |  Řetězec       |
|  offerId           |  Identifikátor nabídky                                                                                              |  Řetězec       |
|  operationId       |  IDENTIFIKÁTOR GUID, který jednoznačně identifikuje operaci v nabídce. OperationId může být načtenpomocí tohoto rozhraní API a je také vrácena v hlavičce HTTP odpovědi pro všechny dlouhotrvající operace, jako je například rozhraní API [nabídky publikování.](./cloud-partner-portal-api-publish-offer.md)  |   Identifikátor GUID   |
|  verze-api       | Nejnovější verze rozhraní API |    Datum      |
|  |  |  |

## <a name="header"></a>Hlavička


|  **Název**          |  **Hodnotu**           |
|  ---------------   | -------------------- |
|  Typ obsahu      | `application/json`   |
|  Autorizace     | `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Příklad těla

### <a name="response"></a>Odpověď

#### <a name="get-operations"></a>Get operace

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>Operace GET

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>Vlastnosti těla odezvy

|  **Název**                    |  **Popis**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | IDENTIFIKÁTOR GUID, který jednoznačně identifikuje operaci                                                       |
|  submissionTyp              | Identifikuje typ operace, která je hlášena pro nabídku, například`Publish/GoLive`      |
|  createdDateTime             | Datum uTC, kdy byla operace vytvořena                                                       |
|  lastActionDateTime          | Čas data času u času u operace byla provedena poslední aktualizace                                       |
|  status                      | Stav operace, a `not started` \| `running` \| `failed` \| `completed`to buď . Stav může být `running` v jednom okamžiku pouze jedna operace. |
|  error                       | Chybová zpráva pro neúspěšné operace                                                               |
|  |  |

### <a name="response-step-properties"></a>Vlastnosti kroku odpovědi

|  **Název**                    |  **Popis**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| odhadTimeFrame | Odhadovaná doba trvání této operace |
| id | Jedinečný identifikátor pro krokový proces |
| description | Popis kroku |
| název kroku | Popisný název kroku |
| status | Stav kroku, a `notStarted` \| `running` \| `failed` \| to buď`completed` |
| cloud-zařízení | Všechna oznámení nebo upozornění zjištěná během kroku. Pole řetězců |
| progressPercentage | Celé číslo od 0 do 100 označující průběh kroku |
| | |

### <a name="response-status-codes"></a>Stavové kódy odpovědi

| **kód**  |   **Popis**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK`- Požadavek byl úspěšně zpracován a požadované operace byly vráceny.        |
|  400      | `Bad/Malformed request`- Tělo odpovědi na chybu může obsahovat více informací.                    |
|  403      | `Forbidden`- Klient nemá přístup k zadanému oboru názvů.                          |
|  404      | `Not found`- Zadaná entita neexistuje.                                                 |
|  |  |
