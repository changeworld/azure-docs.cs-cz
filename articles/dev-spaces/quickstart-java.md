---
title: 'Ladění a iteraci na Kubernetes: Visual Studio Kód & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Tento rychlý start ukazuje, jak používat Azure Dev Spaces a Visual Studio Code k ladění a rychlé iteraci java aplikace ve službě Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Java, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
manager: gwallace
ms.openlocfilehash: ac7a1b37b565f3589b7c049a3c1ed2a84972ded0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239737"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Úvodní příručka: Ladění a iterace na Kubernetes s visual studio kód a Java - Azure Dev Spaces

V tomto rychlém startu nastavíte Azure Dev Spaces se spravovaným clusterem Kubernetes a použijete aplikaci Java v kódu Visual Studia k iterativnímu vývoji a ladění kódu v kontejnerech. Azure Dev Spaces umožňuje ladit a testovat všechny součásti vaší aplikace ve službě Azure Kubernetes Service (AKS) s minimálním nastavením vývojového počítače. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Java Development Kit (JDK) 1.8.0+](https://aka.ms/azure-jdks).
- [Maven 3.5.0+](https://maven.apache.org/download.cgi).
- [Visual Studio kód](https://code.visualstudio.com/download).
- [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) a Java [Debugger pro](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) rozšíření Azure Dev Spaces pro visual studio kód.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Je třeba vytvořit cluster AKS v [podporované oblasti][supported-regions]. Následující příkazy vytvoří skupinu prostředků nazvanou *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí `use-dev-spaces` příkazu povolte funkci Dev Spaces v clusteru AKS a postupujte podle pokynů. Následující příkaz povolí funkce Dev Spaces v clusteru *MyAKS* ve skupině *MyResourceGroup* a vytvoří *výchozí* dev prostor.

> [!NOTE]
> Příkaz `use-dev-spaces` také nainstaluje příkaz CLI Azure Dev Spaces, pokud ještě není nainstalován. V prostředí Azure Cloud Shell nelze nainstalovat příkaz cli Azure Dev Spaces.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Získat ukázkový kód aplikace

V tomto článku použijete [ukázkovou aplikaci Azure Dev Spaces](https://github.com/Azure/dev-spaces) k předvedení pomocí Azure Dev Spaces.

Klonovat aplikaci z GitHubu.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Příprava ukázkové aplikace v kódu sady Visual Studio

Otevřete kód visual studia, vyberte **Soubor** a pak **otevřít**, přejděte do *adresáře dev-spaces/samples/java/getting-started/webfrontend* a vyberte **Otevřít**.

Nyní máte projekt *webfrontend* otevřený v kódu sady Visual Studio. Chcete-li spustit aplikaci ve vašem vývojovém prostoru, vygenerujte prostředky grafu Dockeru a Helmu pomocí rozšíření Azure Dev Spaces v paletě příkazů.

Chcete-li otevřít paletu příkazů v kódu sady Visual Studio, vyberte **možnost Zobrazit** a pak **paletu příkazů**. Začněte `Azure Dev Spaces` psát a vyberte **Azure Dev Spaces: Připravte konfigurační soubory pro Azure Dev Spaces**.

![Příprava konfiguračních souborů pro Azure Dev Spaces](./media/common/command-palette.png)

Když Visual Studio Code také vyzve ke konfiguraci základní image, `Azul Zulu OpenJDK for Azure (Free LTS)` vystavený port `8080` a veřejný koncový `Yes` bod, zvolte pro základní image, pro exponovaný port a povolit veřejný koncový bod.

![Vybrat základní obrázek](media/get-started-java/select-base-image.png)

![Vybrat exponovaný port](media/get-started-java/select-exposed-port.png)

![Vybrat veřejný koncový bod](media/get-started-java/select-public-endpoint.png)

Tento příkaz připraví váš projekt ke spuštění v Azure Dev Spaces generováním Dockerfile a Helm grafu. Generuje také adresář *.vscode* s konfigurací ladění v kořenovém adresáři projektu.

> [!TIP]
> [Dockerfile a Helm graf](how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt se používá Azure Dev Spaces k sestavení a spuštění kódu, ale můžete upravit tyto soubory, pokud chcete změnit způsob sestavení a spuštění projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Sestavení a spuštění kódu v Kubernetes z kódu Sady Visual Studio

Vyberte ikonu **Ladění** vlevo a nahoře vyberte **Spustit program Java (AZDS).**

![Spuštění programu Java](media/get-started-java/debug-configuration.png)

Tento příkaz vytvoří a spustí vaši službu v Azure Dev Spaces. Okno **Terminálu** v dolní části zobrazuje výstup sestavení a adresy URL pro vaši službu se systémem Azure Dev Spaces. **Ladicí konzole** zobrazuje výstup protokolu.

> [!Note]
> Pokud v **paletě příkazů příkazy pro příkazy**nevidíte žádné příkazy Azure Dev Spaces , zkontrolujte, jestli jste nainstalovali [rozšíření kódu Visual Studia pro Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Také ověřte, zda jste otevřeli adresář *dev-spaces/samples/java/getting-started/webfrontend* v kódu sady Visual Studio.

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL.

Vyberte **ladění** a **zastavte ladění,** chcete-li ladicí program zastavit.

## <a name="update-code"></a>Aktualizace kódu

Chcete-li nasadit aktualizovanou verzi služby, můžete aktualizovat libovolný soubor v projektu a znovu spustit **program AZDS (Launch Java Program).** Například:

1. Pokud je vaše aplikace stále spuštěná, vyberte **ladění** a **zastavte ladění,** abyste ji zastavili.
1. Aktualizovat [řádek 19 `src/main/java/com/ms/sample/webfrontend/Application.java` na:](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19)
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Uložte provedené změny.
1. Znovu **spustit spuštění programu Java (AZDS)**.
1. Přejděte na spuštěnou službu a sledujte změny.
1. Vyberte **ladění** a **zastavte ladění a** zastavte aplikaci.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážek pro ladění

Spusťte službu pomocí **programu Launch Java Program (AZDS)**. To také spustí službu v režimu ladění.

Přejděte zpět do zobrazení **Průzkumníka** výběrem **možnosti Zobrazit** a pak **Průzkumníka**. Otevřete *src / main / java / com / ms / vzorek / webfrontend / Application.java* a klikněte někde na řádku 19, aby váš kurzor tam. Chcete-li nastavit zarážku, stiskněte **klávesu F9** nebo vyberte **možnost Ladění** **a možnost Přepnout zarážku**.

Otevřete službu v prohlížeči a všimněte si, že se nezobrazí žádná zpráva. Návrat do visual studio kód a sledovat řádek 19 je zvýrazněna. Nastavená zarážka pozastavila službu na řádku 19. Chcete-li službu obnovit, stiskněte **klávesu F5** nebo vyberte **možnost Ladění** a **pokračujte**. Vraťte se do prohlížeče a všimněte si, že se nyní zobrazí zpráva.

Při spuštění služby v Kubernetes s připojeným ladicím programem máte úplný přístup k informacím o ladění, jako je zásobník volání, místní proměnné a informace o výjimkách.

Odstraňte zarážku tím, že kurzor na řádku 19 v *src / main / java / com / ms / vzorek / webfrontend / Application.java* a stisknutím **F9**.

## <a name="update-code-from-visual-studio-code"></a>Aktualizace kódu z kódu sady Visual Studio

Zatímco služba běží v režimu ladění, aktualizujte řádek 19 v *src/main/java/com/ms/sample/webfrontend/Application.java*. Například:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Uložte soubor. Vyberte **ladění** a **potom Restartujte ladění** nebo na **panelu nástrojů Ladění**vyberte tlačítko **Restartovat ladění.**

![Aktualizovat ladění](media/common/debug-action-refresh.png)

Otevřete službu v prohlížeči a všimněte si, že se zobrazí aktualizovaná zpráva.

Namísto opětovného sestavení a opětovného nasazení nové image kontejneru při každém úpravě kódu Azure Dev Spaces postupně překompiluje kód v rámci existujícího kontejneru, aby poskytoval rychlejší smyčku pro úpravy a ladění.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak můžete zjednodušit vývoj spolupráce pomocí práce s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
