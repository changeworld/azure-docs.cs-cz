---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 94fe099984fae77c65658d7085a8540ff4f2448b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175463"
---
Tato část popisuje omezení pro službu Azure Storage a požadované konvence pojmenování pro soubory Azure, objekty BLOB bloků Azure a objekty BLOB stránky Azure, podle služby Data Box Gateway/Data Box Edge. Pečlivě si zkontrolujte limity úložiště a dodržujte všechna doporučení.

Nejnovější informace o limitech služeb úložiště Azure a doporučených postupech pro pojmenování sdílených složek, kontejnerů a souborů najdete na adrese:

- [Pojmenování a odkazování na kontejnery](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blokovat objekty BLOB a konvence objektů blob stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud existují nějaké soubory nebo adresáře, které překračují limity služby Azure Storage nebo neodpovídají konvencím pojmenování souborů/objektů blob Azure, pak se tyto soubory nebo adresáře nedevolují do úložiště Azure prostřednictvím služby Brána/Data Box Edge.