---
title: Ukázky Azure CLI
description: Ukázky Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 76803f8c3a703071eb733c5cfde65482ffd07f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970083"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Ukázky Azure CLI pro virtuální počítače s Linuxem

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| | |
|---|---|
|**Vytvoření virtuálních počítačů**||
| [Vytvoření virtuálního počítače](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač Linux s minimální konfigurací. |
| [Vytvoření plně nakonfigurovaného virtuálního počítače](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří skupinu prostředků, virtuální počítač a všechny související prostředky.|
| [Vytváření vysoce dostupných virtuálních počítačů](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří několik virtuálních počítačů ve vysoce dostupné konfiguraci s vyrovnáváním zatížení. |
| [Vytvoření virtuálního počítače a spuštění konfiguračního skriptu](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač a k instalaci NGINX uvede rozšíření Azure Custom Script. |
| [Vytvoření virtuálního virtuálního zařízení s nainstalovaným WordPressem](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač a k instalaci WordPressu použije rozšíření Azure Custom Script. |
| [Vytvoření virtuálního počítače ze spravovaného disku operačního systému](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač připojením existujícího spravovaného disku jako disku operačního systému. |
| [Vytvoření virtuálního počítače ze snímku](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač ze snímku tak, že nejprve vytvoří spravovaný disk ze snímku a potom připojí nový spravovaný disk jako disk operačního systému. |
|**Spravovat úložiště**||
| [Vytvoření spravovaného disku z VHD](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří spravovaný disk ze specializovaného virtuálního pevného disku jako disk operačního systému nebo z datového virtuálního pevného disku jako datového disku.  |
| [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří spravovaný disk ze snímku. |
| [Kopírování spravovaného disku na stejné nebo jiné předplatné](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Zkopíruje spravovaný disk do stejného nebo jiného předplatného, ale ve stejné oblasti jako nadřazený spravovaný disk. 
| [Export snímku jako VHD do účtu úložiště](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exportuje spravovaný snímek jako virtuální pevný disk do účtu úložiště v jiné oblasti. |
| [Export VHD spravovaného disku do účtu úložiště](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exportuje základní virtuální pevný disk spravovaného disku do účtu úložiště v jiné oblasti. |
| [Kopírování snímku do stejného nebo jiného předplatného](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Zkopíruje snímek na stejné nebo jiné předplatné, ale ve stejné oblasti jako nadřazený snímek. |
|**Síťové virtuální počítače**||
| [Zabezpečení provozu sítě mezi virtuálními počítači](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří dva virtuální počítače, všechny související prostředky a skupiny zabezpečení interní a externí sítě (NSG). |
|**Zabezpečení virtuálních počítačů**||
| [Šifrování virtuálního počítače a datových disků](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří trezor klíčů Azure, šifrovací klíč a instanční objekt a pak zašifruje virtuální počítač. |
|**Monitorování virtuálních počítačů**||
| [Monitorování virtuálního počítače pomocí protokolů Azure Monitoru](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Vytvoří virtuální počítač, nainstaluje agenta Analýzy protokolů a zapíše virtuální počítač do pracovního prostoru Analýzy protokolů.  |
|**Poradce při potížích s virtuálními počítači**||
| [Řešení potíží s diskem s operačním systémem virtuálního počítače](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Připojí disk operačního systému z jednoho virtuálního počítače jako datový disk na druhém virtuálním počítači. |
| | |
