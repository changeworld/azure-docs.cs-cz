---
title: Vytvoření spravovaného disku ze souboru VHD v účtu úložiště ve stejném předplatném – ukázka rozhraní CLI
description: Ukázkový skript Azure CLI – Vytvoření spravovaného disku ze souboru VHD v účtu úložiště ve stejném předplatném
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: d15e0519e9ee5e5d1d6832e37c10119e40add890
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75458494"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Vytvoření spravovaného disku ze souboru VHD v účtu úložiště ve stejném předplatném pomocí rozhraní příkazového řádku

Tento skript vytvoří spravovaný disk ze souboru VHD v účtu úložiště ve stejném předplatném. Pomocí tohoto skriptu můžete importovat specializovaný (negeneralizovaný a nepřipravený nástrojem Sysprep) virtuální pevný disk do spravovaného disku s operačním systémem a vytvořit tak virtuální počítač. Případně ho můžete použít k importu datového virtuálního pevného disku do spravovaného datového disku. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření spravovaného disku z virtuálního pevného disku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Vytvoří spravovaný disk s použitím identifikátoru URI virtuálního pevného disku v účtu úložiště ve stejném předplatném. |

## <a name="next-steps"></a>Další kroky

[Vytvoření virtuálního počítače připojením spravovaného disku jako disku s operačním systémem](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače a spravované disky najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
