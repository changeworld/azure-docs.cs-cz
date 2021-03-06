---
title: Přesunutí webových rozhraní API volajících webová rozhraní API do produkčního prostředí – platforma identit Microsoftu | Azure
description: Zjistěte, jak přesunout webové rozhraní API, které volá webová rozhraní API do produkčního prostředí.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d557c70c09824f3aead41a654dc92c4ec46e9238
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885051"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Webové rozhraní API, které volá webová rozhraní API: Přechod do produkčního prostředí

Po získání tokenu pro volání webových api můžete aplikaci přesunout do produkčního prostředí.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Další informace

Teď, když znáte základy volání webových rozhraní API z vlastního webového rozhraní API, může vás zajímat následující kurz, který popisuje kód, který se používá k vytvoření chráněného webového rozhraní API, které volá webová rozhraní API.

| Ukázka | Platforma | Popis |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API, desktop (WPF) | ASP.NET webové rozhraní API Core 2.2 volá Microsoft Graph, který voláte z aplikace WPF pomocí platformy identit microsoftu (v2.0). |
