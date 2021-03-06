---
title: Zpracování výjimek & scénář protokolování chyb
description: Případ reálného použití a scénář pro pokročilé zpracování výjimek a protokolování chyb v Aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: 1bb6e28c9dcae01f3233178706d2a24156fa509a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76902702"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scénář: Zpracování výjimek a protokolování chyb pro aplikace logiky

Tento scénář popisuje, jak můžete rozšířit aplikaci logiky pro lepší podporu zpracování výjimek. Použili jsme případ použití v reálném životě, abychom odpověděli na otázku: "Podporuje Aplikace Azure Logic Apps výjimku a zpracování chyb?"

> [!NOTE]
> Aktuální schéma Azure Logic Apps poskytuje standardní šablonu pro odpovědi na akce. Tato šablona obsahuje interní ověření i odpovědi na chyby vrácené z aplikace rozhraní API.

## <a name="scenario-and-use-case-overview"></a>Přehled scénáře a případu použití

Zde je příběh jako případ použití pro tento scénář: 

Známá zdravotnická organizace nás najala, abychom vyvinuli řešení Azure, které by vytvořilo portál pro pacienty pomocí aplikace Microsoft Dynamics CRM Online. Potřebovali odeslat záznamy o schůzkách mezi portálem pro pacienty aplikace Dynamics CRM Online a službou Salesforce. Byli jsme požádáni, abychom použili standard [HL7 FHIR](https://www.hl7.org/implement/standards/fhir/) pro všechny záznamy pacientů.

Projekt měl dva hlavní požadavky:  

* Metoda protokolování záznamů odeslaných z portálu Dynamics CRM Online
* Způsob zobrazení chyb, ke kterým došlo v rámci pracovního postupu

> [!TIP]
> Video na vysoké úrovni o tomto projektu naleznete v [tématu Integration User Group](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Skupina uživatelů integrace").

## <a name="how-we-solved-the-problem"></a>Jak jsme problém vyřešili

Vybrali jsme [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") jako úložiště pro záznamy protokolu a chyb (Cosmos DB odkazuje na záznamy jako dokumenty). Vzhledem k tomu, že Azure Logic Apps má standardní šablonu pro všechny odpovědi, bychom nemuseli vytvářet vlastní schéma. Mohli bychom vytvořit aplikaci rozhraní API pro **vložení** a **dotazování** pro záznamy chyb i protokolu. Můžeme také definovat schéma pro každého v rámci aplikace rozhraní API.  

Dalším požadavkem bylo vymazat záznamy po určitém datu. Cosmos DB má vlastnost s názvem [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Čas žít") (TTL), která nám umožnila nastavit hodnotu Time to **Live** pro každý záznam nebo kolekci. Tato funkce eliminovala potřebu ručního odstranění záznamů v Cosmos DB.

> [!IMPORTANT]
> K dokončení tohoto kurzu je třeba vytvořit databázi Cosmos DB a dvě kolekce (Protokolování a chyby).

## <a name="create-the-logic-app"></a>Vytvoření aplikace logiky

Prvním krokem je vytvoření aplikace logiky a otevření aplikace v Návrháři aplikací logiky. V tomto příkladu používáme aplikace logiky nadřazený podřízený. Předpokládejme, že jsme již vytvořili nadřazený a budeme vytvářet jednu podřízenou aplikaci logiky.

Vzhledem k tomu, že se chystáme zaznamenat záznam vycházející z aplikace Dynamics CRM Online, začněme nahoře. Musíme použít Request aktivační **událost,** protože nadřazená aplikace logiky aktivuje tento podřízený.

### <a name="logic-app-trigger"></a>Aktivační událost aplikace logiky

Používáme **request** trigger, jak je znázorněno v následujícím příkladu:

``` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

```


## <a name="steps"></a>Kroky

Zdroj (požadavek) záznamu pacienta musíme zaznamenat z portálu Dynamics CRM Online.

1. Musíme získat nový záznam události z aplikace Dynamics CRM Online.

   Aktivační událost přicházející z aplikace CRM nám poskytuje **crm patentid**, **typ záznamu**, nový nebo **aktualizovaný záznam** (nová nebo aktualizovat logickou hodnotu) a **SalesforceId**. **SalesforceId** může mít hodnotu null, protože se používá pouze pro aktualizaci.
   Získáme záznam CRM pomocí CRM **PatientID** a **typu záznamu**.

2. Dále musíme přidat naši aplikaci **InsertLogEntry** aplikace Azure Cosmos DB SQL API, jak je znázorněno tady v Návrháři aplikací logiky.

   **Vložit položku protokolu**

   ![Vložit položku protokolu](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Vložit položku chyby**

   ![Vložit položku protokolu](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Kontrola selhání vytvoření záznamu**

   ![Podmínka](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Zdrojový kód aplikace logiky

> [!NOTE]
> Následující příklady jsou pouze ukázky. Vzhledem k tomu, že tento kurz je založen na implementaci nyní v produkčním prostředí, hodnota **zdrojového uzlu** nemusí zobrazit vlastnosti, které souvisejí s plánováním události.> 

### <a name="logging"></a>protokolování

Následující ukázka kódu aplikace logiky ukazuje, jak zpracovat protokolování.

#### <a name="log-entry"></a>Položka protokolu

Tady je zdrojový kód aplikace logiky pro vložení položky protokolu.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Požadavek protokolu

Zde je zpráva požadavku protokolu zaúčtovaná do aplikace rozhraní API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Odpověď protokolu

Tady je zpráva s odpovědí protokolu z aplikace API.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Nyní se podívejme na kroky zpracování chyb.

### <a name="error-handling"></a>Zpracování chyb

Následující ukázka kódu aplikace logiky ukazuje, jak můžete implementovat zpracování chyb.

#### <a name="create-error-record"></a>Vytvořit záznam chyby

Tady je zdrojový kód aplikace logiky pro vytvoření záznamu chyby.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Vložit chybu do Cosmos DB -- požadavek

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Chyba vložení do služby Cosmos DB --response

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Odpověď na chybu služby Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Vrátit odpověď zpět do nadřazené aplikace logiky

Po získání odpovědi můžete předat odpověď zpět do nadřazené aplikace logiky.

#### <a name="return-success-response-to-parent-logic-app"></a>Vrátit odpověď na úspěch nadřazené aplikace logiky

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Vrátit odpověď na chybu do nadřazené aplikace logiky

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Úložiště cosmos DB a portál

Naše řešení přidalo možnosti s [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Portál pro správu chyb

Chcete-li zobrazit chyby, můžete vytvořit webovou aplikaci MVC pro zobrazení chybových záznamů z Cosmos DB. Operace **Seznam**, **Podrobnosti**, **Úpravy**a **Odstranění** jsou zahrnuty v aktuální verzi.

> [!NOTE]
> Operace úprav: Cosmos DB nahradí celý dokument. Záznamy zobrazené v zobrazení **seznam** a **podrobnosti** jsou pouze ukázky. Nejsou to skutečné záznamy o schůzkách pacientů.

Zde jsou příklady podrobností o naší aplikaci MVC vytvořených pomocí dříve popsaného přístupu.

#### <a name="error-management-list"></a>Seznam správy chyb
![Seznam chyb](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Zobrazení podrobností správy chyb
![Podrobnosti o chybě](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portál pro správu protokolů

Chcete-li zobrazit protokoly, jsme také vytvořili MVC webovou aplikaci. Zde jsou příklady podrobností o naší aplikaci MVC vytvořených pomocí dříve popsaného přístupu.

#### <a name="sample-log-detail-view"></a>Ukázkové zobrazení podrobností protokolu
![Zobrazení podrobností protokolu](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Podrobnosti o aplikaci API

#### <a name="logic-apps-exception-management-api"></a>Rozhraní API pro správu výjimek aplikací logiky

Naše aplikace api pro správu výjimek Azure Logic Apps poskytuje funkce, jak je popsáno zde – existují dva řadiče:

* **ErrorController** vloží záznam chyby (dokument) v kolekci Azure Cosmos DB.
* **Kontrolka protokolu** Vloží záznam protokolu (dokument) do kolekce Azure Cosmos DB.

> [!TIP]
> Oba řadiče `async Task<dynamic>` používají operace, což umožňuje operace vyřešit za běhu, takže můžeme vytvořit schéma Azure Cosmos DB v těle operace. 
> 

Každý dokument v Azure Cosmos DB musí mít jedinečné ID. Používáme `PatientId` a přidáváme časové razítko, které je převedeno na hodnotu časového razítka Unixu (double). Zkrátíme hodnotu odebrat zlomkové hodnoty.

Můžete zobrazit zdrojový kód našeho rozhraní API řadiče chyb z [GitHubu](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Volání rozhraní API z aplikace logiky pomocí následující syntaxe:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Výraz v předchozím kódu vzorku kontroluje *stav Create_NewPatientRecord* **Failed**.

## <a name="summary"></a>Souhrn

* Můžete snadno implementovat protokolování a zpracování chyb v aplikaci logiky.
* Azure Cosmos DB můžete použít jako úložiště pro záznamy protokolů a chyb (dokumenty).
* Pomocí mvc můžete vytvořit portál pro zobrazení záznamů protokolu a chyb.

### <a name="source-code"></a>Zdrojový kód

Zdrojový kód pro aplikaci rozhraní API pro správu výjimek Logic Apps je k dispozici v tomto [úložišti GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Rozhraní API pro správu výjimek aplikace logiky").

## <a name="next-steps"></a>Další kroky

* [Zobrazit další příklady a scénáře aplikace logiky](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Monitorování aplikací logiky](../logic-apps/monitor-logic-apps.md)
* [Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
