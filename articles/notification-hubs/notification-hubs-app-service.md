---
title: Integrace center oznámení s mobilními aplikacemi služby App Service
description: Zjistěte, jak Azure Notification Hubs funguje s mobilními aplikacemi Služby Ap.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e6c4fb767e6237f390cdb467b35c323f637bebf2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76264148"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integrace s App Service Mobile Apps

Pro zajištění plynulého a sjednocujícího prostředí napříč službami Azure nabízí [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) integrovanou podporu pro nabízená oznámení prostřednictvím služby Notification Hubs. Služba [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) nabízí vysoce škálovatelnou a globálně dostupnou platformu pro vývoj mobilních aplikací určenou pro vývojáře a integrátory systémů ve velkých firmách. Přináší bohatou sadu funkcí pro vývojáře pro mobilní zařízení.

Vývojáři v Mobile Apps mohou službu Notification Hubs využívat v rámci následujícího pracovního postupu:

1. Načtení popisovače systému PNS zařízení
2. Registrace zařízení ve službě Notification Hubs se provádí pohodlně v registračním rozhraní API sady Mobile Apps Client SDK.

    > [!NOTE]
    > Mějte na paměti, že služba Mobile Apps odstraní z bezpečnostních důvodů při registraci všechny značky. Služba Notification Hubs vám umožní přiřadit značky k zařízením přímo z back-endu.

3. Odesílání oznámení z back-endu aplikace pomocí Notification Hubs

Zde jsou některé výhody, které vývojáři získají díky této integraci:

- **Sady Mobile Apps Client SDK:** Tyto sady jsou univerzální pro všechny platformy. Nabízejí jednoduchá rozhraní API pro registraci a automatickou komunikaci s centrem oznámení automaticky propojeným s mobilní aplikací. Vývojáři se nemusí zabývat přihlašovacími údaji pro Notification Hubs a pracovat s další službou.
  - *Zasílání nabízených oznámení uživatelům:* Sady SDK automaticky označí dané zařízení ověřeným ID funkce Mobile Apps, aby byl možný scénář nabízených oznámení pro uživatele.
  - *Zasílání nabízených oznámení zařízením:* Sady SDK k registraci ve službě Notification Hubs automaticky používají instalační ID funkce Mobile Apps jako identifikátor GUID, aby vývojáři nemuseli spravovat identifikátory GUID různých služeb.
- **Instalační model:** Funkce Mobile Apps pracuje s nejnovějším modelem nabízených oznámení služby Notification Hubs. Zastupuje všechny vlastnosti nabízených oznámení v instalaci JSON spojené se zařízením, aby vyhovovaly Službě nabízených oznámení a daly se snadno použít.
- **Flexibilita:** Vývojáři se vždy mohou rozhodnout, že budou pracovat přímo se službou Notification Hubs, i když je tato služba integrovaná.
- **Integrované prostředí [Azure Portal](https://portal.azure.com)**: Nabízená oznámení jsou v Mobile Apps vizuálně znázorněná jako funkce. Vývojáři mohou prostřednictvím Mobile Apps snadno pracovat s přidruženým centrem oznámení.
