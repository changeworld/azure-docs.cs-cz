---
title: Kurz – aktivace sestavení bitové kopie soukromou aktualizací základního obrazu
description: V tomto kurzu nakonfigurujete úlohu registru kontejneru Azure tak, aby automaticky aktivovala sestavení image kontejneru v cloudu při aktualizaci základní image v jiném privátním registru kontejnerů Azure.
ms.topic: tutorial
ms.date: 01/22/2020
ms.openlocfilehash: e8aae8a91288d470c801dc4d82cfa6b44369d832
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77617699"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Kurz: Automatizace image kontejneru se staví při aktualizaci základní bitové kopie v jiném registru privátního kontejneru 

Úlohy ACR podporují sestavení automatické bitové kopie při [aktualizaci základní bitové kopie](container-registry-tasks-base-images.md)kontejneru , například při opravě operačního systému nebo rozhraní aplikace v jedné ze základních bitových kopií. 

V tomto kurzu se dozvíte, jak vytvořit úlohu ACR, která aktivuje sestavení v cloudu, když je základní bitová kopie kontejneru posunuta do jiného registru kontejnerů Azure. Můžete také zkusit kurz k vytvoření úlohy ACR, která aktivuje sestavení bitové kopie při zasunutí základní bitové kopie do [stejného registru kontejnerů Azure](container-registry-tutorial-base-image-update.md).

V tomto kurzu:

> [!div class="checklist"]
> * Vytvoření základní bitové kopie v základním registru
> * Vytvoření úlohy sestavení aplikace v jiném registru pro sledování základní bitové kopie 
> * Aktualizovat základní image tak, aby aktivovala úlohu image aplikace
> * Zobrazit aktivovanou úlohu
> * Ověřit aktualizovanou image aplikace

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete používat Azure CLI místně, musíte mít nainstalovaný Azure CLI verze **2.0.68** nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku (CLI), přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="prerequisites"></a>Požadavky

### <a name="complete-the-previous-tutorials"></a>Dokončení předchozích kurzů

Tento kurz předpokládá, že jste už dokončili kroky v prvních dvou kurzech série:

* Vytvoření registru kontejneru Azure
* Vytvoření forku ukázkového úložiště
* Klonování ukázkového úložiště
* Vytvoření tokenu PAT GitHubu

Pokud jste tak ještě neučinili, vyplňte před pokračováním následující kurzy:

