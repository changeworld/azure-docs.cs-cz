---
title: Použití dat služby Azure Blockchain Workbench v Power BI
description: Zjistěte, jak načíst a zobrazit data z databáze SQL DB služby Azure Blockchain Workbench v Power BI.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 6e1f160c3563a280548c74ebe84f30bf08945c3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324795"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Použití dat služby Azure Blockchain Workbench v Power BI

Microsoft Power BI umožňuje snadno generovat výkonné sestavy z databází SQL DB pomocí [https://www.powerbi.com](https://www.powerbi.com)Power BI Desktop a pak je publikovat do aplikace .

Tento článek obsahuje podrobný návod, jak se připojit k databázi SQL Database služby Azure Blockchain Workbench z Power BI Desktopu, vytvořit sestavu a nasadit ji na stránku powerbi.com.

## <a name="prerequisites"></a>Požadavky

* Stáhněte si [Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Připojení Power BI k datům v Azure Blockchain Workbench

1.  Otevřete Power BI Desktop.
2.  Vyberte **Načíst data**.

    ![Získání dat](./media/data-powerbi/get-data.png)
3.  Ze seznamu typů zdrojů dat vyberte **SQL Server**.

4.  Do dialogového okna zadejte server a název databáze. Určete, jestli chcete data importovat nebo použít **DirectQuery**. Vyberte **OK**.

    ![Výběr SQL Serveru](./media/data-powerbi/select-sql.png)

5.  Zadejte přihlašovací údaje databáze, abyste získali přístup ke službě Azure Blockchain Workbench. Vyberte **Databáze** a zadejte přihlašovací údaje.

    Pokud používáte přihlašovací údaje vytvořené při nasazení služby Azure Blockchain Workbench, pak uživatelským jménem je **dbadmin** a heslo je stejné jako to, které jste při zadali nasazení.

    ![Nastavení databáze SQL DB](./media/data-powerbi/db-settings.png)

6.  Jakmile se připojíte k databázi, dialogové okno **Navigátor** zobrazí tabulky a zobrazení, která jsou v databázi k dispozici. Zobrazení jsou navržená pro generování sestav a mají předponu **vw**.

    ![Navigátor](./media/data-powerbi/navigator.png)

7.  Vyberte zobrazení, které chcete zahrnout. Pro demonstrační účely, zahrnujeme **vwContractAction**, který poskytuje podrobnosti o akcích, které byly ve smlouvě.

    ![Výběr zobrazení](./media/data-powerbi/select-views.png)

Nyní můžete v Power BI vytvářet a publikovat sestavy běžným způsobem.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](database-views.md)