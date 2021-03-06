---
title: Spuštění kontejneru Počítačové zpracování obrazu v instanci kontejnerů Azure
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner Počítačové zpracování obrazu do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ecb0b6a6577161a5349d9d701dd6c116f69411f0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876069"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Nasazení kontejneru Počítačové zpracování obrazu do instancí kontejnerů Azure

Zjistěte, jak nasadit kontejner Kognitivní služby [počítačového vidění](computer-vision-how-to-install-containers.md) do [instancí kontejnerů](https://docs.microsoft.com/azure/container-instances/)Azure . Tento postup ukazuje vytvoření prostředku počítačového zpracování obrazu. Pak budeme diskutovat o vytažení přidružené image kontejneru. Nakonec zvýrazněme schopnost vykonávat orchestraci obou z prohlížeče. Pomocí kontejnerů můžete přesunout pozornost vývojářů od správy infrastruktury místo toho se zaměřením na vývoj aplikací.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru privátního kontejneru

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]