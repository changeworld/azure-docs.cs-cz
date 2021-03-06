---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s VELPIC SAML | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a VELPIC SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241564"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s VELPIC SAML

V tomto kurzu se dozvíte, jak integrovat VELPIC SAML pomocí Azure Active Directory (Azure AD). Když integrujete VELPIC SAML s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k VELPIC SAML.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k VELPIC SAML pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadované součásti

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* VELPIC předplatného jednotného přihlašování (SSO) s podporou SAML.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* VELPIC SAML podporuje jednotné přihlašování (SSO) iniciované **SP**

## <a name="adding-velpic-saml-from-the-gallery"></a>Přidání VELPIC SAML z Galerie

Pokud chcete nakonfigurovat integraci VELPIC SAML do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat VELPIC SAML z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **VELPIC SAML** .
1. Na panelu výsledků vyberte **VELPIC SAML** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Konfigurace a testování jednotného přihlašování Azure AD pro VELPIC SAML

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí VELPIC SAML pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v VELPIC SAML.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí VELPIC SAML, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte VELPIC SSO pro SAML](#configure-velpic-saml-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte VELPIC testovacího uživatele SAML](#create-velpic-saml-test-user)** – abyste měli protějšek B. Simon ve VELPIC SAML, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **VELPIC SAML** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<sub-domain>.velpicsaml.net`.

    b) Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Upozorňujeme, že přihlašovací adresa URL bude poskytnuta týmem VELPIC SAML a hodnota identifikátoru bude k dispozici při konfiguraci modulu plug-in SSO na straně VELPIC SAML. Tuto hodnotu musíte zkopírovat ze stránky aplikace VELPIC SAML a vložit ji sem.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení VELPIC SAML** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k VELPIC SAML.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **VELPIC SAML**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-velpic-saml-sso"></a>Konfigurace VELPIC SSO jednotného přihlašování SAML

1. Pokud chcete automatizovat konfiguraci v rámci VELPIC SAML, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit VELPIC SAML** . budete přesměrováni na VELPIC aplikaci SAML. Odtud zadejte přihlašovací údaje správce, které se přihlásí k VELPIC SAML. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-8.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit VELPIC SAML ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu VELPIC SAML společnosti jako správce a proveďte následující kroky:

4. Klikněte na možnost **Spravovat** kartu a přejděte do části **integrace** , kde budete muset kliknout na tlačítko **moduly plug** -in a vytvořit nový modul plug-in pro přihlášení.

    ![Modulu](./media/velpicsaml-tutorial/velpic_1.png)

5. Klikněte na tlačítko **Přidat plugin** .
    
    ![Modulu](./media/velpicsaml-tutorial/velpic_2.png)

6. Klikněte na dlaždici **SAML** na stránce přidat modul plug-in.
    
    ![Modulu](./media/velpicsaml-tutorial/velpic_3.png)

7. Zadejte název nového modulu plug-in SAML a klikněte na tlačítko **Přidat** .

    ![Modulu](./media/velpicsaml-tutorial/velpic_4.png)

8. Podrobnosti zadejte následujícím způsobem:

    ![Modulu](./media/velpicsaml-tutorial/velpic_5.png)

    a. Do textového pole **název** zadejte název modulu plug-in SAML.

    b) Do textového pole **Adresa URL vystavitele** vložte **identifikátor Azure AD** , který jste zkopírovali z okna **Konfigurace přihlášení** Azure Portal.

    c. V **konfiguraci metadat poskytovatele** nahrajte soubor XML metadat, který jste stáhli z Azure Portal.

    d. Pomocí zaškrtávacího políčka **automaticky vytvořit nové uživatele** můžete také povolit povolení pro protokol SAML při zřizování pouze v čase. Pokud uživatel v VELPIC neexistuje a tento příznak není povolený, přihlášení z Azure se nezdaří. Pokud je povolen příznak, uživatel se automaticky zřídí do VELPIC v době přihlášení. 

    e. Do textového pole zkopírujte **adresu URL jednotného přihlašování** a vložte ji do Azure Portal.
    
    f. Klikněte na **Save** (Uložit).

### <a name="create-velpic-saml-test-user"></a>Vytvořit testovacího uživatele VELPIC SAML

Tento krok není obvykle vyžadován, protože aplikace podporuje právě zřizování uživatelů v čase. Pokud Automatické zřizování uživatelů není povoleno, můžete ručně vytvořit uživatele, jak je popsáno níže.

Přihlaste se k VELPIC webu společnosti SAML jako správce a proveďte následující kroky:
    
1. Klikněte na tlačítko Spravovat kartu a přejděte do části Uživatelé a potom kliknutím na tlačítko Nový přidejte uživatele.

    ![Přidat uživatele](./media/velpicsaml-tutorial/velpic_7.png)

2. Na stránce **vytvořit nového uživatele** proveďte následující kroky.

    ![uživatel](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Do textového pole **název** zadejte jméno B.

    b) Do textového pole **příjmení** zadejte jméno Simon.

    c. Do textového pole **uživatelské jméno** zadejte uživatelské jméno B. Simon.

    d. Do textového pole **e-mail** zadejte e-mailovou adresu účtu B.Simon@contoso.com.

    e. Zbývající informace jsou volitelné, můžete je v případě potřeby vyplnit.
    
    f. Klikněte na **Uložit**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

1. Když kliknete na dlaždici VELPIC SAML na přístupovém panelu, měli byste získat přihlašovací stránku VELPIC aplikace SAML. Na přihlašovací stránce byste měli vidět tlačítko **Přihlásit se pomocí Azure AD** .

    ![Modulu](./media/velpicsaml-tutorial/velpic_6.png)

1. Pokud se chcete přihlásit k VELPIC pomocí svého účtu Azure AD, klikněte na tlačítko **Přihlásit se pomocí Azure AD** .

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte VELPIC SAML pomocí Azure AD](https://aad.portal.azure.com/)

