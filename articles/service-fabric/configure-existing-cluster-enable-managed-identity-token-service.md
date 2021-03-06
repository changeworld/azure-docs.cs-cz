---
title: Konfigurace podpory spravovaných identit v existujícím clusteru Service Fabric
description: Tady je postup, jak povolit podporu spravovaných identit v existujícím clusteru Azure Service Fabric.
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 396978546b301884087c4ea51e242258d64a6b0b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983802"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Konfigurace podpory spravovaných identit v existujícím clusteru Service Fabric (preview)

Chcete-li používat [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) ve vašich aplikacích Service Fabric, povolte nejprve *službu Token spravované identity* v clusteru. Tato služba je zodpovědná za ověřování aplikací Service Fabric pomocí jejich spravovaných identit a za získání přístupových tokenů jejich jménem. Jakmile je služba povolena, můžete ji vidět v aplikaci Service Fabric Explorer v části **Systém** v levém podokně, která je spuštěna pod názvem **fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> K povolení **služby Managed Identity Token Service Fabric**verze 6.5.658.9590 nebo vyšší je vyžadována runtime service fabric verze 6.5.658.9590 nebo vyšší .  
>
> Verzi service fabric clusteru z portálu Azure najdete tak, že otevřete prostředek clusteru a zkontrolujete vlastnost **Service Fabric verze** v části **Essentials.**
>
> Pokud je cluster v režimu **ručního** upgradu, budete ho muset nejprve upgradovat na 6.5.658.9590 nebo novější.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Povolení *služby tokenů spravované identity* v existujícím clusteru

Chcete-li povolit službu tokenů spravované identity v existujícím clusteru, budete muset zahájit upgrade clusteru určující dvě změny: (1) Povolení služby tokenů spravované identity a (2) požadující restartování každého uzlu. Nejprve přidejte následující úryvek šablony správce prostředků Azure:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

Aby se změny projevily, budete také muset změnit zásady upgradu a určit vynucené restartování runtime Service Fabric v každém uzlu, zatímco upgrade probíhá v clusteru. Toto restartování zajišťuje, že nově povolená systémová služba je spuštěna a spuštěna na každém uzlu. Ve fragmentu níže, `forceRestart` je základní nastavení povolit restartování. Pro zbývající parametry použijte níže popsané hodnoty nebo použijte existující vlastní hodnoty, které již byly zadány pro prostředek clusteru. Vlastní nastavení zásad upgradu prostředků ("upgradeDescription") lze zobrazit z webu Azure Portal výběrem možnosti Upgrady prostředků prostředků nebo resources.azure.com. Výchozí možnosti zásad upgradu ("upgradeDescription") nelze zobrazit z prostředí Powershell nebo resources.azure.com. Další informace naleznete v [tématu ClusterUpgradePolicy.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet)  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Po úspěšném dokončení upgradu nezapomeňte vrátit `forceRestart` zpět nastavení, aby se minimalizoval dopad následných upgradů. 

## <a name="errors-and-troubleshooting"></a>Chyby a řešení problémů

Pokud se nasazení nezdaří s následující zprávou, znamená to, že cluster není spuštěn na dostatečně vysoké verzi Service Fabric:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Další kroky
* [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Využití spravované identity aplikace Service Fabric z kódu služby](./how-to-managed-identity-service-fabric-app-code.md)
* [Udělení přístupu aplikací Azure Service Fabric k dalším prostředkům Azure](./how-to-grant-access-other-resources.md)
