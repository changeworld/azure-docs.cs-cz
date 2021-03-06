---
title: (ZASTARALÉ) Kurz služby Azure Container Service – nasazení aplikace
description: Kurz Azure Container Service – Nasazení aplikace
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2c2d50da4328ff07c3d2fda4a8721839aa3aa6e7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274059"
---
# <a name="deprecated-run-applications-in-kubernetes"></a>(ZASTARALÉ) Spouštění aplikací v Kubernetes

> [!TIP]
> Aktualizovanou verzi tohoto kurzu, který používá službu Azure Kubernetes, [najdete v tématu Kurz: Spouštění aplikací ve službě Azure Kubernetes Service (AKS).](../../aks/tutorial-kubernetes-deploy-application.md)

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

V tomto kurzu, který je čtvrtou částí sedmidílné série, se ukázková aplikace nasadí do clusteru Kubernetes. Mezi dokončené kroky patří:

> [!div class="checklist"]
> * Aktualizace souborů manifestu Kubernetes
> * Spuštění aplikace v Kubernetes
> * Testování aplikace

V dalších kurzech se tato aplikace bude škálovat a aktualizovat a nakonfiguruje se služba Log Analytics pro monitorování clusteru Kubernetes.

V tomto kurzu se předpokládá základní znalost konceptů Kubernetes. Podrobné informace o Kubernetes najdete v [dokumentaci ke Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do image kontejneru, tato image se odeslala do Azure Container Registry a vytvořil se cluster Kubernetes. 

K dokončení tohoto kurzu potřebujete předem vytvořený soubor manifestu Kubernetes `azure-vote-all-in-one-redis.yml`. Tento soubor se stáhnul se zdrojovým kódem aplikace v předchozí kurzu. Ověřte, že jste naklonovali úložiště a že jste změnili adresáře na klonované úložiště.

Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Aktualizace souboru manifestu

V tomto kurzu se Azure Container Registry (ACR) používá k uložení image kontejneru. Před spuštěním aplikace je potřeba v souboru manifestu Kubernetes aktualizovat název přihlašovacího serveru ACR.

Název přihlašovacího serveru ACR získáte pomocí příkazu [az acr list](/cli/azure/acr#az-acr-list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

V předem vytvořeném souboru manifestu se jako název přihlašovacího serveru používá `microsoft`. Otevřete tento soubor pomocí libovolného textového editoru. V tomto příkladu se soubor otevírá pomocí `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Místo `microsoft` použijte název přihlašovacího serveru ACR. Tuto hodnotu najdete na řádku **47** souboru manifestu.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Uložte soubor a zavřete ho.

## <a name="deploy-application"></a>Nasazení aplikace

Pomocí příkazu [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) spusťte aplikaci. Tento příkaz analyzuje soubor manifestu a vytvoří definované objekty Kubernetes.

```console
kubectl create -f azure-vote-all-in-one-redis.yml
```

Výstup:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Otestování aplikace

Vytvoří se [služba Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/), která zveřejní aplikaci na internetu. Tento proces může trvat několik minut. 

Pomocí příkazu [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) s argumentem `--watch` můžete sledovat průběh.

```console
kubectl get service azure-vote-front --watch
```

Na začátku se bude adresa **EXTERNAL-IP** pro službu `azure-vote-front` zobrazovat ve stavu `pending`. Jakmile se stav adresy EXTERNAL-IP změní ze stavu `pending` na `IP address`, pomocí klávesové zkratky `CTRL-C` zastavte sledovací proces kubectl.

```output
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Pokud se chcete na aplikaci podívat, přejděte na externí IP adresu.

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se aplikace Azure Vote nasadila do clusteru Kubernetes se službou Azure Container Service. Mezi dokončené úlohy patří:  

> [!div class="checklist"]
> * Stažení souborů manifestu Kubernetes
> * Spuštění aplikace v Kubernetes
> * Otestování aplikace

V dalším kurzu se dozvíte, jak škálovat aplikaci Kubernetes i příslušnou infrastrukturu Kubernetes. 

> [!div class="nextstepaction"]
> [Škálování aplikace a infrastruktury Kubernetes](./container-service-tutorial-kubernetes-scale.md)
