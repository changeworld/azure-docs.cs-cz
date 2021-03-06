---
title: Hromadně vytvářet uživatele (preview) na portálu Azure Active Directory | Dokumenty společnosti Microsoft
description: Hromadné přidání uživatelů v Centru pro správu Azure AD ve službě Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174314"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Hromadně vytvářet uživatele (preview) ve službě Azure Active Directory

Azure Active Directory (Azure AD) podporuje hromadné uživatelské operace vytváření a odstraňování, hromadné pozvání pro hosty a podporuje stahování seznamů uživatelů, skupin a členů skupiny.

## <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li hromadně vytvářet uživatele na portálu pro správu, musíte být přihlášeni jako globální správce nebo správce uživatelů.

## <a name="to-create-users-in-bulk"></a>Hromadné vytváření uživatelů

1. [Přihlaste se k organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. Ve službě Azure AD vyberte **uživatelé** > **hromadné vytváření**.
1. Na stránce **Hromadné vytvoření uživatele** vyberte **Stáhnout,** chcete-li získat platný soubor hodnot oddělených čárkami (CSV) uživatelských vlastností a pak přidejte uživatele, které chcete vytvořit.

   ![Vyberte místní soubor CSV, do kterého uvedete seznam uživatelů, které chcete přidat.](./media/users-bulk-add/upload-button.png)

1. Otevřete soubor CSV a přidejte řádek pro každého uživatele, kterého chcete vytvořit. Jediné požadované hodnoty jsou **Název**, **Hlavní jméno uživatele**, Počáteční **heslo** a Přihlášení **bloku (Ano/Ne)**. Pak soubor uložte.

   ![Soubor CSV obsahuje názvy a ID uživatelů k vytvoření](./media/users-bulk-add/add-csv-file.png)

1. Na stránce **Hromadné vytvoření uživatele (Náhled)** v části Nahrání souboru CSV vyhledejte soubor. Když vyberete soubor a klepnete na **tlačítko Odeslat**, spustí se ověření souboru CSV.
1. Po ověření obsahu souboru se **soubor úspěšně nahraje**. Pokud se jedná o chyby, je nutné je před odesláním úlohy opravit.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která importuje nové uživatele.
1. Po dokončení operace importu se zobrazí oznámení o stavu úlohy hromadné operace.

Pokud dojde k chybám, můžete soubor výsledků stáhnout a zobrazit na stránce **Výsledky hromadné operace.** Soubor obsahuje důvod každé chyby.

## <a name="check-status"></a>Zkontrolování stavu

Stav všech nevyřízených hromadných požadavků můžete zobrazit na stránce **Výsledky hromadné operace (preview).**

   ![Kontrola stavu nahrávání na stránce Výsledky hromadných operací](./media/users-bulk-add/bulk-center.png)

Dále můžete zkontrolovat, zda uživatelé, které jste vytvořili, existují v organizaci Azure AD buď na webu Azure Portal, nebo pomocí PowerShellu.

## <a name="verify-users-in-the-azure-portal"></a>Ověření uživatelů na webu Azure Portal

1. [Přihlaste se do Centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. V navigačním podokně vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Uživatele**.
1. V části **Zobrazit**vyberte **Možnost Všichni uživatelé** a ověřte, zda jsou v seznamu uvedeni uživatelé, které jste vytvořili.

### <a name="verify-users-with-powershell"></a>Ověření uživatelů pomocí PowerShellu

Spusťte následující příkaz:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Měli byste vidět, že uživatelé, které jste vytvořili, jsou uvedeny.

## <a name="bulk-import-service-limits"></a>Limity služeb hromadného importu

Každá hromadná aktivita pro vytvoření uživatelů může běžet až jednu hodinu. To umožňuje hromadné vytváření alespoň 50 000 uživatelů.

## <a name="next-steps"></a>Další kroky

- [Hromadné odstranění uživatelů](users-bulk-delete.md)
- [Stáhnout seznam uživatelů](users-bulk-download.md)
- [Uživatelé hromadného obnovení](users-bulk-restore.md)
