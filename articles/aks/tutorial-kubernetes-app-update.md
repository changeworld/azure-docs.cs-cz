---
title: Kurz Kubernetes v Azure – Aktualizace aplikace
description: V tomto kurzu Azure Kubernetes Service (AKS) zjistíte, jak aktualizovat existující nasazení aplikace do AKS o novou verzi kódu aplikace.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: d5457d790cd3c95bb23ec0c517097b443a2389ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77593372"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Kurz: Aktualizace aplikace ve službě Azure Kubernetes Service (AKS)

Aplikaci je možné po nasazení v Kubernetes aktualizovat zadáním nové image kontejneru nebo verze image. Aktualizace je připravena tak, aby byla aktualizována pouze část nasazení současně. Tato fázovaná aktualizace umožňuje, aby aplikace během aktualizace běžela. Poskytuje také mechanismus vrácení zpět pro případ, že dojde k selhání nasazení.

V tomto kurzu, který je šestou částí sedmidílné série, se aktualizuje aplikace Azure Vote. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Aktualizace kódu front-endu aplikace
> * Vytvoření aktualizované image kontejneru
> * Odeslání image kontejneru do služby Azure Container Registry
> * Nasazení aktualizované image kontejneru

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech byla aplikace zabalena do bitové kopie kontejneru. Tato bitová kopie byla odeslána do registru kontejnerů Azure a vy jste vytvořili cluster AKS. Aplikace byla poté nasazena do clusteru AKS.

Také se naklonovalo úložiště aplikace, které zahrnuje zdrojový kód aplikace, a předem se vytvořil soubor Docker Compose použitý v tomto kurzu. Ověřte, zda jste vytvořili klon úložiště a zda jste změnili adresáře na klonovaný adresář. Pokud jste tyto kroky nedokončili a chcete je sledovat, začněte s [kurzem 1 – Vytvořte iimages kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, abyste spouštěli Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="update-an-application"></a>Aktualizace aplikace

Teď upravíme ukázkovou aplikaci a pak aktualizujeme verzi, která je už nasazená v clusteru AKS. Ujistěte se, že jste v adresáři klonované *azure-hlasování app-redis.* Ukázkový zdrojový kód aplikace pak najdete v adresáři *azure-vote.* V editoru, jako je například `vi`, otevřete soubor *config_file.cfg*:

```console
vi azure-vote/azure-vote/config_file.cfg
```

Změňte hodnoty *vote1VALUE* a *VOTE2VALUE* na různé hodnoty, například barvy. Následující příklad ukazuje aktualizované hodnoty:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Uložte soubor a zavřete ho. V `vi`, `:wq`použijte .

## <a name="update-the-container-image"></a>Aktualizace image kontejneru

Pokud chcete znovu vytvořit image front-endu a otestovat aktualizovanou aplikaci, použijte příkaz [docker-compose][docker-compose]. Jako instrukce pro Docker Compose, že se má znovu vytvořit image aplikace, se použije argument `--build`:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Test aplikace v místním prostředí

Pokud chcete ověřit, že se v aktualizované imagi kontejneru projevily provedené změny, otevřete místní webový prohlížeč a přejděte na adresu `http://localhost:8080`.

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

Aktualizované hodnoty uvedené v souboru *config_file.cfg* jsou zobrazeny ve spuštěné aplikaci.

## <a name="tag-and-push-the-image"></a>Označení a odeslání image

Abyste mohli aktualizovanou image správně používat, označte image *azure-vote-front* pomocí názvu přihlašovacího serveru vašeho registru ACR. Název přihlašovacího serveru získáte pomocí příkazu [az acr list](/cli/azure/acr):

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

K označení image použijte [docker tag][docker-tag]. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru ACR nebo názvem hostitele veřejného registru a aktualizujte verzi image na *:v2* následujícím způsobem:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Teď pomocí příkazu [docker push][docker-push] nahrajte image do registru. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru ACR.

> [!NOTE]
> Pokud dochází k problémům s odesíláním do registru ACR, ujistěte se, že jste stále přihlášeni. Spusťte příkaz [az acr login][az-acr-login] s názvem registru kontejnerů Azure, který jste vytvořili v kroku [Vytvořit registr kontejnerů Azure.](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry) Například, `az acr login --name <azure container registry name>`.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>Nasazení aktualizované aplikace

Chcete-li zajistit maximální dobu provozu, musí být spuštěno více instancí podu aplikace. Pomocí příkazu [kubectl get pods][kubectl-get] ověřte počet spuštěných instancí front-endu:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Pokud nemáte více front-endových podů, škálujte nasazení *azure-vote-front* takto:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

K aktualizaci aplikace použijte příkaz [kubectl set][kubectl-set]. Jako `<acrLoginServer>` použijte název přihlašovacího serveru nebo hostitele vašeho registru kontejneru a zadejte verzi aplikace *v2*:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

K monitorování nasazení použijte příkaz [kubectl get pod][kubectl-get]. Při nasazení aktualizované aplikace se vaše pody ukončí a vytvoří se znovu s novou imagí kontejneru.

```console
kubectl get pods
```

Následující příklad výstupu ukazuje ukončování podů a spouštění nových instancí v průběhu nasazení:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>Test aktualizované aplikace

Pokud chcete zobrazit aktualizovanou aplikaci, nejprve získejte externí IP adresu služby `azure-vote-front`:

```console
kubectl get service azure-vote-front
```

Nyní otevřete místní webový prohlížeč na IP adresu vaší služby:

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste aktualizovali aplikaci a zavedli tuto aktualizaci do clusteru AKS. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Aktualizace kódu front-endu aplikace
> * Vytvoření aktualizované image kontejneru
> * Odeslání image kontejneru do služby Azure Container Registry
> * Nasazení aktualizované image kontejneru

V dalším kurzu se dozvíte, jak upgradovat cluster AKS na novou verzi Kubernetes.

> [!div class="nextstepaction"]
> [Upgrade Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
