---
title: Podporované verze clusteru ve službě Azure Service Fabric
description: Další informace o verzích clusteru v Azure Service Fabric, včetně odkazu na nejnovější verze z blogu týmu Service Fabric.
ms.topic: troubleshooting
ms.date: 03/02/2020
ms.openlocfilehash: 1158266857f0864ed2f442edb100032c70063719
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385040"
---
# <a name="supported-service-fabric-versions"></a>Podporované verze service fabric

Ujistěte se, že váš cluster je vždy spuštěna podporovaná verze Azure Service Fabric. Minimálně 60 dní poté, co oznámíme vydání nové verze Service Fabric, podpora pro předchozí verzi končí. Oznámení o nových verzích najdete na [blogu týmu Service Fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Podrobnosti o tom, jak zachovat v clusteru podporovanou verzi Service Fabric, naleznete v následujících dokumentech:

- [Upgrade clusteru Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Upgrade verze Service Fabric, která je spuštěna v samostatném clusteru Windows Server](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Podporované verze

V následující tabulce jsou uvedeny verze service fabric a jejich koncová data podpory.

| Za běhu Service Fabric v clusteru | Lze upgradovat přímo z verze clusteru |Kompatibilní verze balíčku Sady SDK nebo NuGet | Ukončení podpory |
| --- | --- |--- | --- |
| Všechny verze clusteru před 5.3.121 | 5.1.158.* |Menší nebo rovno verzi 2.3 |20. ledna 2017 January 20, 2017 |
| 5.3.* | 5.1.158.* |Menší nebo rovno verzi 2.3 |24. února 2017 February 24, 2017 |
| 5.4.* | 5.1.158.* |Menší nebo rovno verzi 2.4 |10. května 2017       |
| 5.5.* | 5.4.164.* |Menší nebo rovno verzi 2.5 |10. srpna 2017August 10,2017    |
| 5.6.* | 5.4.164.* |Menší nebo rovno verzi 2.6 |13. října 2017October 13,2017   |
| 5.7.* | 5.4.164.* |Menší nebo rovno verzi 2.7 |15. prosince 2017  |
| 6.0.* | 5.6.205.* |Menší nebo rovno verzi 2.8 |30. března 2018     |
| 6.1.* | 5.7.221.* |Menší nebo rovno verzi 3.0 |15. července 2018July 15, 2018      |
| 6.2.* | 6.0.232.* |Menší nebo rovno verzi 3.1 |26. října 2018October 26, 2018   |
| 6.3.* | 6.1.480.* |Menší nebo rovno verzi 3.2 |31. března 2019  |
| 6.4.* | 6.2.301.* |Menší nebo rovno verzi 3.3 |15. září 2019 September 15, 2019 |
| 6.5.* | 6.4.617.* |Menší nebo rovno verzi 3.4 |1. srpna 2020 |
| 7.0.466.* | 6.4.664.* |Menší nebo rovno verzi 4.0|Aktuální verze, takže žádné koncové datum |
| 7.0.466.* | 6.5.* |Menší nebo rovno verzi 4.0|Aktuální verze, takže žádné koncové datum |
| 7.0.470.* | 7.0.466.* |Menší nebo rovno verzi 4.0 |Aktuální verze, takže žádné koncové datum |

## <a name="supported-operating-systems"></a>Podporované operační systémy

V následující tabulce jsou uvedeny podporované operační systémy pro podporované verze service fabric.

| Operační systém | Nejstarší podporovaná verze service fabric |
| --- | --- |
| Windows Server 2012 R2 | Všechny verze |
| Windows Server 2016 | Všechny verze |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>Podporované názvy verzí

V následující tabulce jsou uvedeny názvy verzí service fabric a odpovídající čísla verzí.

| Název verze | Číslo verze systému Windows | Číslo verze Linuxu |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | Není k dispozici |
| 5.3 CU1 | 5.3.204.9494 | Není k dispozici |
| 5.3 CU2 | 5.3.301.9590 | Není k dispozici |
| 5,3 CU3 | 5.3.311.9590 | Není k dispozici |
| 5,4 CU2 | 5.4.164.9494 | Není k dispozici |
| 5,5 CU1 | 5.5.216.0    | Není k dispozici |
| 5,5 CU2 | 5.5.219.0    | Není k dispozici |
| 5,5 CU3 | 5.5.227.0    | Není k dispozici |
| 5,5 CU4 | 5.5.232.0    | Není k dispozici |
| 5.6 RTO | 5.6.204.9494 | Není k dispozici |
| 5,6 CU2 | 5.6.210.9494 | Není k dispozici |
| 5,6 CU3 | 5.6.220.9494 | Není k dispozici |
| 5.7 RTO | 5.7.198.9494 | Není k dispozici |
| 5,7 CU4 | 5.7.221.9494 | Není k dispozici |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | Není k dispozici |
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | Není k dispozici |
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | Není k dispozici |
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | Není k dispozici |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
