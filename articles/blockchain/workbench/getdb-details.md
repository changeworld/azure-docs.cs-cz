---
title: Získání podrobností o databázi služby Azure Blockchain Workbench
description: Zjistěte, jak získat informace o databázi Azure Blockchain Workbench Preview a databázovém serveru.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 2b3190a9d042be8ead1ff3d5ef48d4a2a19e8963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324692"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Získání informací o databázi služby Azure Blockchain Workbench

Tento článek ukazuje, jak získat podrobné informace o databázi Azure Blockchain Workbench Preview.

## <a name="overview"></a>Přehled

Informace o spouštění aplikací, pracovních postupů a chytrých kontraktů se poskytují pomocí zobrazení databáze SQL služby Blockchain Workbench. Vývojáři mohou tyto informace použít při použití nástrojů, jako je Microsoft Excel, Power BI, Visual Studio a SQL Server Management Studio.

Aby se vývojář mohl k této databázi připojit, potřebuje:

* Povolený přístup externího klienta v bráně firewall databáze. Tento článek o konfiguraci brány firewall databáze vysvětluje, jak povolit přístup.
* Název databázového serveru a název databáze

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi Blockchain Workbench

K databázi se připojíte takto:

1. Přihlaste se k portálu Azure pomocí účtu, který má oprávnění **vlastníka** pro prostředky Azure Blockchain Workbench.
2. V levém navigačním podokně zvolte **Skupiny prostředků**.
3. Zvolte název skupiny prostředků svého nasazení služby Blockchain Workbench.
4. Výběrem možnosti **Typ** tento seznam prostředků seřaďte a pak zvolte **SQL Server**. V seřazeném seznamu na dalším snímku obrazovky se zobrazují dvě databáze SQL – jedna „master“ a jedna, která jako **předponu prostředku** používá „lhgn“.

   ![Seřazený seznam prostředků služby Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Pokud si chcete prohlédnout podrobné informace o databázi služby Blockchain Workbench, vyberte odkaz na databázi s **předponou prostředku**, kterou jste zadali pro nasazení služby Blockchain Workbench.

   ![Podrobnosti databáze](./media/getdb-details/workbench-db-details.png)

Název databázového serveru a název databáze vám umožní se připojit k databázi služby Blockchain Workbench pomocí vývojového nebo vykazovacího nástroje.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](database-views.md)