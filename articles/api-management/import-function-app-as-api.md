---
title: Import aplikace funkce Azure jako rozhraní API ve správě rozhraní API
titleSuffix: Azure API Management
description: V tomto kurzu se dozvíte, jak importovat aplikaci Azure Function App do služby Azure API Management jako rozhraní API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: apimpm
ms.openlocfilehash: c393ba081b480408373ed6867624ac6278c1674e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260951"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Import aplikace Azure Function App jako rozhraní API ve službě Azure API Management

Azure API Management podporuje import aplikací Azure Function App jako nových rozhraní API nebo jejich připojení k existujícím rozhraním API. Tento proces v aplikaci Azure Function App automaticky vygeneruje klíč hostitele, který se pak přiřadí k pojmenované hodnotě ve službě Azure API Management.

Tento článek vás provede importem aplikace Azure Function App jako rozhraní API ve službě Azure API Management. Také popisuje proces testování.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Import aplikace Azure Function App jako rozhraní API
> * Připojení aplikace Azure Function App k rozhraní API
> * Zobrazení klíče hostitele nové aplikace Azure Function App a pojmenované hodnoty ve službě Azure API Management
> * Testovat rozhraní API na portálu Azure Portal
> * Testování rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Požadavky

* Projděte si rychlý start [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
* Zkontrolujte, jestli máte v předplatném aplikaci Azure Functions. Další informace najdete v tématu [Vytvoření aplikace Azure Function App](../azure-functions/functions-create-first-azure-function.md#create-a-function-app). Musí obsahovat funkce s triggerem HTTP a úrovní autorizace nastavenou na *Anonymní* nebo *Funkce*.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a>Import aplikace Azure Function App jako nového rozhraní API

Podle následujícího postupu vytvoříte z aplikace Azure Function App nové rozhraní API.

1. Ve vaší instanci služby **Azure API Management** vyberte v nabídce na levé straně **Rozhraní API**.

2. V seznamu **Přidat nové rozhraní API** vyberte **Function App**.

    ![Přidání z aplikace Function App](./media/import-function-app-as-api/add-01.png)

3. Klikněte na **Procházet** a vyberte funkce, které chcete importovat.

    ![Přidání z aplikace Function App](./media/import-function-app-as-api/add-02.png)

4. Kliknutím na část **Function App** zobrazte seznam dostupných aplikací Function App, ze kterých si můžete vybrat.

    ![Přidání z aplikace Function App](./media/import-function-app-as-api/add-03.png)

5. Vyhledejte aplikaci Function App, ze které chcete importovat funkce, klikněte na ni a stiskněte **Vybrat**.

    ![Přidání z aplikace Function App](./media/import-function-app-as-api/add-04.png)

6. Vyberte funkce, které chcete importovat, a klikněte na **Vybrat**.

    ![Přidání z aplikace Function App](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Můžete importovat pouze funkce, které jsou založené na triggeru HTTP a mají úroveň autorizace nastavenou na *Anonymní* nebo *Funkce*.

7. Přepněte do **úplného** zobrazení a přiřaďte **Produkt** k novému rozhraní API. V případě potřeby upravte jiná předvyplněná pole.

    ![Přidání z aplikace Function App](./media/import-function-app-as-api/add-06.png)

8. Klikněte na **Vytvořit**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a>Připojení aplikace Azure Function App k existujícímu rozhraní API

Podle následujícího postupu připojíte aplikaci Azure Function App k existujícímu rozhraní API.

1. Ve vaší instanci služby **Azure API Management** vyberte v nabídce na levé straně **Rozhraní API**.

2. Zvolte rozhraní API, do kterého chcete importovat aplikaci Azure Function App. Klikněte na **...** a v místní nabídce vyberte **Importovat**.

    ![Připojení z aplikace Function App](./media/import-function-app-as-api/append-01.png)

3. Klikněte na dlaždici **Function App**.

    ![Připojení z aplikace Function App](./media/import-function-app-as-api/append-02.png)

4. V automaticky otevíraném okně klikněte na **Procházet**.

    ![Připojení z aplikace Function App](./media/import-function-app-as-api/append-03.png)

5. Kliknutím na část **Function App** zobrazte seznam dostupných aplikací Function App, ze kterých si můžete vybrat.

    ![Přidání z aplikace Function App](./media/import-function-app-as-api/add-03.png)

6. Vyhledejte aplikaci Function App, ze které chcete importovat funkce, klikněte na ni a stiskněte **Vybrat**.

    ![Přidání z aplikace Function App](./media/import-function-app-as-api/add-04.png)

7. Vyberte funkce, které chcete importovat, a klikněte na **Vybrat**.

    ![Přidání z aplikace Function App](./media/import-function-app-as-api/add-05.png)

8. Klepněte na **tlačítko Importovat**.

    ![Připojení z aplikace Function App](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a><a name="authorization"></a>Autorizace

Při importu aplikace Azure Function App se automaticky vygeneruje:

* Klíč hostitele uvnitř aplikace function app s názvem apim-{*název instance služby Azure API Management*},
* Pojmenovaná hodnota uvnitř instance Azure API Management s názvem {*název instance aplikace Azure Function App*}-key, který obsahuje vytvořený klíč hostitele.

Pro rozhraní API vytvořená po 4. Starší rozhraní API předají klíč hostitele jako [parametr dotazu](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). Toto chování může `PATCH Backend` být změněno prostřednictvím [volání rozhraní REST API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) na entitu *Back-end* přidružené k aplikaci funkce.

> [!WARNING]
> Odebráním nebo změnou hodnoty klíče hostitele aplikace Azure Function App nebo pojmenované hodnoty ve službě Azure API Management se přeruší komunikace mezi službami. Tyto hodnoty se nesynchronizují automaticky.
>
> Pokud potřebujete obměnit klíč hostitele, nezapomeňte upravit také pojmenovanou hodnotu ve službě Azure API Management.

### <a name="access-azure-function-app-host-key"></a>Přístup ke klíči hostitele aplikace Azure Function App

1. Přejděte k vaší instanci aplikace Azure Function App.

2. V přehledu vyberte **Nastavení aplikace Function App**.

    ![Přidání z aplikace Function App](./media/import-function-app-as-api/keys-02-a.png)

3. Klíč se nachází v části **Klíče hostitele**.

    ![Přidání z aplikace Function App](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Přístup k pojmenované hodnotě ve službě Azure API Management

Přejděte k vaší instanci služby Azure API Management a v nabídce na levé straně vyberte **Pojmenované hodnoty**. Tady je uložený klíč aplikace Azure Function App.

![Přidání z aplikace Function App](./media/import-function-app-as-api/keys-01.png)

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a>Testování nového rozhraní API na webu Azure Portal

Operace můžete volat přímo z webu Azure Portal. Web Azure Portal nabízí pohodlný způsob zobrazení a testování operací v rozhraní API.  

1. Vyberte rozhraní API, které jste vytvořili v předchozí části.

2. Vyberte kartu **Test**.

3. Vyberte operaci.

    Stránka zobrazí pole pro parametry dotazu a pole pro hlavičky. Jedním z hlaviček je **Ocp-Apim-Subscription-Key**, pro klíč předplatného produktu, který je přidružen k tomuto rozhraní API. Pokud jste vytvořili instanci služby API Management, jste už správcem a klíč se tedy vyplní automaticky. 

4. Vyberte **Poslat**.

    Back-end předá odpověď **200 OK** a nějaká data.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)
