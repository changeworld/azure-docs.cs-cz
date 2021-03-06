---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70049010"
---
<!-- put the ## header in the file that includes this file -->

V této části vytvoříte identitu zařízení v registru identit v centru IoT. Zařízení se nemůže připojit k rozbočovači, pokud nemá položku v registru identit. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations).

1. V navigační nabídce služby IoT hub otevřete **zařízení IoT a**pak vyberte **Nový** a přidejte zařízení do služby IoT hub.

    ![Vytvoření identity zařízení na portálu](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. V **části Vytvořit zařízení**zadejte název nového zařízení, například **myDeviceId**, a vyberte **Uložit**. Tato akce vytvoří identitu zařízení pro vaše služby IoT hub.

   ![Přidání nového zařízení](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Vytvořené zařízení otevřete v seznamu v podokně **Zařízení IoT**. Zkopírujte **primární připojovací řetězec,** který chcete použít později.

    ![Připojovací řetězec zařízení](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají pouze identity zařízení za účelem bezpečného přístupu ke službě IoT Hub. Ukládají se zde ID zařízení a jejich klíče, které slouží jako zabezpečené přihlašovací údaje, a příznak povoleno/zakázáno, s jehož pomocí můžete zakázat přístup k jednotlivým zařízením. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
