---
title: Uživatelem definované body obnovení
description: Jak vytvořit bod obnovení pro fond SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5c9b7eb1b03b6b6e3721c13f9ebf7da25dd2e376
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745058"
---
# <a name="user-defined-restore-points"></a>Uživatelem definované body obnovení

V tomto článku se naučíte vytvořit nový uživatelem definovaný bod obnovení pro fond SQL v Azure Synapse Analytics pomocí PowerShellu a portálu Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Vytváření uživatelem definovaných bodů obnovení pomocí prostředí PowerShell

Chcete-li vytvořit uživatelem definovaný bod obnovení, použijte rutinu prostředí [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell.

1. Než začnete, nezapomeňte [nainstalovat Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otevřete PowerShell.
3. Připojte se ke svému účtu Azure a uveďte všechna předplatná přidružená k vašemu účtu.
4. Vyberte odběr, který obsahuje databázi, která má být obnovena.
5. Vytvořte bod obnovení pro okamžitou kopii datového skladu.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Podívejte se na seznam všech existujících bodů obnovení.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Vytváření uživatelem definovaných bodů obnovení prostřednictvím portálu Azure

Uživatelem definované body obnovení lze také vytvořit prostřednictvím portálu Azure.

1. Přihlaste se ke svému účtu [na Portálu Azure.](https://portal.azure.com/)

2. Přejděte do fondu SQL, pro který chcete vytvořit bod obnovení.

3. V levém podokně vyberte **Přehled,** vyberte **+ Nový bod obnovení**. Pokud není povoleno tlačítko Nový bod obnovení, ujistěte se, že fond SQL není pozastaven.

    ![Nový bod obnovení](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Zadejte název uživatelem definovaného bodu obnovení a klepněte na **tlačítko Použít**. Uživatelem definované body obnovení mají výchozí dobu uchování sedm dní.

    ![Název bodu obnovení](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Další kroky

- [Obnovení existujícího fondu SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Obnovení odstraněného fondu SQL](sql-data-warehouse-restore-deleted-dw.md)
- [Obnovení z fondu SQL geografické zálohování](sql-data-warehouse-restore-from-geo-backup.md)

