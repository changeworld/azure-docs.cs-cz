---
title: 'Úvodní příručka: Vytvoření aplikace PHP na Linuxu'
description: Začínáme s linuxovými aplikacemi ve službě Azure App Service nasazením první aplikace PHP do kontejneru Linuxu ve službě App Service.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, cli-validatem seodec18
ms.openlocfilehash: 5a2abaf49071c90ea4fe0d5b5a454ce91f2cb1e4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046055"
---
# <a name="create-a-php-app-in-app-service-on-linux"></a>Vytvoření aplikace PHP ve službě App Service na Linuxu

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service v Linuxu. Informace o nasazení ve službě App Service ve _Windows_najdete [v tématu Vytvoření aplikace PHP v Azure](../app-service-web-get-started-php.md).
>

[App Service na Linuxu](app-service-linux-intro.md) poskytuje vysoce škálovatelnou webhostingovou službu s vlastní mizákou pomocí operačního systému Linux. Tento úvodní kurz ukazuje, jak nasadit aplikaci PHP do služby Azure App Service na Linuxu pomocí [cloudového prostředí](https://docs.microsoft.com/azure/cloud-shell/overview).

![Ukázková aplikace spuštěná v Azure](media/quickstart-php/hello-world-in-browser.png)

Postup v tomto článku můžete použít v případě počítačů se systémem Mac, Windows nebo Linux.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
* <a href="https://php.net" target="_blank">Instalace PHP</a>

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu naklonujte spuštěním následujících příkazů ukázkovou aplikaci do místního počítače a přejděte do adresáře, který obsahuje vzorový kód.

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

Spusťte aplikaci místně, abyste viděli, jak by měla vypadat po nasazení do Azure. Otevřete okno terminálu a pomocí příkazu `php` spusťte integrovaný webový server PHP.

```bash
php -S localhost:8080
```

Otevřete webový prohlížeč a přejděte do ukázkové aplikace na adrese `http://localhost:8080`.

Na stránce se zobrazí zpráva **Hello World!** z ukázkové aplikace.

![Ukázková aplikace spuštěná místně](media/quickstart-php/localhost-hello-world-in-browser.png)

V okně terminálu ukončete webový server stisknutím **Ctrl + C**.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

Přejděte na web a podívejte se na nově vytvořenou aplikaci s vestavěnou bitovou kopií. Nahraďte _ &lt;název aplikace>_ názvem aplikace.

```bash
http://<app_name>.azurewebsites.net
```

Zde je to, co vaše nová aplikace by měla vypadat:

![Prázdná stránka aplikace](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```bash
http://<app_name>.azurewebsites.net
```

Ukázkový kód PHP běží v App Service na Linuxu s vestavěnou image.

![Ukázková aplikace spuštěná v Azure](media/quickstart-php/hello-world-in-browser.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci v PHP do služby App Service v Linuxu.

## <a name="update-locally-and-redeploy-the-code"></a>Místní aktualizace a opětovné nasazení kódu

V místním adresáři otevřete soubor `index.php`, který je součástí aplikace v PHP, a proveďte malou změnu textu v řetězci vedle `echo`:

```php
echo "Hello Azure!";
```

Potvrďte změny v Gitu a potom odešlete změny kódu do Azure.

```bash
git commit -am "updated output"
git push azure master
```

Po dokončení nasazení se vraťte do okna prohlížeče, které se otevřelo v kroku **Přechod do aplikace**, a aktualizujte zobrazení stránky.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Přejděte na <a href="https://portal.azure.com" target="_blank">portál Azure</a> a spravujte aplikaci, kterou jste vytvořili.

V levé nabídce klikněte na **App Services**a potom klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

Zobrazí se stránka Přehled aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Stránka služby App Service na webu Azure Portal](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Výuka: PHP aplikace s MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace PHP](configure-language-php.md)
