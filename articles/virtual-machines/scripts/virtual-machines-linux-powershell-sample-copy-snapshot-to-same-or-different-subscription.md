---
title: Kopírování snímku spravovaného disku do předplatného – ukázka prostředí PowerShell
description: Ukázka skriptu Azure PowerShell – kopírování (nebo přesunutí) snímku spravovaného disku na stejné nebo jiné předplatné
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
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 65a3e39206864f10c41e79ba6b3e7a89da99dc6f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75463771"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopírování snímku spravovaného disku do stejného nebo jiného předplatného pomocí PowerShellu

Tento skript zkopíruje snímek spravovaného disku do stejného nebo jiného předplatného. Tento skript použijte pro následující scénáře:

1. Migrujte snímek v úložišti Premium (Premium_LRS) do standardního úložiště (Standard_LRS nebo Standard_ZRS) a snižte náklady.
1. Migrujte snímek z místně redundantního úložiště (Premium_LRS, Standard_LRS) do zónově redundantního úložiště (Standard_ZRS) a využijte vyšší spolehlivost úložiště ZRS.
1. Přesuňte snímek do jiného předplatného ve stejné oblasti pro delší uchovávání informací.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření snímku v cílovém předplatném pomocí ID zdrojového snímku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Nový-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Vytvoří konfiguraci snímku, která se použije při vytvoření snímku. Bude obsahovat ID prostředku nadřazeného snímku a stejné umístění, jako má nadřazený snímek.  |
| [Nový-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Vytvoří snímek s použitím konfigurace snímku, názvu snímku a názvu skupiny prostředků, které se předají jako parametry. |

## <a name="next-steps"></a>Další kroky

[Vytvoření virtuálního počítače ze snímku](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).