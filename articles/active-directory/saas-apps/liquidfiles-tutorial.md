---
title: 'Kurz: Integrace služby Azure Active Directory s LiquidFiles | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LiquidFiles.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cb517134-0b34-4a74-b40c-5a3223ca81b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6514594d3119ebf8fab774c3e84c85e34bdfeaf4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67097935"
---
# <a name="tutorial-azure-active-directory-integration-with-liquidfiles"></a>Kurz: Integrace služby Azure Active Directory s LiquidFiles

V tomto kurzu se dozvíte, jak integrovat LiquidFiles s Azure Active Directory (Azure AD).
Integrace LiquidFiles s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k LiquidFiles.
* Můžete povolit uživatelům, aby se automaticky přihlásili k LiquidFiles (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s LiquidFiles, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením LiquidFiles

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LiquidFiles podporuje **sp** inicioval sso

## <a name="adding-liquidfiles-from-the-gallery"></a>Přidání liquidfiles z galerie

Chcete-li nakonfigurovat integraci LiquidFiles do Azure AD, musíte přidat LiquidFiles z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat LiquidFiles z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LiquidFiles**, z panelu výsledků vyberte **LiquidFiles** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

    ![LiquidFiles v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí LiquidFiles na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v LiquidFiles.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí LiquidFiles, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace LiquidFiles Single Sign-On](#configure-liquidfiles-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte LiquidFiles testovací ho uživatele](#create-liquidfiles-test-user)** – mít protějšek Britta Simon v LiquidFiles, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí LiquidFiles, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **LiquidFiles** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![LiquidFiles Informace o jedné přihlašovací službě domény a adresy URL](common/sp-identifier-reply.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<YOUR_SERVER_URL>/saml/init`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<YOUR_SERVER_URL>`

    c. Do textového pole **Adresa URL pro odpověď** zadejte adresu URL pomocí následujícího vzoru:`https://<YOUR_SERVER_URL>/saml/consume`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte [se na tým podpory klienta LiquidFiles](https://www.liquidfiles.com/support.html) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. V části **Podpisový certifikát SAML** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Podpisový certifikát SAML.**

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **Podpisový certifikát SAML** zkopírujte **thumbprint** a uložte jej do počítače.

    ![Kopírovat kryptografický otisk, hodnota](common/copy-thumbprint.png)

7. V části **Nastavit LiquidFiles** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-liquidfiles-single-sign-on"></a>Konfigurace jednotného přihlašování LiquidFiles

1. Přihlaste se k webu společnosti LiquidFiles jako správce.

1. V nabídce klikněte **na Jedno přihlášení** v **konfiguraci admin > konfigurace.**

1. Na stránce **Konfigurace jednotného přihlášení proveďte** následující kroky

    ![Konfigurace jednotného přihlašování](./media/liquidfiles-tutorial/tutorial_single_01.png)

    a. Jako **metodu jednotného přihlášení**vyberte **možnost SAML 2**.

    b. V textovém poli **Adresa URL přihlášení k IDP** vložte hodnotu přihlašovací adresy **URL**, kterou jste zkopírovali z webu Azure Portal.

    c. V textovém poli **Url odhlášení protokolu IDp** vložte hodnotu **adresy URL odhlášení**, kterou jste zkopírovali z webu Azure Portal.

    d. V textovém poli **Otisk prstu certifikátu IDP** vložte hodnotu **THUMBPRINT,** kterou jste zkopírovali z portálu Azure..

    e. Do textového pole Formát identifikátoru `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`názvu zadejte hodnotu .

    f. Do textového pole Kontext Authn `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport`zadejte hodnotu .

    g. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k LiquidFiles.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **LiquidFiles**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **LiquidFiles**.

    ![Odkaz LiquidFiles v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-liquidfiles-test-user"></a>Vytvořit uživatele testu LiquidFiles

Cílem této části je vytvořit uživatele s názvem Britta Simon v LiquidFiles. Spolupracujte se správcem serveru LiquidFiles a před přihlášením do aplikace LiquidFiles se přidejte jako uživatel.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici LiquidFiles na přístupovém panelu, by měl být automaticky přihlášeni k LiquidFiles, pro které nastavíte přivytváření přistupuje. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