[Sestavení imagí kontejnerů v cloudu pomocí Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Automatizace sestavení imagí kontejnerů pomocí Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

Kromě registru kontejneru vytvořeného pro předchozí kurzy je třeba vytvořit registr pro uložení základních bitových kopií. Pokud chcete, vytvořte druhý registr v jiném umístění než původní registr.

### <a name="configure-the-environment"></a>Konfigurace prostředí

Tyto proměnné prostředí naplňte hodnotami vhodnými pro vaše prostředí. Tento krok není nezbytně nutný, ale usnadní provádění víceřádkových příkazů Azure CLI v tomto kurzu. Pokud tyto proměnné prostředí nenaplníte, je nutné ručně nahradit každou hodnotu všude tam, kde se zobrazí v ukázkových příkazech.

```azurecli-interactive
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Scénář aktualizace základní image

Tento kurz vás provede scénářem aktualizace základní image. Tento scénář odráží pracovní postup vývoje pro správu základních bitových kopií ve společném registru privátníkontejner při vytváření bitových kopií aplikace v jiných registrech. Základní bitové kopie mohou určit běžné operační systémy a architektury používané týmem nebo dokonce běžné součásti služby.

Například vývojáři, kteří vyvíjejí bitové kopie aplikací ve svých vlastních registrech, mají přístup k sadě základních bitových kopií uchovávaných ve společném základním registru. Základní registr může být v jiné oblasti nebo dokonce geograficky replikován.

[Vzorový kód][code-sample] zahrnuje dva soubory Dockerfile: image aplikace a image, na které je založená. V následujících částech vytvoříte úlohu ACR, která automaticky aktivuje sestavení bitové kopie aplikace, když je nová verze základní bitové kopie posunuta do jiného registru kontejnerů Azure.

* [Dockerfile-app][dockerfile-app]: Jedná se o malou webovou aplikaci Node.js, která vykreslí statickou webovou stránku zobrazující verzi Node.js, na které je založena. Řetězec verze je simulovaný: zobrazuje obsah proměnné prostředí `NODE_VERSION`, která je definovaná v základní imagi.

* [Dockerfile-base][dockerfile-base]: Image, kterou `Dockerfile-app` specifikuje jako svou základní image. Je založená na imagi [Node][base-node] a zahrnuje proměnnou prostředí `NODE_VERSION`.

V následujících částech vytvoříte úlohu, aktualizujete hodnotu `NODE_VERSION` v souboru Dockerfile základní image a potom použijete ACR Tasks k sestavení základní image. Když úloha ACR odešle do registru novou základní image, automaticky aktivuje sestavení image aplikace. Volitelně můžete spustit image kontejneru aplikace místně, abyste se mohli podívat na různé řetězce verze v sestavených imagích.

V tomto kurzu úloha ACR vytvoří a odešle image kontejneru aplikace zadané v souboru Dockerfile. Úlohy ACR mohou také spouštět [vícekrokové úlohy](container-registry-tasks-multi-step.md)pomocí souboru YAML k definování kroků pro sestavení, nabízení a volitelné testování více kontejnerů.

## <a name="build-the-base-image"></a>Sestavit základní image

Začněte vytvořením základní bitové kopie pomocí *rychlého úkolu*Úlohy ACR pomocí [sestavení az acr][az-acr-build]. Jak je popsáno v [prvním kurzu](container-registry-tutorial-quick-task.md) série, tímto postupem se nejen sestaví image, ale v případě úspěšného sestavení se odešle do registru kontejneru. V tomto příkladu je bitová kopie posunuta do registru základní bitové kopie.

```azurecli-interactive
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Vytvoření úkolu pro sledování soukromé základní bitové kopie

Dále vytvořte úlohu v registru bitových obrázků aplikace s [vytvořením úlohy az acr][az-acr-task-create], která umožňuje [spravovanou identitu](container-registry-tasks-authentication-managed-identity.md). Spravovaná identita se používá v pozdějších krocích, aby se úloha ověřuje pomocí registru základní bitové kopie. 

Tento příklad používá systémem přiřazenou identitu, ale můžete vytvořit a povolit spravovanou identitu přiřazenou uživatelem pro určité scénáře. Podrobnosti najdete [v tématu ověřování mezi registry v úloze ACR pomocí identity spravované Azure](container-registry-tasks-cross-registry-authentication.md).

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```


Tento úkol je podobný úkolu vytvořenému v [předchozím kurzu](container-registry-tutorial-build-task.md). Dává službě ACR Tasks pokyn aktivovat sestavení image, když se do úložiště určeného parametrem `--context` odešlou potvrzení. Zatímco Dockerfile slouží k vytvoření bitové kopie v předchozím`FROM node:9-alpine`kurzu určuje image veřejné základny ( ), Dockerfile v této úloze, [Dockerfile-app][dockerfile-app], určuje základní image v registru základní image:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Tato konfigurace usnadňuje simulaci opravy architektury v základní bitové kopii dále v tomto kurzu.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Udělení oprávnění k vyžádat identitu základnímu registru

Chcete-li udělit oprávnění spravované identity úlohy k vyproštění bitových kopií z registru základní bitové kopie, [nejprve spusťte az acr task show,][az-acr-task-show] abyste získali ID instančního objektu identity. Potom spusťte [az acr show,][az-acr-show] abyste získali ID prostředku základního registru:

```azurecli-interactive
# Get service principal ID of the task
principalID=$(az acr task show --name taskhelloworld --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Přiřazení oprávnění k vyžádat spravovanou identitu registru spuštěním [vytvoření přiřazení role az][az-role-assignment-create]:

```azurecli-interactive
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>Přidání cílových pověření registru k úkolu

Spusťte [az acr úkol pověření přidat][az-acr-task-credential-add] přidat pověření k úloze. Předajte `--use-identity [system]` parametr, který označuje, že systémově přiřazená spravovaná identita úkolu může přistupovat k pověřením.

```azurecli-interactive
az acr task credential add \
  --name taskhelloworld \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>Ruční spuštění úlohy

Pomocí [spuštění úlohy az acr][az-acr-task-run] můžete ručně spustit úlohu a vytvořit bitovou kopii aplikace. Tento krok je potřeba tak, aby úloha sleduje závislost image aplikace na základní bitové kopii.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Jakmile se úloha dokončí, poznamenejte si **ID spuštění** (například „da6“), pokud chcete provést následující volitelný krok.

### <a name="optional-run-application-container-locally"></a>Volitelné: Spuštění kontejneru aplikace místně

Pokud pracujete místně (nejste v Cloud Shellu) a máte nainstalovaný Docker, spusťte kontejner, abyste zobrazili aplikaci vykreslenou ve webovém prohlížeči dříve, než opětovně sestavíte její základní image. Pokud Cloud Shell používáte, tuto část přeskočte (Cloud Shell nepodporuje `az acr login` ani `docker run`).

Nejprve se ověřte do registru kontejnerů pomocí [přihlášení az acr][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Teď pomocí `docker run` spusťte kontejner místně. Nahraďte ** \<run-id\> ** s ID běhu, které se nachází ve výstupu z předchozího kroku (například "da6"). Tento příklad pojmenuje `myapp` kontejner `--rm` a obsahuje parametr odebrat kontejner u jeho zastavení.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

V prohlížeči přejděte na `http://localhost:8080`. Měli byste vidět číslo verze Node.js vykreslené na webové stránce. Mělo by vypadat takto. V dalším kroku změníte verzi tak, že do řetězce verze přidáte znak „a“.

![Snímek obrazovky ukázkové aplikace vykreslené v prohlížeči][base-update-01]

Chcete-li kontejner zastavit a vyjmout, spusťte následující příkaz:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Výpis sestavení

Dále pomocí příkazu [az acr task list-runs][az-acr-task-list-runs] vypište spuštění úloh, která služba ACR Tasks dokončila pro váš registr:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Pokud jste dokončili předchozí kurz (a nevymazali jste registr), měli byste vidět výstup podobný následujícímu. Poznamenejte si počet spuštění úloh a ID nejnovějšího spuštění, abyste je mohli porovnat s výstupem po aktualizaci základní image, kterou provedete v další části.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>Aktualizace základní image

V tomto kroku budete simulovat opravu architektury v základní imagi. Upravte **Dockerfile-base** a za číslo verze definované v `NODE_VERSION` přidejte znak „a“:

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

Spusťte rychlou úlohu, která sestaví upravenou základní image. Poznamenejte si **ID spuštění** uvedené ve výstupu.

```azurecli-interactive
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
```

Jakmile se sestavení dokončí a úloha ACR odešle novou základní image do registru, aktivuje se sestavení image aplikace. Než úloha, kterou jste vytvořili dříve, aktivuje sestavení image aplikace, může to chvíli trvat, protože musí zjistit nově sestavenou a odeslanou základní image.

## <a name="list-updated-build"></a>Výpis aktualizovaného sestavení

Teď, když jste aktualizovali základní image, znovu zobrazte seznam spuštění úloh a porovnejte ho s předchozím seznamem. Pokud se napoprvé výstup neliší, opakovaně spouštějte příkaz, aby se nové spuštění úlohy v seznamu zobrazilo.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Výstup je podobný tomuto. Triggerem pro poslední spuštěné sestavení by měl být „Image Update“ značící, že se úloha zahájila rychlou úlohou základní image.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

Pokud chcete provést následující volitelný krok, kterým je spuštění nově sestaveného kontejneru, abyste se mohli podívat na aktualizované číslo verze, poznamenejte si hodnotu **ID spuštění** pro sestavení aktivované pomocí Image Update (v předchozím výstupu to bylo „da8“).

### <a name="optional-run-newly-built-image"></a>Volitelné: Spuštění nově sestavené image

Pokud pracujete místně (nejste v Cloud Shellu) a máte nainstalovaný Docker, spusťte novou image aplikace, jakmile se její sestavení dokončí. `<run-id>` nahraďte hodnotou ID spuštění, kterou jste získali v předchozím kroku. Pokud Cloud Shell používáte, tuto část přeskočte (Cloud Shell nepodporuje `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

V prohlížeči přejděte na http://localhost:8081. Měli byste vidět aktualizované číslo verze Node.js (se znakem „a“) na webové stránce:

![Snímek obrazovky ukázkové aplikace vykreslené v prohlížeči][base-update-02]

Důležité je uvědomit si, že jste aktualizovali **základní** image s novým číslem verze, ale novou verzi zobrazuje poslední sestavená image **aplikace**. Služba ACR Tasks převzala změnu základní image a automaticky znovu sestavila image aplikace.

Chcete-li kontejner zastavit a vyjmout, spusťte následující příkaz:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí úlohy automaticky aktivovat sestavení imagí kontejnerů při aktualizaci základní image. Nyní přejděte k dalšímu kurzu a zjistěte, jak aktivovat úlohy podle definovaného plánu.

> [!div class="nextstepaction"]
> [Spuštění úlohy podle plánu](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
