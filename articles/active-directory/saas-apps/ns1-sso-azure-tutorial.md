---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s NS1 SSO pro Azure | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NS1 SSO pro Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 24a1afb6-b8b6-4787-bd4b-8fe3a32f8def
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e917265f4bf6f857a0eada2433f0a0e4e24d7c5
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565566"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s NS1 SSO pro Azure

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování NS1 pro Azure pomocí Azure Active Directory (Azure AD). Při integraci NS1 SSO pro Azure se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k NS1 SSO pro Azure.
* Umožněte uživatelům, aby se automaticky přihlásili k NS1 SSO pro Azure pomocí svých účtů Azure AD.
* Spravujte své účty na jednom centrálním místě, Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* NS1 SSO pro předplatné Azure jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* NS1 SSO pro Azure podporuje aktualizace SP a IDP, které zahájily jednotné přihlašování.
* Po nakonfigurování jednotného přihlašování NS1 pro Azure můžete vyhovět řízení relace. Tím se chrání exfiltrace a infiltrace citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>Přidání NS1 jednotného přihlašování pro Azure z Galerie

Pokud chcete nakonfigurovat integraci NS1 jednotného přihlašování pro Azure do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat NS1 SSO pro Azure z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ns1 SSO pro Azure** .
1. Z panelu výsledků vyberte **ns1 SSO pro Azure** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Konfigurace a testování jednotného přihlašování Azure AD pro NS1 SSO pro Azure

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí NS1 jednotného přihlašování pro Azure pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, vytvořte propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v NS1 SSO pro Azure.

Tady jsou obecné kroky pro konfiguraci a testování jednotného přihlašování Azure AD s NS1 SSO pro Azure:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.

    a. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.

    b. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. Nakonfigurujte jednotné přihlašování **[ns1 SSO pro Azure SSO](#configure-ns1-sso-for-azure-sso)** a nakonfigurujte nastavení jednotného přihlašování na straně aplikace.

    a. **[Vytvořte ns1 SSO pro Azure Test User](#create-an-ns1-sso-for-azure-test-user)** , aby měl protějšek B. Simon v rámci ns1 jednotného přihlašování pro Azure. Tato strana je propojená se zastoupením uživatele v Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **ns1 SSO pro službu Azure** Application Integration vyhledejte část **Správa** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky nastavení jednotného přihlašování pomocí stránky SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v části **základní konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte následující adresu URL: `https://api.nsone.net/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, která používá následující vzor: `https://api.nsone.net/saml/sso/<ssoid>`

1. Vyberte **nastavit další adresy URL**a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciované **SP** :

    Do textového pole **Adresa URL pro přihlášení** zadejte následující adresu url: `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není skutečná. Aktualizujte hodnotu adresy URL odpovědi skutečnou adresou URL odpovědi. Pokud chcete získat hodnotu, obraťte se na [tým podpory ns1 SSO pro Azure Client support](mailto:techops@nsone.net) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. NS1 SSO pro aplikace Azure očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele & deklarací** na stránce integrace aplikací. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu tužky a otevřete dialogové okno **atributy uživatele** .

    ![Snímek obrazovky s atributy uživatele & deklarací v části s ikonou tužky zvýrazněné](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Vyberte název atributu pro úpravu deklarace identity.

    ![Snímek obrazovky s atributy uživatele & deklarací identity se zvýrazněným názvem atributu](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Vyberte **transformaci**.

    ![Snímek obrazovky s oddílem pro správu deklarace se zvýrazněnou transformací](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. V části **Spravovat transformaci** proveďte následující kroky:

    ![Snímek obrazovky oddílu spravovat transformaci se zvýrazněnými různými poli](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Jako **transformaci**vyberte **ExactMailPrefix ()** .

    1. Jako **parametr 1**vyberte **User. userPrincipalName** .

    1. Vyberte **Přidat**.

    1. Vyberte **Save** (Uložit).

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte tlačítko Kopírovat. Tím se zkopíruje **Adresa URL federačních metadat aplikace** a uloží se do počítače.

    ![Snímek pro podpisový certifikát SAML s zvýrazněným tlačítkem pro kopírování](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory** > **uživatelů** > **všech uživatelích**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:

   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište hodnotu zobrazenou v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k NS1 SSO pro Azure.

1. V Azure Portal vyberte **podnikové aplikace** > **všech aplikacích**.
1. V seznamu aplikace vyberte **ns1 SSO pro Azure**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky oddílu Správa se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Snímek stránky uživatelů a skupin se zvýrazněným možností přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** . Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-ns1-sso-for-azure-sso"></a>Konfigurace jednotného přihlašování NS1 pro jednotné přihlašování Azure

Pokud chcete nakonfigurovat jednotné přihlašování na straně NS1 SSO pro Azure, musíte poslat adresu URL federačních metadat aplikace pro [tým podpory ns1 SSO pro Azure](mailto:techops@nsone.net). Nakonfigurují toto nastavení tak, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Vytvoření NS1 jednotného přihlašování pro Azure Test User

V této části vytvoříte uživatele s názvem B. Simon v jednotném přihlašování NS1 pro Azure. Pracujte s NS1 SSO pro tým podpory Azure a přidejte uživatele do NS1 SSO pro platformu Azure. Jednotné přihlašování nemůžete použít, dokud nevytvoříte a neaktivujete uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když vyberete dlaždici NS1 SSO pro Azure na přístupovém panelu, měli byste se automaticky přihlásit k NS1 SSO pro Azure, pro které jste nastavili jednotné přihlašování. Další informace najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si NS1 SSO pro Azure pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)