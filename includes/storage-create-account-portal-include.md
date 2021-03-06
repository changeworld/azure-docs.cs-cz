---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ea8ed75bf91850abb95ebe983923989375c0fcf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76759840"
---
Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 na webu Azure Portal, postupujte takto:

1. V nabídce webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Účty úložiště**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **účty úložiště**.
2. V okně **Účty úložiště**, které se zobrazí, zvolte **Přidat**.
3. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.
4. Pod polem **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte název nové skupiny prostředků, jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky znázorňující postup vytvoření skupiny prostředků na portálu](./media/storage-create-account-portal-include/create-resource-group-for-storage.png)

5. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název také musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
6. Vyberte pro svůj účet úložiště nějaké umístění nebo použijte výchozí umístění.
7. Tato pole nechte nastavená na výchozí hodnoty:

   |Pole  |Hodnota  |
   |---------|---------|
   |Model nasazení     |Resource Manager         |
   |Výkon     |Standard         |
   |Account kind (Druh účtu)     |StorageV2 (obecné účely v2)         |
   |Replikace     |Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)         |
   |Access tier (Vrstva přístupu)     |Hot         |

8. Pokud plánujete používat [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), zvolte kartu **Upřesnit** a nastavte **hierarchický obor názvů** na **Povoleno**.
9. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.
10. Vyberte **Vytvořit**.

Další informace o typech účtů úložiště a dalším nastavení účtu úložiště najdete v článku s [přehledem účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview). Další informace o skupinách prostředků najdete v [tématu Přehled Správce prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
