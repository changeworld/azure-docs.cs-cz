---
title: Vytvoření úhlové aplikace s rozhraním API Azure Cosmos DB pro MongoDB (část 1)
description: Čtvrtá část série kurzů týkající se vytvoření aplikace MongoDB s Angular a Node postavené na službě Azure Cosmos DB s použitím stejných rozhraní API, jako používáte pro MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: d5ed706a0577423f0a95b8eb5f7a563da74a0e7f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75441172"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Vytvoření úhlové aplikace s rozhraním API Azure Cosmos DB pro MongoDB – vytvoření účtu Cosmos

Tento vícedílný kurz ukazuje, jak vytvořit novou aplikaci napsanou v Souboru Node.js pomocí Express a Angular a pak ji připojit k [účtu Cosmos nakonfigurovanému pomocí rozhraní API Cosmos DB pro MongoDB](mongodb-introduction.md).

Čtvrtá část kurzu vychází z [části 3](tutorial-develop-mongodb-nodejs-part3.md) a zabývá se následujícími úlohami:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků Azure pomocí Azure CLI
> * Vytvoření účtu Cosmos pomocí příkazového příkazového příkazu Azure

## <a name="video-walkthrough"></a>Video s návodem

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Požadavky

Před zahájením této části kurzu se ujistěte, že jste dokončili kroky v [části 3](tutorial-develop-mongodb-nodejs-part3.md) tohoto kurzu. 

V této části kurzu můžete použít Azure Cloud Shell (v internetovém prohlížeči) nebo místně nainstalované [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Tento kurz vás postupně provede jednotlivými kroky vytvoření aplikace. Pokud si chcete stáhnout dokončený projekt, v [úložišti angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) na GitHubu můžete získat hotovou aplikaci.

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Vytvořte účet Azure Cosmos [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create) DB pomocí příkazu.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Místo `<cosmosdb-name>` použijte vlastní jedinečný název účtu služby Azure Cosmos DB. Tento název musí být jedinečný mezi všemi názvy účtů služby Azure Cosmos DB v Azure.
* Nastavení `--kind MongoDB` umožňuje, aby služba Azure Cosmos DB měla klientská připojení MongoDB.

Dokončení příkazu může pár minut trvat. Po dokončení se v okně terminálu zobrazí informace o nové databázi. 

Po vytvoření účtu služby Azure Cosmos DB:
1. Otevřete nové okno prohlížeče a přejděte na[https://portal.azure.com](https://portal.azure.com)
1. Klikněte na logo služby Azure Cosmos DB ![Ikona služby Azure Cosmos DB na webu Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) na levém panelu a zobrazí se všechny účty služby Azure Cosmos DB, které máte.
1. Klikněte na účet služby Azure Cosmos DB, který jste právě vytvořili, vyberte kartu **Přehled** a přejděte dolů k zobrazení mapy s umístěním databáze. 

    ![Nový účet služby Azure Cosmos DB na webu Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. V levém navigačním panelu přejděte dolů a klikněte na kartu **Globální replikace dat**. Zobrazí se mapa s vyznačením různých oblastí, do kterých můžete replikovat. Můžete například kliknout na Austrálie – jihovýchod nebo Austrálie – východ a replikovat svá data do Austrálie. Další informace o globální replikaci najdete v tématu [Globální distribuce dat pomocí služby Azure Cosmos DB](distribute-data-globally.md). Prozatím ponecháme jenom jednu instanci a až budeme chtít replikovat, budeme vědět jak.

    ![Nový účet služby Azure Cosmos DB na webu Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>Další kroky

V této části kurzu jste provedli následující:

> [!div class="checklist"]
> * Vytvořili jste skupinu prostředků Azure pomocí Azure CLI.
> * Vytvořili jste účet služby Azure Cosmos DB pomocí Azure CLI.

Můžete přejít k další části kurzu a připojit službu Azure Cosmos DB ke své aplikaci pomocí Mongoose.

> [!div class="nextstepaction"]
> [Připojení ke službě Azure Cosmos DB pomocí Mongoose](tutorial-develop-mongodb-nodejs-part5.md)
