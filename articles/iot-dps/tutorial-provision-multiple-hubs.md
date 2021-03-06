---
title: Kurz – zřízení zařízení napříč centry s vyrovnáváním zatížení pomocí služby Azure IoT Hub Device Provisioning Service
description: Tento kurz ukazuje, jak služba Zřizování zařízení (DPS) umožňuje automatické zřizování zařízení napříč centry IoT s vyrovnáváním zatížení na Webu Azure Portal.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: e1a66da52eea6d5da711fa6def58eba65d0960ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74976736"
---
# <a name="tutorial-provision-devices-across-load-balanced-iot-hubs"></a>Kurz: Zřízení zařízení napříč centry IoT s vyrovnáváním zatížení

Tento kurz ukazuje, jak zřídit zařízení pro několik center IoT s vyrovnáváním zatížení pomocí služby Device Provisioning. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použití webu Azure Portal ke zřízení druhého zařízení ve druhém centru IoT 
> * Přidat položku seznamu registrací do druhého zařízení
> * Nastavit zásady přidělování služby Device Provisioning na **rovnoměrnou distribuci**
> * Propojit nové centrum IoT se službou Device Provisioning

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Tento kurz je založený na předchozím kurzu týkajícím se [zřízení zařízení v centru](tutorial-provision-device-to-hub.md).

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Použití webu Azure Portal ke zřízení druhého zařízení ve druhém centru IoT

Postupujte podle kroků v kurzu týkajícím se [zřízení zařízení v centru](tutorial-provision-device-to-hub.md) a zřiďte druhé zařízení v jiném centru IoT.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Přidat položku seznamu registrací do druhého zařízení

Seznam registrací informuje službu Device Provisioning o tom, jaká metoda ověření identity (metoda potvrzení identity zařízení) se u zařízení používá. Dalším krokem je přidání položky seznamu registrací pro druhé zařízení. 

1. Na stránce vaší služby Device Provisioning klikněte na **Spravovat registrace**. Zobrazí se stránka **Přidat položku seznamu registrací**. 
2. V horní části stránky klikněte na **Přidat**.
2. Vyplňte pole a pak klikněte na **Uložit**.

## <a name="set-the-device-provisioning-service-allocation-policy"></a>Nastavení zásad přidělování služby Device Provisioning

Zásady přidělování jsou nastavením služby Device Provisioning, které určuje způsob přiřazování zařízení k centru IoT. Existují tři podporované zásady přidělování: 

1. **Nejnižší latence:** Zařízení se zřizují v centru IoT, které má se zařízením nejnižší latenci.
2. **Rovnoměrně vážená distribuce (výchozí):** Zařízení se zřizují se stejnou pravděpodobností ve všech propojených centrech IoT. Toto je výchozí nastavení. Pokud zřizujete zařízení pouze v jednom centru IoT, můžete nechat toto nastavení. 
3. **Statická konfigurace prostřednictvím seznamu registrací:** Specifikace požadovaného centra IoT v seznamu registrací má přednost před zásadami přidělování na úrovni služby Device Provisioning.

Postupujte podle těchto kroků k nastavení zásad přidělování:

1. Pokud chcete nastavit zásady přidělování, na stránce služby Device Provisioning klikněte na **Spravovat zásady přidělování**.
2. Nastavte zásady přidělování na **Rovnoměrně vážená distribuce**.
3. Klikněte na **Uložit**.

## <a name="link-the-new-iot-hub-to-the-device-provisioning-service"></a>Propojit nové centrum IoT se službou Device Provisioning

Propojte službu Device Provisioning a centrum IoT, aby služba Device Provisioning mohla do daného centra registrovat zařízení.

1. Na stránce **Všechny prostředky** klikněte na službu Device Provisioning, kterou jste vytvořili dříve.
2. Na stránce služby Device Provisioning klikněte na **Propojená centra IoT**.
3. Klikněte na **Přidat**.
4. Na stránce **Přidat propojení s centrem IoT** pomocí přepínačů určete, jestli je propojené centrum IoT umístěné v aktuálním předplatném nebo v jiném předplatném. Pak v poli **Centrum IoT** zvolte název centra IoT.
5. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Použití webu Azure Portal ke zřízení druhého zařízení ve druhém centru IoT 
> * Přidat položku seznamu registrací do druhého zařízení
> * Nastavit zásady přidělování služby Device Provisioning na **rovnoměrnou distribuci**
> * Propojit nové centrum IoT se službou Device Provisioning

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps]()
-->
