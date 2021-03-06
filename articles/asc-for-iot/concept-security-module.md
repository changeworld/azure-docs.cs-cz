---
title: Bezpečnostní modul a dvojčata zařízení
description: Seznamte se s konceptem dvojčat zabezpečovacích modulů a s tím, jak se používají v Centru zabezpečení Azure pro IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311451"
---
# <a name="security-module"></a>Modul zabezpečení

Tento článek vysvětluje, jak Azure Security Center pro IoT používá dvojčata zařízení a moduly.

## <a name="device-twins"></a>Dvojčata zařízení

U řešení IoT integrovaných v Azure hrají dvojčata zařízení klíčovou roli jak při správě zařízení, tak v automatizaci procesů.

Azure Security Center pro IoT nabízí plnou integraci s vaší stávající platformou pro správu zařízení IoT, což vám umožní spravovat stav zabezpečení zařízení a využívat stávající možnosti řízení zařízení. Integrace je dosaženo využitím mechanismu dvojčete ioT hubu.

Další informace o konceptu [dvojčat zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) v Azure IoT Hub.

## <a name="security-module-twins"></a>Dvojčata bezpečnostních modulů

Azure Security Center pro IoT udržuje dvojče modulu zabezpečení pro každé zařízení ve službě.
Dvojče modulu zabezpečení obsahuje všechny informace týkající se zabezpečení zařízení pro každé konkrétní zařízení ve vašem řešení.
Vlastnosti zabezpečení zařízení jsou udržovány ve vyhrazeném dvojčeti modulu zabezpečení pro bezpečnější komunikaci a pro povolení aktualizací a údržby, které vyžadují méně prostředků.

Informace o tom, jak dvojče tekutá verze vytvořit, přizpůsobit a nakonfigurovat, najdete v tématech [Vytvoření dvojčete modulu](quickstart-create-security-twin.md) zabezpečení a [Konfigurace agentů zabezpečení.](how-to-agent-configuration.md) Další informace o konceptu dvojčat modulů v centru IoT Hub najdete v tématu [Principy dvojčat modulů.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)

## <a name="see-also"></a>Viz také

- [Azure Security Center pro IoT – přehled](overview.md)
- [Nasazení agentů zabezpečení](how-to-deploy-agent.md)
- [Metody ověřování agenta zabezpečení](concept-security-agent-authentication-methods.md)
