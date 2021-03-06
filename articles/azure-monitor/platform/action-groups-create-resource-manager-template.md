---
title: Vytvoření skupin akcí pomocí šablon Správce prostředků
description: Zjistěte, jak vytvořit skupinu akcí pomocí šablony Azure Resource Manager.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 50ad9d57b24fab9ee57c2f9caae8f4c39d2681f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669076"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Vytvoření skupiny akcí pomocí šablony Správce prostředků
Tento článek ukazuje, jak pomocí [šablony Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) ukonfigurace skupin akcí. Pomocí šablon můžete automaticky nastavit skupiny akcí, které lze znovu použít v určitých typech výstrah. Tyto skupiny akcí zajišťují, že všechny správné strany jsou upozorněny při aktivaci výstrahy.

Základní kroky jsou:

1. Vytvořte šablonu jako soubor JSON, která popisuje, jak vytvořit skupinu akcí.

2. Nasaďte šablonu pomocí [libovolné metody nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Nejprve popíšeme, jak vytvořit šablonu Správce prostředků pro skupinu akcí, kde jsou definice akcí pevně zakódovány v šabloně. Za druhé, popisujeme, jak vytvořit šablonu, která bere informace o konfiguraci webhooku jako vstupní parametry při nasazení šablony.

## <a name="resource-manager-templates-for-an-action-group"></a>Šablony Správce prostředků pro skupinu akcí

Chcete-li vytvořit skupinu akcí pomocí šablony Správce prostředků, vytvořte prostředek typu `Microsoft.Insights/actionGroups`. Pak vyplníte všechny související vlastnosti. Zde jsou dvě ukázkové šablony, které vytvářejí skupinu akcí.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [skupinách akcí](../../azure-monitor/platform/action-groups.md).
* Další informace o [výstrahách](alerts-overview.md).
* Přečtěte si, jak přidat [výstrahy pomocí šablony Správce prostředků](../../azure-monitor/platform/alerts-activity-log.md).

