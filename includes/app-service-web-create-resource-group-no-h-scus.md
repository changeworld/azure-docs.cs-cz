---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/18/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 458782011623721bb44771d42caa32130e23bbc9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67175494"
---
[!INCLUDE [resource group intro text](resource-group.md)]

V prostředí cloudu vytvořte skupinu prostředků pomocí příkazu. [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *USA – středojih*. Pokud chcete zobrazit všechna podporovaná umístění pro službu App Service na úrovni **Free**, spusťte příkaz [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Obvykle budete svoji skupinu prostředků a prostředky vytvářet v oblasti, kterou máte blízko. 

Po dokončení příkazu se ve výstupu JSON zobrazí vlastnosti skupiny prostředků.