---
title: 'Kurz: Udělte skupině přístup k prostředkům Azure pomocí RBAC a Azure PowerShellu'
description: Zjistěte, jak udělit skupině přístup k prostředkům Azure pomocí řízení přístupu na základě rolí (RBAC) a Azure PowerShell v tomto kurzu.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: 2f001e781640704cfcee161b3de75b3f4cae1423
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77138243"
---
# <a name="tutorial-grant-a-group-access-to-azure-resources-using-rbac-and-azure-powershell"></a>Kurz: Udělení skupiny přístupu k prostředkům Azure pomocí RBAC a Azure PowerShellu

[Řízení přístupu na základě rolí (RBAC)](overview.md) je způsob, jakým spravujete přístup k prostředkům Azure. V tomto kurzu pomocí Azure PowerShellu udělíte skupině přístup k zobrazení všeho v předplatném a správě všeho ve skupině prostředků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělení přístupu pro skupinu v různých oborech
> * Výpis přístupu
> * Odebrání přístupu

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Oprávnění vytvářet skupiny v Azure Active Directory (nebo existující skupinu)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>Přiřazení rolí

V RBAC se přístup uděluje vytvořením přiřazení role. Přiřazení role se skládá ze tří prvků: objekt zabezpečení, definice role a obor. Tady jsou dvě přiřazení rolí, která v tomto kurzu provedete:

| Objekt zabezpečení | Definice role | Rozsah |
| --- | --- | --- |
| Skupina<br>(RBAC Tutorial Group) | [Čtenář](built-in-roles.md#reader) | Předplatné |
| Skupina<br>(RBAC Tutorial Group)| [Přispěvatel](built-in-roles.md#contributor) | Skupina prostředků<br>(rbac-tutorial-resource-group) |

   ![Přiřazení rolí pro skupinu](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>Vytvoření skupiny

Pro přiřazení role potřebujete uživatele, skupinu nebo instanční objekt. Pokud skupinu ještě nemáte, můžete ji vytvořit.

- Ve službě Azure Cloud Shell vytvořte novou skupinu pomocí příkazu [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

Pokud nemáte oprávnění k vytváření skupin, můžete zkusit [kurz: Udělit uživateli přístup k prostředkům Azure pomocí RBAC a Azure PowerShell](tutorial-role-assignments-user-powershell.md) místo.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupinu prostředků použijeme k ukázce, jak přiřadit roli v oboru skupiny prostředků.

1. Seznam umístění oblastí získáte pomocí příkazu [Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Vyberte umístění blízko vás a přiřaďte ho do proměnné.

   ```azurepowershell
   $location = "westus"
   ```

1. Vytvořte novou skupinu prostředků pomocí příkazu [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Udělení přístupu

Chcete-li skupině udělit přístup, přiřaďte roli pomocí příkazu [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment) Musíte zadat objekt zabezpečení, definici role a obor.

1. Získejte ID objektu skupiny pomocí příkazu [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. Uložte ID objektu skupiny do proměnné.

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. Získejte ID předplatného pomocí příkazu [Get-AzSubscription.](/powershell/module/Az.Accounts/Get-AzSubscription)

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Uložte obor předplatného do proměnné.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Přiřaďte skupině roli [Čtenář](built-in-roles.md#reader) v oboru předplatného.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. Přiřaďte skupině roli [Přispěvatel](built-in-roles.md#contributor) v oboru skupiny prostředků.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>Výpis přístupu

1. Chcete-li ověřit přístup k odběru, použijte příkaz [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) k zobrazení seznamu přiřazení rolí.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    Ve výstupu vidíte, že se skupině RBAC Tutorial Group přiřadila role Čtenář v oboru předplatného.

1. Chcete-li ověřit přístup ke skupině prostředků, použijte příkaz [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) k zobrazení seznamu přiřazení rolí.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    Ve výstupu vidíte, že se skupině RBAC Tutorial Group přiřadily role Přispěvatel i Čtenář. Role Přispěvatel je v oboru skupiny prostředků rbac-tutorial-resource-group a role Čtenář se dědí v oboru předplatného.

## <a name="optional-list-access-using-the-azure-portal"></a>(Volitelné) Výpis přístupu pomocí webu Azure Portal

1. Pokud se chcete podívat, jak přiřazení rolí vypadají na webu Azure Portal, zobrazte okno **Řízení přístupu (IAM)** pro dané předplatné.

    ![Přiřazení rolí pro skupinu v oboru předplatného](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. Zobrazte okno **Řízení přístupu (IAM)** pro danou skupinu prostředků.

    ![Přiřazení rolí pro skupinu v oboru skupiny prostředků](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>Odebrání přístupu

Chcete-li odebrat přístup uživatelům, skupinám a aplikacím, odeberte přiřazení role pomocí [funkce Remove-AzRoleAssignment.](/powershell/module/az.resources/remove-azroleassignment)

1. Pomocí následujícího příkazu odeberte skupině přiřazení role Přispěvatel v oboru skupiny prostředků.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Pomocí následujícího příkazu odeberte skupině přiřazení role Čtenář v oboru předplatného.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, odstraňte skupinu prostředků a skupinu.

1. Odstraňte skupinu prostředků pomocí příkazu [Remove-AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup)

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Po potvrže zadejte **Y**. Odstranění bude trvat několik sekund.

1. Odstraňte skupinu pomocí příkazu [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup).

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    Pokud se při pokusu o odstranění skupiny zobrazí chyba, můžete skupinu odstranit také na portálu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa přístupu k prostředkům Azure pomocí RBAC a Azure PowerShellu](role-assignments-powershell.md)
