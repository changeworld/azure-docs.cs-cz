---
title: 'ROZHRANÍ SE křižovatek v pořizovacím rozhraní: Průběžné nasazení z GitHubu'
description: Zjistěte, jak pomocí azure cli automatizovat nasazení a správu aplikace App Service. Tato ukázka ukazuje, jak vytvořit aplikaci s CI/CD z GitHubu.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/02/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 0578ea7f677e1abaa5687295fc7bc871e6db2d3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80044787"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>Vytvoření aplikace služby App Service s nepřetržitým nasazením z GitHubu pomocí rozhraní CLI

Tento ukázkový skript vytvoří aplikaci ve službě App Service s jeho související prostředky a pak nastaví průběžné nasazení z úložiště GitHub. Pro nasazení GitHubu bez průběžného nasazení najdete [v tématu Vytvoření aplikace a nasazení kódu z GitHubu](cli-deploy-github.md). Pro tuto ukázku potřebujete:

* Úložiště GitHub s kódem aplikace, ke kterému máte oprávnění správce. Chcete-li získat automatické sestavení, strukturujte úložiště podle tabulky [Příprava úložiště.](../deploy-continuous-deployment.md#prepare-your-repository)
* [Token PAT](https://help.github.com/articles/creating-an-access-token-for-command-line-use) pro váš účet GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Create an app with continuous deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Vytvoří aplikaci služby App Service. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | Přidruží aplikaci služby App Service k úložišti Git nebo Mercurial. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../samples-cli.md).
