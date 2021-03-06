---
title: Vyhledání a odstranění nepřipojených spravovaných a nespravovaných disků Azure
description: Jak najít a odstranit nepřipojené disky Azure spravované a nespravované (VHD/stránky blobs) pomocí Azure CLI.
author: roygara
ms.service: virtual-machines
ms.topic: article
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6cf48c53e7b5c1cc8537abeda164460de66abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945149"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Vyhledání a odstranění nepřipojených spravovaných a nespravovaných disků Azure pomocí příkazového příkazového příkazu Azure
Když odstraníte virtuální počítač (VM) v Azure, ve výchozím nastavení se neodstraní všechny disky připojené k virtuálnímu počítači. Tato funkce pomáhá zabránit ztrátě dat v důsledku neúmyslného odstranění virtuálních stránek. Po odstranění virtuálního počítače budete nadále platit za nepřipojené disky. Tento článek ukazuje, jak najít a odstranit všechny nepřipojené disky a snížit zbytečné náklady. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Spravované disky: Hledání a odstraňování nepřipojených disků 

Následující skript hledá nepřipojené [spravované disky](managed-disks-overview.md) kontrolou hodnoty **ManagedBy** vlastnost. Když je spravovaný disk připojený k virtuálnímu počítače, vlastnost **ManagedBy** obsahuje ID prostředku virtuálního počítače. Pokud je spravovaný disk odpojen, vlastnost **ManagedBy** má hodnotu null. Skript zkontroluje všechny spravované disky v předplatném Azure. Když skript vyhledá spravovaný disk s vlastností **ManagedBy** nastavenou na hodnotu null, skript určí, že disk není připojen.

>[!IMPORTANT]
>Nejprve spusťte skript nastavením proměnné **deleteUnattachedDisks** na 0. Tato akce umožňuje najít a zobrazit všechny nepřipojené spravované disky.
>
>Po kontrole všech nepřipojených disků spusťte skript znovu a nastavte proměnnou **deleteUnattachedDisks** na 1. Tato akce umožňuje odstranit všechny nepřipojené spravované disky.
>

```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nespravované disky: Vyhledání a odstranění nepřipojených disků 

Nespravované disky jsou soubory Virtuálního pevného disku, které jsou uloženy jako [objekty BLOB stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) v [účtech úložiště Azure](../../storage/common/storage-create-storage-account.md). Následující skript vyhledá nepřipojené nespravované disky (objekty BLOB stránky) kontrolou hodnoty vlastnosti **LeaseStatus.** Pokud je k virtuálnímu počítače připojen nespravovaný disk, je vlastnost **LeaseStatus** nastavena na **uzamčeno**. Pokud není připojen nespravovaný disk, je vlastnost **LeaseStatus** nastavena na **odemčeno**. Skript zkoumá všechny nespravované disky ve všech účtech úložiště Azure v předplatném Azure. Pokud skript vyhledá nespravovaný disk s vlastností **LeaseStatus** nastavenou na **Unlocked**, skript určí, že disk není připojen.

>[!IMPORTANT]
>Nejprve spusťte skript nastavením **deleteUnattachedVHDs** proměnné 0. Tato akce umožňuje najít a zobrazit všechny nepřipojené nespravované virtuální disky.
>
>Po kontrole všech nepřipojených disků spusťte skript znovu a nastavte proměnnou **deleteUnattachedVHDs** na 1. Tato akce umožňuje odstranit všechny nepřipojené nespravované virtuální disponitova.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Další kroky

[Odstranit účet úložiště](../../storage/common/storage-create-storage-account.md)



