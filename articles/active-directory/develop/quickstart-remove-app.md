---
title: Odebrání aplikace registrované na platformě microsoft identit | Azure
description: Zjistěte, jak odebrat aplikaci zaregistrovanou na platformě Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 3cc9e4458f14a63bad7f484bc16683248895ede9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240846"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Úvodní příručka: Odebrání aplikace registrované na platformě identit Microsoftu

Podnikoví vývojáři a poskytovatelé softwaru jako služby (SaaS), kteří mají zaregistrované aplikace na platformě Microsoft Identity Platform, můžou potřebovat odebrat registraci aplikace.

V tomto rychlém startu se naučíte:

* Odebrání aplikace vytvořené vámi nebo vaší organizací
* Odebrání aplikace vytvořené jinou organizací

## <a name="prerequisites"></a>Požadavky

Musíte mít klienta, který má aplikace registrované na něj. Informace o přidání a registraci aplikací najdete v tématu [Registrace aplikace na platformě Microsoft Identity Platform](quickstart-register-app.md).

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Odebrání aplikace vytvořené vámi nebo vaší organizací

Aplikace, které jste zaregistrovali vy nebo vaše organizace, jsou reprezentované objektem aplikace a instančním objektem ve vašem tenantovi. Další informace najdete v tématu [Objekty aplikací a instanční objekty](active-directory-application-objects.md).

### <a name="to-remove-an-application"></a>Odebrání aplikace

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
2. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
3. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak vyberte Registrace **aplikací**. Vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat. Jakmile vyberete aplikaci, zobrazí se stránka **Přehled** dané aplikace.
4. Na stránce **Přehled** vyberte **Odstranit**.
5. Vybráním **Ano** potvrďte, že chcete aplikaci odstranit.

   > [!NOTE]
   > Abyste mohli odstranit aplikaci, musíte být uvedeni jako vlastník aplikace nebo mít oprávnění správce.

## <a name="remove-an-application-authored-by-another-organization"></a>Odebrání aplikace vytvořené jinou organizací

Pokud se díváte na **Registrace aplikací** v kontextu tenanta, na kartě **Všechny aplikace** se zobrazí podmnožina aplikací, které jsou z jiného tenanta a do vašeho tenanta se zaregistrovaly po vyjádření souhlasu. Konkrétněji je reprezentuje pouze instanční objekt ve vašem tenantovi bez odpovídajícího objektu aplikace. Další informace o rozdílech mezi objekty aplikací a instančními objekty najdete v tématu [Objekty aplikací a instanční objekty v Azure AD](active-directory-application-objects.md).

Pokud chcete odebrat přístup aplikace k vašemu adresáři (po udělení souhlasu), musí správce společnosti odebrat instanční objekt aplikace. Tento správce musí mít přístup globálního správce a instanční objekt může odebrat prostřednictvím webu Azure Portal nebo použít [rutiny Azure AD PowerShellu](https://go.microsoft.com/fwlink/?LinkId=294151) k odebrání přístupu.

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto rychlých startech souvisejících se správou aplikací:

* [Registrace aplikace na platformě Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md)
* [Konfigurace aplikace pro zveřejnění webových rozhraní API](quickstart-configure-app-expose-web-apis.md)
* [Úprava účtů podporovaných aplikací](quickstart-modify-supported-accounts.md)
