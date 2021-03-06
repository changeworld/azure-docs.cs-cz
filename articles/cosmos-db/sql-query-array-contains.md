---
title: ARRAY_CONTAINS v dotazovacím jazyce Azure Cosmos DB
description: Informace o tom, jak pole obsahuje funkci systému SQL v Azure Cosmos DB vrátí logickou hodnotu označující, zda pole obsahuje zadanou hodnotu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 47fe20888aa546e414b268b30c2e03580750a040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303473"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Vrátí logickou hodnotu označující, zda pole obsahuje zadanou hodnotu. Můžete zkontrolovat částečnou nebo úplnou shodu objektu pomocí logického výrazu v příkazu. 

## <a name="syntax"></a>Syntaxe
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Je pole výraz, který má být prohledán.  
  
*Výraz*  
   Je výraz, který má být nalezen.  

*bool_expr*  
   Je logický výraz. Pokud je vyhodnocena jako 'true' a zadaná vyhledávací hodnota je objekt, příkaz zkontroluje částečnou shodu (vyhledávací objekt je podmnožinou jednoho z objektů). Pokud se vyhodnotí na 'false', příkaz zkontroluje úplnou shodu všech objektů v rámci pole. Výchozí hodnota, pokud není zadána, je false. 
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logickou hodnotu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad, jak zkontrolovat členství v `ARRAY_CONTAINS`poli pomocí .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Zde je sada výsledků.  
  
```json
[{"b1": true, "b2": false}]  
```  

Následující příklad, jak zkontrolovat částečnou shodu JSON v poli pomocí ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Zde je sada výsledků.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude mít prospěch z [indexu rozsahu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Funkce pole Azure Cosmos DB](sql-query-array-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
