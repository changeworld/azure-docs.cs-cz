---
title: Správa databáze Azure pro PostgreSQL – portál Azure
description: Zjistěte, jak spravovat Azure Database for PostgreSQL server z webu Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: bcddd64afca29ac9fdd5d284fc8f809ff9e2477d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535688"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Správa databáze Azure pro postgreSQL server pomocí portálu Azure
V tomto článku se ukazuje, jak spravovat azure databázi pro servery PostgreSQL. Úlohy správy zahrnují škálování výpočetních prostředků a úložišť, resetování hesla správce a zobrazení podrobností o serveru.

## <a name="sign-in"></a>Přihlášení
Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Vytvoření serveru
Najdete [na úvodním panelu,](quickstart-create-server-database-portal.md) kde se dozvíte, jak vytvořit a začít s Azure Database for PostgreSQL serverem.

## <a name="scale-compute-and-storage"></a>Škálování výpočetních prostředků a úložiště

Po vytvoření serveru můžete škálovat mezi úrovněmi General Purpose a Memory Optimized podle potřeby. Můžete také škálovat výpočetní prostředky a paměť zvýšením nebo snížením virtuálních jader. Úložiště lze škálovat nahoru (ale nelze škálovat úložiště dolů).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Škálování mezi úrovněmi optimalizovanými pro obecné účely a optimalizovanými pro paměť

Můžete škálovat z obecnéúčely na optimalizovanou paměť a naopak. Změna na a z úrovně Basic po vytvoření serveru není podporována. 

1. Vyberte svůj server na webu Azure Portal. Vyberte **Cenová úroveň**, která se nachází v části **Nastavení.**

2. Vyberte **možnost Pro obecné účely** nebo **Optimalizovaná pro paměť**v závislosti na změně měřítka. 

    ![úroveň ocenění změn](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > Změna úrovní způsobí restartování serveru.

4. Chcete-li uložit změny, vyberte **ok.**


### <a name="scale-vcores-up-or-down"></a>Škálování virtuálních jader nahoru nebo dolů

1. Vyberte svůj server na webu Azure Portal. Vyberte **Cenová úroveň**, která se nachází v části **Nastavení.**

2. Změňte nastavení **virtuálního jádra** přesunutím jezdce na požadovanou hodnotu.

    ![škálovat-vypočítat](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > Škálování virtuálních jader způsobí restartování serveru.

3. Chcete-li uložit změny, vyberte **ok.**


### <a name="scale-storage-up"></a>Škálování úložiště nahoru

1. Vyberte svůj server na webu Azure Portal. Vyberte **Cenová úroveň**, která se nachází v části **Nastavení.**

2. Změňte nastavení **úložiště** posunutím jezdce na požadovanou hodnotu.

    ![škálovací úložiště](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > Úložiště nelze zmenšit.

3. Chcete-li uložit změny, vyberte **ok.**


## <a name="update-admin-password"></a>Aktualizovat heslo správce
Heslo role správce můžete změnit pomocí portálu Azure.

1. Vyberte svůj server na webu Azure Portal. V okně **Přehled** vyberte **Obnovit heslo**.

   ![overview](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Zadejte nové heslo a potvrďte heslo. Textové pole vás vyzve k zadání požadavků na složitost hesla.

   ![resetovací heslo](./media/howto-create-manage-server-portal/reset-password.png)

3. Chcete-li uložit nové heslo, vyberte **možnost OK.**


## <a name="delete-a-server"></a>Odstranění serveru

Pokud server již nepotřebujete, můžete jej odstranit. 

1. Vyberte svůj server na webu Azure Portal. V okně **Přehled** vyberte **Odstranit**.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Zadejte název serveru do vstupního pole a ověřte, zda se jedná o server, který chcete odstranit.

    ![potvrdit-odstranit](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Odstranění serveru je nevratné.

3. Vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky
- Informace o [zálohování a obnovení serveru](howto-restore-server-portal.md)
- Informace o [možnostech ladění a monitorování v Azure Database pro PostgreSQL](concepts-monitoring.md)
