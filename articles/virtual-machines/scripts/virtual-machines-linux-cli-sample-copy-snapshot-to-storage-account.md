---
title: Zkopírování snímku do účtu úložiště v jiné oblasti – ukázka vyjednané zúčtovacích úsecích
description: Ukázka skriptu Azure CLI – export/kopírování snímku jako virtuálního pevného disku do účtu úložiště ve stejné nebo jiné oblasti.
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
ms.custom: mvc,seodec18
ms.openlocfilehash: 01d3b17ab2623570e086b0edea17ff97c8fe8ae8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75458519"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>Export/kopírování snímku do účtu úložiště v jiné oblasti pomocí příkazového příkazového příkazu

Tento skript exportuje spravovaný snímek do účtu úložiště v jiné oblasti. Nejprve vygeneruje identifikátor URI SAS snímku a pak pomocí něj zkopíruje snímek do účtu úložiště v jiné oblasti. Tento skript můžete použít k udržování zálohy spravovaných disků v jiné oblasti pro účely zotavení po havárii. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vygenerování identifikátoru URI SAS pro spravovaný snímek a zkopírování snímku do účtu úložiště s použitím tohoto identifikátoru URI SAS používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot) | Vygeneruje sdílený přístupový podpis jen pro čtení, který se použije ke zkopírování základního souboru VHD do účtu úložiště nebo jeho stažení do místního prostředí.  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Asynchronně zkopíruje objekt blob z jednoho účtu úložiště do jiného. |

## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaného disku z virtuálního pevného disku](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Vytvoření virtuálního počítače ze spravovaného disku](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače a spravované disky najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
