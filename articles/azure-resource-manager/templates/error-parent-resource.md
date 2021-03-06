---
title: Nadřazené chyby prostředků
description: Popisuje, jak vyřešit chyby při práci s nadřazeným prostředkem v šabloně Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: f1847389d60ddf3c6abc70bc3309940c2246084e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154036"
---
# <a name="resolve-errors-for-parent-resources"></a>Řešení chyb pro nadřazené prostředky

Tento článek popisuje chyby, které se mohou stát při nasazování prostředku, který je závislý na nadřazeném prostředku.

## <a name="symptom"></a>Příznak

Při nasazování prostředku, který je podřízený do jiného prostředku, se může zobrazit následující chyba:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Příčina

Pokud je jeden prostředek podřízeným prostředkem jiného prostředku, nadřazený prostředek musí existovat před vytvořením podřízeného prostředku. Název podřízeného prostředku definuje připojení k nadřazenému prostředku. Název podřízeného prostředku je `<parent-resource-name>/<child-resource-name>`ve formátu . Databáze SQL může být například definována jako:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Pokud nasadíte server i databázi ve stejné šabloně, ale nezadejte závislost na serveru, může být nasazení databáze možné spustit před nasazením serveru.

Pokud nadřazený prostředek již existuje a není nasazen ve stejné šabloně, zobrazí se tato chyba, když Správce prostředků nemůže přidružit podřízený prostředek k nadřazenému prostředku. K této chybě může dojít, pokud podřízený prostředek není ve správném formátu nebo podřízený prostředek je nasazen do skupiny prostředků, která se liší od skupiny prostředků pro nadřazený prostředek.

## <a name="solution"></a>Řešení

Chcete-li tuto chybu vyřešit při nadřazené a podřízené prostředky jsou nasazeny ve stejné šabloně, zahrnout závislost.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Chcete-li tuto chybu vyřešit, když byl nadřazený prostředek dříve nasazen v jiné šabloně, nenastavíte závislost. Místo toho nasadit podřízené do stejné skupiny prostředků a zadejte název nadřazeného prostředku.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "sqlServerName": {
      "type": "string"
    },
    "databaseName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Další informace najdete [v tématu Definování pořadí pro nasazení prostředků v šablonách Azure Resource Manager .](define-resource-dependency.md)