---
title: Export/kopírování virtuálního pevného disku spravovaného disku do účtu úložiště v jiné oblasti – ukázka prostředí PowerShell
description: Ukázkový skript Azure PowerShellu – Export nebo kopírování virtuálního pevného disku spravovaného disku do účtu úložiště ve stejné nebo jiné oblasti
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: 1e144b408de1271607b749d8e54289d0563d4b6e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75463794"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell"></a>Export nebo kopírování virtuálního pevného disku spravovaného disku do účtu úložiště v jiné oblasti pomocí PowerShellu

Tento skript exportuje virtuální pevný disk spravovaného disku do účtu úložiště v jiné oblasti. Nejprve vygeneruje identifikátor URI SAS spravovaného disku a pak pomocí něj zkopíruje základní virtuální pevný disk do účtu úložiště v jiné oblasti. Tento skript můžete použít ke zkopírování spravovaných disků do jiné oblasti za účelem regionálního rozšíření.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vygenerování identifikátoru URI SAS spravovaného disku a zkopírování základního virtuálního pevného disku do účtu úložiště s použitím tohoto identifikátoru URI SAS používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Grant-AzDiskAccess](https://docs.microsoft.com/powershell/module/az.compute/grant-azdiskaccess) | Vygeneruje identifikátor URI SAS spravovaného disku, který se použije ke zkopírování základního virtuálního pevného disku do účtu úložiště. |
| [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/New-AzureStorageContext) | Vytvoří kontext účtu úložiště s použitím názvu a klíče účtu. Tento kontext je možné použít k provádění operací čtení a zápisu v účtu úložiště. |
| [Start-AzureStorageBlobCopy](https://docs.microsoft.com/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Zkopíruje základní virtuální pevný disk snímku do účtu úložiště. |

## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaného disku z virtuálního pevného disku](virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Vytvoření virtuálního počítače ze spravovaného disku](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).