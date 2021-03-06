---
title: Vytvoření a publikování produktu ve službě Azure API Management
description: Zjistěte, jak vytvářet a publikovat produkty ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 686d6e2ff01fd4d8283350ab8097e1f73c914af0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260992"
---
# <a name="create-and-publish-a-product"></a>Vytvoření a publikování produktu  

Ve službě Azure API Management obsahuje produkt jedno nebo více rozhraní API a také kvótu využití a podmínky použití. Jakmile je projekt publikovaný, vývojáři se můžou přihlásit k jeho odběru a začít používat jeho rozhraní API.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření a publikování produktu
> * Přidání rozhraní API do produktu

![Kurz přidání produktu](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Požadavky

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Dokončete následující rychlý start: [Vytvořte instanci Azure API Management](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Vytvoření a publikování produktu

![Přidání produktu](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. Kliknutím na **Produkty** v nabídce na levé straně zobrazte stránku **Produkty**.
2. Klikněte na tlačítko **+ Přidat**.

    Při přidávání produktu je potřeba zadat následující informace: 

    | Name (Název)                     | Popis                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Zobrazované jméno             | Název, který se má zobrazit na **portálu pro vývojáře**.                                                                                                                                                                                                                                                        |
    | Name (Název)                     | Popisný název produktu.                                                                                                                                                                                                                                                                                      |
    | Popis              | Do pole **Popis** můžete zadat podrobné informace o produktu, například jeho účel, rozhraní API, ke kterým poskytuje přístup, a další užitečné informace.                                                                                                                                               |
    | Stav                    | Pokud chcete produkt publikovat, stiskněte **Publikováno**. Předtím, než bude možné volat rozhraní API v produktu, je nutné produkt publikovat. Ve výchozím nastavení jsou nové produkty nepublikované a viditelné pouze pro skupinu **Správci**.                                                                                      |
    | Vyžaduje předplatné    | Zaškrtněte políčko **Vyžadovat předplatné**, pokud se k používání produktu vyžaduje přihlášení uživatele k odběru.                                                                                                                                                                                                                                   |
    | Vyžaduje schválení        | Zaškrtněte políčko **Vyžadovat schválení**, pokud chcete, aby pokusy o přihlášení k odběru produktu kontroloval a následně přijímal nebo odmítal správce. Pokud toto políčko není zaškrtnuté, pokusy o přihlášení k odběru se automaticky schvalují.                                                                                                                         |
    | Omezení počtu předplatných | Pokud chcete omezit počet více souběžných předplatných, zadejte omezení předplatných.                                                                                                                                                                                                                                |
    | Právní podmínky              | Pro produkt můžete zahrnout podmínky použití, které musí předplatitelé přijmout, aby mohli produkt využívat.                                                                                                                                                                                                             |
    | Rozhraní API                     | Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. <br/> Při vytváření produktu můžete přidat existující rozhraní API. Rozhraní API můžete přidat do produktu později, a to buď na stránce **Nastavení** produktu, nebo při vytváření rozhraní API. |

3. Kliknutím na **Vytvořit** vytvořte nový produkt.

### <a name="add-more-configurations"></a>Přidání více konfigurací

Po uložení produktu můžete pokračovat v jeho konfiguraci zvolením karty **Nastavení**. 

Na kartě **Předplatná** můžete zobrazit předplatitele produktu nebo je přidat.

Nastavte viditelnost produktu pro vývojáře nebo hosty na kartě **Řízení přístupu.**

## <a name="add-apis-to-a-product"></a><a name="add-apis"> </a>Přidání rozhraní API do produktu

Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Při vytváření produktu můžete přidat existující rozhraní API. Rozhraní API můžete do produktu přidat i později, a to na stránce **Nastavení** produktu nebo při vytváření rozhraní API.

Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci služby APIM, již jste správcem, takže jste ve výchozím nastavení přihlášeni k odběru všech produktů.

### <a name="add-an-api-to-an-existing-product"></a>Přidání rozhraní API do existujícího produktu

![přidání rozhraní API produktu](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. Na kartě **Produkty** vyberte produkt.
2. Přejděte na kartu **Rozhraní API**.
3. Klikněte na tlačítko **+ Přidat**.
4. Zvolte rozhraní API a klikněte na **Vybrat**.

> [!TIP]
> Uživatelské předplatné *produktu* můžete vytvořit nebo aktualizovat s použitím vlastních klíčů předplatného [prostřednictvím rozhraní REST API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription/createorupdate) nebo pomocí příkazu PowerShellu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření a publikování produktu
> * Přidání rozhraní API do produktu

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Vytvoření prázdného rozhraní API a napodobení odpovědí rozhraní API](mock-api-responses.md)
