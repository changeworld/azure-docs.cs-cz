---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 07/10/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: b864e5374dcb0bab321109e7b12ee8946adf0c05
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67849519"
---
Vytvořte [webovou aplikaci](../articles/app-service/containers/app-service-linux-intro.md) v plánu služby `myAppServicePlan` App Service. 

V prostředí Cloud Shell můžete [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest) použít příkaz. V následujícím příkladu nahraďte `<app-name>` globálně jedinečným názvem aplikace (platné znaky jsou `a-z`, `0-9` a `-`). Modul runtime je nastavený na `RUBY|2.3`. Chcete-li zobrazit všechny [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest)podporované moduly runtimes, spusťte . 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "RUBY|2.6.2" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "RUBY|2.6.2" --deployment-local-git
```

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Vytvořili jste novou prázdnou webovou aplikaci s povoleným nasazením Gitu.

> [!NOTE]
> Adresa URL vzdáleného úložiště Git se zobrazuje ve vlastnosti `deploymentLocalGitUrl` ve formátu `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Tuto adresu URL si uložte, protože ji budete potřebovat později.
>
