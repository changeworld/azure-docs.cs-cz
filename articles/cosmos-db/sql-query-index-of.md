---
title: INDEX_OF v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o INDEX_OF funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50e489fdf57398d486b07944782ecbb3fd1d6a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71350984"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Azure Cosmos DB)
 Vrátí počáteční pozici prvního výskytu druhého řetězcového výrazu v rámci prvního zadaného řetězcového výrazu nebo -1, pokud řetězec nebyl nalezen.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je řetězec výraz, který má být prohledán.  
  
*str_expr2*  
   Je řetězec výraz hledat.  

*numeric_expr* Volitelný číselný výraz, který nastaví pozici, kterou začne hledání. První pozice v *str_expr1* je 0. 
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí index různých podřetězců uvnitř "abc".  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Zde je sada výsledků.  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>Další kroky

- [Funkce řetězce Azure Cosmos DB](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
