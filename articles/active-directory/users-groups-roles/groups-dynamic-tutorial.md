---
title: Přidání uživatelů do dynamické skupiny – kurz – Azure AD | Dokumenty společnosti Microsoft
description: V tomto kurzu použijete skupiny s pravidly členství uživatelů k automatickému přidávání nebo odebírání uživatelů.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 382f3b59142aee7ddfbec4aceb153a174874ac1a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74027114"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Kurz: Automatické přidávání nebo odebírání členů skupin

V Azure Active Directory (Azure AD) můžete automaticky přidávat uživatele do skupin zabezpečení nebo skupin Office 365 nebo je z nich odebírat, abyste to pokaždé nemuseli dělat ručně. Při každé změně jakýchkoli vlastností uživatele nebo zařízení Azure AD vyhodnotí všechna dynamická pravidla skupin ve vašem tenantovi a zjistí, jestli má změna přidat nebo odebrat členy.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření automaticky vyplněné skupiny uživatelů typu Host z partnerské společnosti
> * Přiřazení licencí ke skupině pro funkce specifické pro partnera, ke kterým budou mít uživatelé typu host přístup
> * Bonus: Zabezpečení skupiny **Všichni uživatelé** odebráním uživatelů typu host, abyste členům mohli například udělit přístup k výhradně interním webům

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Tato funkce vyžaduje jednu licenci Azure AD Premium pro vás jako globálního správce klienta. Pokud ho nemáte, vazure ad vyberte **Licenční** > **produkty** > **Try/Buy**.

Uživatelům, kteří mají být členy dynamických skupin, licence přiřazovat nemusíte. V tenantovi potřebujete pouze minimální počet dostupných licencí Azure AD Premium P1 pro pokrytí všech takových uživatelů. 

## <a name="create-a-group-of-guest-users"></a>Vytvoření skupiny uživatelů typu host

Nejprve vytvoříte skupinu pro uživatele typu host, kteří jsou všichni z jedné partnerské společnosti. Potřebují zvláštní licencování, takže je efektivnější pro tento účel vytvořit skupinu.

1. Přihlaste se khttps://portal.azure.com) portálu Azure (s účtem, který je globálním správcem vašeho tenanta.
2. Vyberte novou **skupinu skupiny Služby Active Directory** > **služby** > **New group**Azure .
   ![výběr příkazu pro spuštění nové skupiny](./media/groups-dynamic-tutorial/new-group.png)
3. V okně **Skupina**:
  
   * Jako typ skupiny vyberte **Zabezpečení.**
   * Zadejte `Guest users Contoso` jako název a popis skupiny.
   * Změnit **typ členství** na dynamický **uživatel**.
   
4. Vyberte **Vlastníci** a v okně **Přidat vlastníky** vyhledejte všechny požadované vlastníky. Klikněte na požadované vlastníky přidat do výběru.
5. Klepnutím na **tlačítko Vybrat** zavřete okno **Přidat vlastníky.**  
6. V poli Dynamické **uživatelské členy** vyberte **Upravit dynamický dotaz.**
7. V okně**Pravidla dynamického členství**:

   * V poli **Vlastnosti** klikněte na existující hodnotu a vyberte **userType**. 
   * Ověřte, **zda** je v poli **Operátor vybráno stejné.**  
   * Vyberte pole **Hodnota** a zadejte **Host**. 
   * Kliknutím na hypertextový odkaz **Přidat výraz** přidejte další řádek.
   * V poli **A/Or** vyberte **a**.
   * V poli **Vlastnosti** vyberte **název společnosti**.
   * Ověřte, **zda** je v poli **Operátor vybráno stejné.**
   * Do pole **Hodnota** zadejte **contoso**.
   * Klepnutím na tlačítko **Uložit** zavřete okno **Dynamic kára pravidel členství.**
   
8. V okně **Skupina** výběrem možnosti **Vytvořit** vytvořte skupinu.

## <a name="assign-licenses"></a>Přiřazení licencí

Teď, když máte novou skupinu, můžete použít licence, které tito uživatelé z partnerské společnosti potřebují.

1. V Azure AD vyberte **Licence**, vyberte jednu nebo několik licencí a pak vyberte **Přiřadit**.
2. Vyberte **Uživatelé a skupiny**, vyberte skupinu **Uživatelé typu host – Contoso** a uložte provedené změny.
3. **Možnosti přiřazení** umožňují zapnout nebo vypnout plány služeb zahrnuté v licencích, které jste vybrali. Když provedete nějaké změny, nezapomeňte je uložit kliknutím na **OK**.
4. Přiřazení dokončíte tak, že v dolní části podokna **Přiřadit licenci** kliknete na **Přiřadit**.

## <a name="remove-guests-from-all-users-group"></a>Odebrání hostů ze skupiny Všichni uživatelé

Možná je vaším konečným správním plánem přiřadit všechny uživatele typu host k vlastním skupinám podle společnosti. Teď můžete také změnit skupinu **Všichni uživatelé** tak, aby byla vyhrazená pouze pro členy ve vašem tenantovi. Pak ji můžete použít k přiřazování aplikací a licencí specifických pro vaši domovskou organizaci.

   ![Změna skupiny Všichni uživatelé tak, aby byla pouze pro členy](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

**Odebrání skupiny uživatelů typu host**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu, který má k tenantovi oprávnění globálního správce.
2. Vyberte**skupiny služby** **Azure Active Directory** > . Vyberte skupinu **Uživatelé typu host – Contoso**, vyberte tři tečky (...) a pak vyberte **Odstranit**. Když odstraníte skupinu, odeberou se všechny přiřazené licence.

**Obnovení skupiny Všichni uživatelé**
1. Vyberte**skupiny služby** **Azure Active Directory** > . Výběrem názvu skupiny **Všichni uživatelé** otevřete skupinu.
1. Vyberte **Pravidla dynamického členství**, vymažte veškerý text pravidla a vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Vytvoření skupiny uživatelů typu host
> * Přiřazení licencí k nové skupině
> * Změna skupiny Všichni uživatelé tak, aby byla pouze pro členy

Přejděte k dalšímu článku, kde najdete další základní informace o licencování na základě skupin.
> [!div class="nextstepaction"]
> [Základy licencování na základě skupin](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



