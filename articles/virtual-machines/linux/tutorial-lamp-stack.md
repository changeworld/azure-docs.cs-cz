---
title: Výuka – nasazení lampy na virtuálním počítači s Linuxem v Azure
description: V tomto kurzu zjistíte, jak nainstalovat stack LAMP na virtuální počítač s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 2a636ae5609d9cb5c81782af5a419a27d7880106
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80154368"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Kurz: Instalace webového serveru LAMP na virtuální počítač s Linuxem v Azure

Tento článek vás provede nasazením webového serveru Apache, MySQL a PHP (stack LAMP) na virtuální počítač s Ubuntu v Azure. Pokud dáváte přednost webovému serveru NGINX, projděte si kurz věnovaný [stacku LEMP](tutorial-lemp-stack.md). Pokud chcete zobrazit server LAMP v akci, můžete volitelně nainstalovat a nakonfigurovat web WordPress. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Ubuntu (písmeno L ve stacku LAMP)
> * Otevření portu 80 pro webový provoz
> * Instalace Apache, MySQL a PHP
> * Ověření instalace a konfigurace
> * Instalace WordPressu na server LAMP

Toto nastavení je určené pro rychlé testy nebo testování konceptu. Další informace o stacku LAMP, včetně doporučení pro produkční prostředí, najdete v [dokumentaci k Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

Tento kurz používá vynesené mezizaviny příkazového příkazové číslo v rámci [prostředí Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), které se neustále aktualizuje na nejnovější verzi. Chcete-li otevřít prostředí Cloud Shell, vyberte **Vyzkoušet** v horní části libovolného bloku kódu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Instalace Apache, MySQL a PHP

Spuštěním následujícího příkazu aktualizujte zdroje balíčků Ubuntu a nainstalujte Apache, MySQL a PHP. Všimněte si stříšky (^) na konci příkazu, která je součástí názvu balíčku `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Zobrazí se výzva k instalaci balíčků a dalších závislostí. Tímto postupem se nainstalují minimální požadovaná rozšíření PHP potřebná k používání PHP a MySQL.  

## <a name="verify-installation-and-configuration"></a>Ověření instalace a konfigurace


### <a name="verify-apache"></a>Ověřit Apache

Zkontrolujte verzi Apache pomocí následujícího příkazu:
```bash
apache2 -v
```

Když je teď server Apache nainstalovaný a port 80 k virtuálnímu počítači otevřený, webový server je přístupný z internetu. Pokud chcete zobrazit výchozí stránku Apache2 v Ubuntu, otevřete webový prohlížeč a zadejte veřejnou IP adresu virtuálního počítače. Použijte veřejnou IP adresu, kterou jste použili k připojení přes SSH k virtuálnímu počítači:

![Výchozí stránka Apache][3]


### <a name="verify-and-secure-mysql"></a>Ověřit a zabezpečit MySQL

Zkontrolujte verzi MySQL pomocí následujícího příkazu (všimněte si parametru velké `V`):

```bash
mysql -V
```

Chcete-li pomoci zabezpečit instalaci MySQL, včetně `mysql_secure_installation` nastavení kořenového hesla, spusťte skript. 

```bash
sudo mysql_secure_installation
```

Volitelně můžete nastavit modul plug-in ověření hesla (doporučeno). Poté nastavte heslo pro uživatele kořenového adresáře MySQL a nakonfigurujte zbývající nastavení zabezpečení pro vaše prostředí. Doporučujeme odpovědět na všechny otázky "Y" (ano).

Pokud chcete vyzkoušet funkce MySQL (vytvoření databáze MySQL, přidání uživatelů nebo změna nastavení konfigurace), přihlaste se k MySQL. Tento krok není nezbytný k dokončení kurzu.

```bash
sudo mysql -u root -p
```

Jakmile budete hotovi, ukončete příkazový řádek mysql zadáním `\q`.

### <a name="verify-php"></a>Ověření PHP

Zkontrolujte verzi PHP pomocí následujícího příkazu:

```bash
php -v
```

Pokud chcete podrobnější test, vytvořte jednoduchou informační stránku PHP a zobrazte ji v prohlížeči. Následující příkaz vytvoří informační stránku PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Teď můžete zkontrolovat informační stránku PHP, kterou jste vytvořili. Otevřete prohlížeč a přejděte na adresu `http://yourPublicIPAddress/info.php`. Použijte veřejnou IP adresu svého virtuálního počítače. Informační stránka by měla vypadat podobně jako na tomto obrázku.

![Informační stránka PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili server LAMP v Azure. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Ubuntu
> * Otevření portu 80 pro webový provoz
> * Instalace Apache, MySQL a PHP
> * Ověření instalace a konfigurace
> * Instalace WordPressu na server LAMP

Přejdete k dalšímu kurzu, kde se dozvíte, jak zabezpečit webové servery pomocí certifikátů TLS/SSL.

> [!div class="nextstepaction"]
> [Zabezpečený webový server s TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
