---
title: ToString v dotazovacím jazyce Azure Cosmos DB
description: Další informace o funkci systému SQL ToString v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 293449b1616e7124245d91c647177b958006009e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304255"
---
# <a name="tostring-azure-cosmos-db"></a>ToString (Azure Cosmos DB)
 Vrátí řetězcovou reprezentaci skalárního výrazu. 
  
## <a name="syntax"></a>Syntaxe
  
```sql
ToString(<expr>)
```  
  
## <a name="arguments"></a>Argumenty
  
*Výraz*  
   Je nějaký skalární výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí řetězec výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak `ToString` se chová napříč různými typy.   
  
```sql
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 Zde je sada výsledků.  
  
```json
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 Vzhledem k následujícímu vstupu:
```json
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 Následující příklad ukazuje, jak `ToString` lze použít `CONCAT`s jinými funkcemi řetězce, jako je .   
 
```sql
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

Zde je sada výsledků.  
  
```json
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
Vzhledem k následujícímu vstupu.
```json
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
Následující příklad ukazuje, jak `ToString` lze použít `REPLACE`s jinými funkcemi řetězce, jako je .   
```sql
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
Zde je sada výsledků.  
 ```json
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
