---
title: Jako příklad aktivní geograficky replikační databáze Azure SQL Database je aktivní funkce AKTIVACE
description: Ukázkový skript Azure CLI pro nastavení aktivní geografické replikace pro jednu databázi v Azure SQL Database a převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3bc6cbe025854286b0a24a34240396eea16292b6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067410"
---
# <a name="use-cli-to-configure-active-geo-replication-for-a-single-database-in-azure-sql-database"></a>Konfigurace aktivní geografické replikace pro jednu databázi v Azure SQL Database pomocí rozhraní příkazového příkazového příkazu

Tento příklad skriptu Azure CLI konfiguruje aktivní geografickou replikaci pro jednu databázi a převezme ji do sekundární repliky databáze.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-single-database.sh "Set up active geo-replication for single database")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní spojené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Odkaz na vzorek

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| | |
|---|---|
| [az sql db replika](/cli/azure/sql/db/replica) | Příkazy repliky databáze. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).