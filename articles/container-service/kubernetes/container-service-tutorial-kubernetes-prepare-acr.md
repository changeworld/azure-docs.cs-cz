---
title: (ZASTARALÉ) Kurz služby Azure Container Service – příprava acr
description: Kurz Azure Container Service – Příprava ACR
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 087530fd3834c4ec4620c087134bee0ed26bb6c9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78399782"
---
# <a name="deprecated-deploy-and-use-azure-container-registry"></a>(ZASTARALÉ) Nasazení a použití registru kontejnerů Azure

> [!TIP]
> Aktualizovaná verze tohoto kurzu, který používá službu Azure Kubernetes, [najdete v tématu Kurz: Nasazení a použití registru kontejnerů Azure](../../aks/tutorial-kubernetes-prepare-acr.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Azure Container Registry (ACR) je privátní registr prostředí Azure pro image kontejneru Dockeru. Tento kurz je druhou částí sedmidílné série. Provede vás nasazením instance služby Azure Container Registry a nahráním image kontejneru do ní. Mezi dokončené kroky patří:

> [!div class="checklist"]
> * Nasazení instance služby Azure Container Registry (ACR)
> * Označení image kontejneru pro službu ACR
> * Odeslání image do služby ACR

V následujících kurzech bude tato instance služby ACR integrována do clusteru Kubernetes v Azure Container Service. 

## <a name="before-you-begin"></a>Než začnete

V [předchozím kurzu](./container-service-tutorial-kubernetes-prepare-app.md) byla vytvořena image kontejneru pro jednoduchou hlasovací aplikaci v Azure. Pokud jste image hlasovací aplikace v Azure ještě nevytvořili, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru](./container-service-tutorial-kubernetes-prepare-app.md).

Tento kurz vyžaduje použití Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Nasazení služby Azure Container Registry

Pokud chcete nasadit službu Azure Container Registry, nejprve potřebujete skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). V tomto příkladu se vytvoří skupina prostředků s názvem `myResourceGroup` v oblasti `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Vytvořte registr kontejneru Azure pomocí příkazu [az acr create.](/cli/azure/acr#az-acr-create) Název registru kontejneru **musí být jedinečný**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

V celé zbývající části tohoto kurzu používáme položku `<acrname>` jako zástupný symbol pro název registru kontejneru.

## <a name="container-registry-login"></a>Přihlášení k registru kontejneru

Pomocí příkazu [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) se přihlaste k instanci služby ACR. Je třeba uvést jedinečný název zadaný pro registr kontejneru při jeho vytvoření.

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu Login Succeeded (Přihlášení proběhlo úspěšně).

## <a name="tag-container-images"></a>Označování imagí kontejneru

Seznam aktuálních imagí můžete zobrazit pomocí příkazu [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Výstup:

```output
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Každá image kontejneru musí být označena názvem loginServer registru. Tato značka se používá pro směrování při nahrávání imagí kontejneru do registru imagí.

Pokud chcete zjistit název loginServer, spusťte následující příkaz.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Nyní označte image `azure-vote-front` názvem loginServer registru kontejneru. Na konec názvu image také přidejte řetězec `:v1`. Tato značka označuje verzi image.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Po označení operaci ověřte spuštěním příkazu [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Výstup:

```output
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Nahrávání imagí do registru

Nahrajte image `azure-vote-front` do registru. 

Podle následujícího příkladu nahraďte název loginServer služby ACR názvem loginServer ze svého prostředí.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Tato akce trvá několik minut.

## <a name="list-images-in-registry"></a>Výpis imagí v registru

Pokud chcete vrátit seznam imagí, které byly nahrány do vašeho registru kontejneru Azure, použijte příkaz [az acr repository list](/cli/azure/acr/repository#az-acr-repository-list). Aktualizujte příkaz s použitím názvu instance služby ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Výstup:

```output
Result
----------------
azure-vote-front
```

A pak můžete pomocí příkazu [az acr repository show-tags](/cli/azure/acr/repository) zobrazit značky pro konkrétní image.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Výstup:

```output
Result
--------
v1
```

Na konci kurzu byla image kontejneru uložena v privátní instanci služby Azure Container Registry. Tato image bude nasazena ze služby ACR do clusteru Kubernetes v následných kurzech.

## <a name="next-steps"></a>Další kroky

V tomto kurzu byla služba Azure Container Registry připravena pro použití v clusteru Kubernetes ACS. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Nasazení instance služby Azure Container Registry
> * Označení image kontejneru pro službu ACR
> * Odeslání image do služby ACR

Pokud se chcete naučit nasadit cluster Kubernetes v Azure, přejděte k následujícímu kurzu.

> [!div class="nextstepaction"]
> [Nasazení clusteru Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md)
