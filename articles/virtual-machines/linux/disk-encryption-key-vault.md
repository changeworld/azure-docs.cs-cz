---
title: Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption
description: Tento článek obsahuje postup pro vytvoření a konfiguraci trezoru klíčů pro použití s azure disk encryption
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5d9acb525f35da756a986826574082f1ecafedf5
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314097"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption

Azure Disk Encryption používá Azure Key Vault k řízení a správě šifrovacích klíčů a tajných kódů disku.  Další informace o trezorech klíčů najdete [v tématu Začínáme s trezorem klíčů Azure](../../key-vault/key-vault-get-started.md) a [Zabezpečte trezor klíčů](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Pokud jste dříve používali Azure Disk Encryption s Azure AD k šifrování virtuálního počítače, musíte pokračovat v použití této možnosti k šifrování virtuálního počítače. Podrobnosti najdete [v tématu Vytváření a konfigurace trezoru klíčů pro šifrování disku Azure pomocí Azure AD (předchozí verze).](disk-encryption-key-vault-aad.md)

Vytvoření a konfigurace trezoru klíčů pro použití s Azure Disk Encryption zahrnuje tři kroky:

1. V případě potřeby vytvoříte skupinu prostředků.
2. Vytvoření trezoru klíčů. 
3. Nastavení pokročilých zásad přístupu trezoru klíčů.

Tyto kroky jsou znázorněny v následujících rychlých startech:

- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure CLI](disk-encryption-cli-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure PowerShellu](disk-encryption-cli-quickstart.md)

Pokud chcete, můžete také vygenerovat nebo importovat šifrovací klíč (KEK).

> [!Note]
> Kroky v tomto článku jsou automatizované v [předpokladech Azure Disk Encryption CLI skript](https://github.com/ejarvi/ade-cli-getting-started) a [Azure Disk Encryption předpoklady PowerShell skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Instalace nástrojů a připojení k Azure

Kroky v tomto článku můžete dokončit pomocí [rozhraní příkazového příkazu Azure ,](/cli/azure/) [modulu Azure PowerShell Az](/powershell/azure/overview)nebo [portálu Azure](https://portal.azure.com). 

Zatímco portál je přístupný prostřednictvím vašeho prohlížeče, Azure CLI a Azure PowerShell vyžadují místní instalaci; podrobnosti najdete v [tématu Azure Disk Encryption for Linux: Nainstalujte nástroje](disk-encryption-linux.md#install-tools-and-connect-to-azure) pro podrobnosti.

### <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Před použitím Azure CLI nebo Azure PowerShell, musíte se nejprve připojit k předplatnému Azure. Uděláte to tak, že [se přihlásíte pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [přihlásíte se pomocí Azure Powershellu](/powershell/azure/authenticate-azureps?view=azps-2.5.0)nebo po zobrazení výzvy zadáte svá pověření na portál Azure.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 ```
 
## Next steps

- [Azure Disk Encryption prerequisites CLI script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Learn [Azure Disk Encryption scenarios on Linux VMs](disk-encryption-linux.md)
- Learn how to [troubleshoot Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Read the [Azure Disk Encryption sample scripts](disk-encryption-sample-scripts.md)
