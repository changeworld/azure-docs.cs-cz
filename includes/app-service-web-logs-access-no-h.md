---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175483"
---
Můžete přistupovat k protokolům konzoly generovaným z kontejneru. Nejprve zapněte protokolování kontejnerů spuštěním následujícího příkazu v prostředí Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Po zapnutí protokolování kontejnerů zprovozněte datový proud protokolu následujícím příkazem:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

> [!NOTE]
> Můžete také zkontrolovat soubory protokolu z `https://<app-name>.scm.azurewebsites.net/api/logs/docker`prohlížeče na adrese .

Chcete-li kdykoli zastavit streamování `Ctrl` + `C`protokolů, zadejte příkaz .
