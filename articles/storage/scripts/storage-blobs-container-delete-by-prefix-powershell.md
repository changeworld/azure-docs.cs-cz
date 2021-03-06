---
title: Ukázka skriptu Prostředí Azure – odstranění kontejnerů pomocí předpony | Dokumenty společnosti Microsoft
description: Kontejnery objektů blob ve službě Azure Storage můžete odstranit na základě předpony názvu kontejneru.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 73c2f221d2ac0b3ceb76e3db0fdc6379a6dd330a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060885"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Odstranění kontejnerů na základě předpony názvu kontejneru

Tento skript odstraní kontejnery v úložišti objektů Blob Azure na základě předpony v názvu kontejneru.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, zbývající kontejnery a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k odstranění kontejnerů na základě předpony názvu kontejneru používá následující příkazy. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Získá zadaný účet úložiště nebo všechny účty úložiště ve skupině prostředků nebo předplatné. |
| [Kontejner Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Zobrazí seznam kontejnerů úložiště přidružených k účtu úložiště. |
| [Odebrat kontejner AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | Odebere zadaný kontejner úložiště. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro úložiště najdete v tématu [Ukázky PowerShellu pro úložiště objektů blob v Azure](../blobs/storage-samples-blobs-powershell.md).
