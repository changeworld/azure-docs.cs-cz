---
title: Chyby kvót
description: Popisuje, jak vyřešit chyby kvóty prostředků při nasazování prostředků pomocí Správce prostředků Azure.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 410b086b39d63d03491d390364f4aec6300fc7c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273784"
---
# <a name="resolve-errors-for-resource-quotas"></a>Řešení chyb týkajících se kvót prostředků

Tento článek popisuje chyby kvóty, se kterými se může dojít při nasazování prostředků.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Příznak

Pokud nasadíte šablonu, která vytvoří prostředky, které překračují vaše kvóty Azure, zobrazí se chyba nasazení, která vypadá takto:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Nebo se může zobrazit:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Příčina

Kvóty se uplatňují u jednotlivých skupin prostředků, předplatných, účtů a dalších oborů. Například v rámci předplatného může být nakonfigurované omezení počtu jader v jedné oblasti. Pokud se pokusíte nasadit virtuální počítač s více jádry, než je povolené množství, zobrazí se chyba s informací, že došlo k překročení kvóty.
Úplné informace o kvótách najdete v [tématu Limity předplatného a služeb Azure, kvóty a omezení](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="azure-cli"></a>Azure CLI

Pro Azure CLI `az vm list-usage` použijte příkaz k vyhledání kvót virtuálních strojů.

```azurecli
az vm list-usage --location "South Central US"
```

Který vrátí:

```output
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

V prostředí PowerShell použijte příkaz **Get-AzVMUsage** k vyhledání kvót virtuálních strojů.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Který vrátí:

```output
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Řešení

Chcete-li požádat o zvýšení kvóty, přejděte na portál a podařte problém s podporou. V problému podpory požádejte o zvýšení kvóty pro oblast, do které chcete nasadit.

> [!NOTE]
> Nezapomeňte, že pro skupiny prostředků je kvóta pro každou jednotlivou oblast, nikoli pro celé předplatné. Pokud potřebujete nasadit 30 jader v západní USA, musíte požádat o 30 jader Resource Manager v západní USA. Pokud potřebujete nasadit 30 jader v některé z oblastí, ke kterým máte přístup, měli byste požádat o 30 jader Správce prostředků ve všech oblastech.
>
>

1. Vyberte **Předplatná**.

   ![Předplatná](./media/error-resource-quota/subscriptions.png)

2. Vyberte předplatné, které potřebuje navýšit kvótu.

   ![Výběr předplatného](./media/error-resource-quota/select-subscription.png)

3. Vybrat **využití + kvóty**

   ![Výběr využití a kvót](./media/error-resource-quota/select-usage-quotas.png)

4. V pravém horním rohu vyberte **Požádat o zvýšení**.

   ![Zvýšení žádosti](./media/error-resource-quota/request-increase.png)

5. Vyplňte formuláře pro typ kvóty, který potřebujete navýšit.

   ![Vyplňte formulář](./media/error-resource-quota/forms.png)