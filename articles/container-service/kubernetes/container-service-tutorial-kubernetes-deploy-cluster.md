---
title: (ZASTARALÉ) Kurz služby Kontejnerová služba Azure – nasazení clusteru
description: Kurz Azure Container Service – Nasazení clusteru
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b8821f3bb3d48786697cbc4137baf530856774fd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274005"
---
# <a name="deprecated-deploy-a-kubernetes-cluster-in-azure-container-service"></a>(ZASTARALÉ) Nasazení clusteru Kubernetes ve službě Azure Container Service

> [!TIP]
> Aktualizovaná verze tohoto kurzu, který používá službu Azure Kubernetes, [najdete v tématu Kurz: Nasazení clusteru služby Azure Kubernetes Service (AKS).](../../aks/tutorial-kubernetes-deploy-cluster.md)

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Kubernetes poskytuje distribuovanou platformu pro kontejnerizované aplikace. Se službou Azure Container Service je zřízení clusteru Kubernetes připraveného pro produkční prostředí snadné a rychlé. V tomto kurzu, který je třetí částí sedmidílné série, se nasadí cluster Kubernetes ve službě Azure Container Service. Mezi dokončené kroky patří:

> [!div class="checklist"]
> * Nasazení clusteru Kubernetes ACS
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl

V dalších kurzech se aplikace Azure Vote nasadí do clusteru a provede škálování a aktualizaci a služba Log Analytics se nakonfiguruje pro monitorování clusteru Kubernetes.

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se vytvořila image kontejneru a nahrála se do instance služby Azure Container Registry. Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes

Vytvořte cluster Kubernetes ve službě Azure Container Service pomocí příkazu [az acs create](/cli/azure/acs#az-acs-create). 

Následující příklad vytvoří cluster s názvem `myK8sCluster` ve skupině prostředků s názvem `myResourceGroup`. Tato skupina prostředků byla vytvořena v [předchozím kurzu](./container-service-tutorial-kubernetes-prepare-acr.md).

```azurecli-interactive
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

V některých případech, například s omezenou zkušební verzí, má předplatné Azure omezený přístup k prostředkům Azure. Pokud se nasazení nezdaří kvůli omezenému počtu dostupných jader, snižte výchozí počet agentů přidáním možnosti `--agent-count 1` do příkazu [az acs create](/cli/azure/acs#az-acs-create). 

Po několika minutách se nasazení dokončí a vrátí informace o nasazení ACS ve formátu JSON.

## <a name="install-the-kubectl-cli"></a>Instalace rozhraní příkazového řádku kubectl

Pokud se chcete připojit ke clusteru Kubernetes z klientského počítače, použijte klienta příkazového řádku Kubernetes [kubectl](https://kubernetes.io/docs/user-guide/kubectl/). 

Pokud používáte Azure Cloud Shell, kubectl je už nainstalován. Pokud ho chcete nainstalovat místně, použijte příkaz [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

Pokud používáte Linux nebo macOS, budete možné ke spuštění muset použít sudo. V systému Windows se ujistěte, že vaše prostředí bylo spuštěné jako správce.

```azurecli-interactive
az acs kubernetes install-cli 
```

Výchozí instalace v systému Windows je *c:\program files (x86)\kubectl.exe*. Pravděpodobně bude nutné přidat tento soubor do cesty Windows. 

## <a name="connect-with-kubectl"></a>Připojení přes kubectl

Abyste nakonfigurovali kubectl pro připojení ke svému clusteru Kubernetes, spusťte příkaz [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes).

```azurecli-interactive
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Chcete-li ověřit připojení ke clusteru, spusťte příkaz [kubectl get nodes.](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get)

```console
kubectl get nodes
```

Výstup:

```output
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Po dokončení tohoto kurzu máte cluster ACS Kubernetes připravený pro úlohy. V následujících kurzech se do tohoto clusteru nasadí vícekontejnerová aplikace, horizontálně se navýší její kapacita, aktualizuje se a sleduje.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se nasadil cluster Kubernetes ve službě Azure Container Service. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Nasazení clusteru Kubernetes ACS
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl

Přejděte k dalšímu kurzu, kde se seznámíte se spuštěním aplikace v tomto clusteru.

> [!div class="nextstepaction"]
> [Nasazení aplikace v Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
