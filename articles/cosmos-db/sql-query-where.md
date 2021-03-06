---
title: Klauzule WHERE v Azure Cosmos DB
description: Informace o klauzuli SQL WHERE pro Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898786"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Klauzule WHERE v Azure Cosmos DB

Volitelná klauzule`WHERE <filter_condition>`WHERE ( ) určuje podmínku ( podmínky), které musí zdrojové položky JSON splňovat, aby je dotaz zahrnul do výsledků. Položka JSON musí vyhodnotit `true` zadané podmínky, které mají být považovány za výsledek. Indexová vrstva používá klauzuli WHERE k určení nejmenší podmnožiny zdrojových položek, které mohou být součástí výsledku.
  
## <a name="syntax"></a>Syntaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenty

- `<filter_condition>`  
  
   Určuje podmínku, která má být splněna pro dokumenty, které mají být vráceny.  
  
- `<scalar_expression>`  
  
   Výraz představující hodnotu, která má být vypočítána. Podrobnosti najdete [v tématu Skalární výrazy.](sql-query-scalar-expressions.md)  
  
## <a name="remarks"></a>Poznámky
  
  Aby měl být dokument vrácen, musí být výraz zadaný jako podmínka filtru vyhodnocen jako true. Pouze logická `true` hodnota bude splňovat podmínku, žádná jiná hodnota: undefined, null, false, Number, Array nebo Object nesplňuje podmínku.

  Pokud do klauzule zahrnete klíč oddílu `WHERE` jako součást filtru rovnosti, dotaz se automaticky přefiltruje pouze na příslušné oddíly.

## <a name="examples"></a>Příklady

Následující dotaz požaduje položky, `id` které obsahují `AndersenFamily`vlastnost, jejíž hodnota je . Vyloučí všechny položky, `id` které nemají vlastnost nebo `AndersenFamily`jejichž hodnota neodpovídá .

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou následující:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Skalární výrazy v klauzuli WHERE

V předchozím příkladu byl zobrazen jednoduchý dotaz rovnosti. Rozhraní SQL API také podporuje různé [skalární výrazy](sql-query-scalar-expressions.md). Nejčastěji používané jsou binární a unární výrazy. Odkazy na vlastnosti ze zdrojového objektu JSON jsou také platné výrazy.

Můžete použít následující podporované binární operátory:  

|**Typ operátora**  | **Hodnoty** |
|---------|---------|
|Aritmetické | +,-,*,/,% |
|Bitové    | \|, &, ^, <<, >>, >>>  (posun vpravo s nulovým vyplněním) |
|Logické    | A, NEBO, NEBO NE      |
|Srovnání | =, &lt;!=, &gt; &lt;, &gt;, =, =, <> |
|Řetězec     |  \|\|(zřetězit) |

Následující dotazy používají binární operátory:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Můžete také použít unární operátory +,-, ~, a NOT v dotazech, jak je znázorněno v následujících příkladech:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Odkazy na vlastnosti můžete také použít v dotazech. Například `SELECT * FROM Families f WHERE f.isRegistered` vrátí položku JSON obsahující `isRegistered` vlastnost s `true`hodnotou rovnou . Jakákoli jiná hodnota, `false` `null`například , `<object>`, `<array>` `Undefined` `<number>`, `<string>`, , nebo , vylučuje položku z výsledku.

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Klíčové slovo IN](sql-query-keywords.md#in)
- [FROM klauzule](sql-query-from.md)