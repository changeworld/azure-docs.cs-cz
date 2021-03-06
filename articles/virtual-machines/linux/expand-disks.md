---
title: Rozšíření virtuálních pevných disků na virtuálním počítači s Linuxem
description: Zjistěte, jak rozšířit virtuální pevné disky na virtuálním počítači s Linuxem pomocí azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1295c5276f0f342323acf8d86eaaf9f785af3e9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945188"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Rozšíření virtuálních pevných disků na virtuálním počítači s Linuxem pomocí azure cli

Tento článek popisuje, jak rozbalit spravované disky pro virtuální počítač (VM) Linuxu pomocí azure CLI. Můžete [přidat datové disky,](add-disk.md) které poskytují další úložný prostor, a můžete také rozšířit existující datový disk. Výchozí velikost virtuálního pevného disku pro operační systém (OS) je obvykle 30 GB na virtuálním počítači s Linuxem v Azure. 

> [!WARNING]
> Vždy se ujistěte, že je souborový systém v pořádku, typ tabulky oddílů disku bude podporovat novou velikost a před provedením operací s velikostí disku se ujistěte, že jsou data zálohována. Další informace najdete [v tématu Zálohování virtuálních počítačů SIP v Azure](tutorial-backup-vms.md). 

## <a name="expand-an-azure-managed-disk"></a>Rozšíření spravovaného disku Azure
Ujistěte se, že máte nainstalovaný nejnovější [Azure CLI](/cli/azure/install-az-cli2) a jsou přihlášeni k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az-login).

Tento článek vyžaduje existující virtuální počítač v Azure s alespoň jeden datový disk připojené a připravené. Pokud ještě nemáte virtuální počítač, který můžete použít, přečtěte si informace [o vytvoření a přípravě virtuálního počítače s datovými disky](tutorial-manage-disks.md#create-and-attach-disks).

V následujících ukázkách nahraďte názvy vzorových parametrů, jako je *myResourceGroup* a *myVM,* vlastními hodnotami.

1. Operace na virtuálních pevných discích nelze provést se spuštěným virtuálním počítačem. Navrátit virtuální počítač s [az vm navrátit](/cli/azure/vm#az-vm-deallocate). Následující příklad navrací virtuální hosed s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Virtuální počítač musí být přidělena rozšířit virtuální pevný disk. Zastavení virtuálního `az vm stop` počítače s neuvolní výpočetní prostředky. Chcete-li uvolnit `az vm deallocate`výpočetní prostředky, použijte .

1. Zobrazení seznamu spravovaných disků ve skupině prostředků se [seznamem az disků](/cli/azure/disk#az-disk-list). V následujícím příkladu je zobrazen seznam spravovaných disků ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Rozbalte požadovaný disk pomocí [aktualizace az disku](/cli/azure/disk#az-disk-update). Následující příklad rozšiřuje spravovaný disk s názvem *myDataDisk* na *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Když rozbalíte spravovaný disk, aktualizovaná velikost se zaokrouhlí nahoru na nejbližší velikost spravovaného disku. Tabulku dostupných velikostí spravovaných disků a úrovní najdete v tématu [Přehled spravovaných disků Azure – ceny a fakturace](../windows/managed-disks-overview.md).

1. Začněte virtuální počítač se [spuštěním az vm](/cli/azure/vm#az-vm-start). Následující příklad spustí virtuální hod s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Rozbalení oddílu disku a souborového systému
Chcete-li použít rozbalený disk, rozbalte základní oddíl a souborový systém.

1. SSH do virtuálního počítače s příslušnými přihlašovacími údaji. Můžete vidět veřejnou IP adresu vašeho virtuálního počítače s [az vm show](/cli/azure/vm#az-vm-show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Rozbalte základní oddíl a souborový systém.

    a. Pokud je disk již připojen, odpojte jej:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Slouží `parted` k zobrazení informací o disku a změně velikosti oddílu:

    ```bash
    sudo parted /dev/sdc
    ```

    Zobrazení informací o existujícím `print`rozložení oddílu pomocí rozhraní . Výstup je podobný následujícímu příkladu, který ukazuje, že podkladový disk je 215 GB:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Rozbalte oddíl `resizepart`pomocí . Zadejte číslo oddílu *1*a velikost nového oddílu:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Chcete-li `quit`ukončit, zadejte .

1. Při velikosti oddílu ověřte `e2fsck`konzistenci oddílu pomocí nástroje : :

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Změna velikosti souborového systému pomocí `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Připojujte oddíl na požadované `/datadrive`místo, například :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Chcete-li ověřit, zda byla velikost `df -h`datového disku změněna, použijte . Následující příklad výstupu ukazuje, že datová jednotka */dev/sdc1* je nyní 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Další kroky
* Pokud potřebujete další úložiště, můžete také [přidat datové disky do virtuálního počítače s Linuxem](add-disk.md). 
* Další informace o šifrování disku najdete [v tématu Azure Disk Encryption for Linux VM](disk-encryption-overview.md).
