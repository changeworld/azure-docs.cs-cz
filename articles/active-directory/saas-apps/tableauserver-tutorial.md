---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování se serverem Tableau | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Tableau serverem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d19a7cc8d81f9e6e913f147b24c5cce03ff82027
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986729"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Kurz: Azure Active Directory integrace jednotného přihlašování se serverem Tableau

V tomto kurzu se dozvíte, jak integrovat Server Tableau s Azure Active Directory (Azure AD). Při integraci serveru Tableau s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k Tableau serveru.
* Umožněte uživatelům, aby se automaticky přihlásili k Tableau serveru pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným Tableau serverem jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Tableau Server podporuje jednotné přihlašování iniciované v **SP**
* Po nakonfigurování serveru Tableau můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>Přidání serveru Tableau z Galerie

Pokud chcete nakonfigurovat integraci serveru Tableau do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Tableau Server z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Tableau Server** .
1. Na panelu výsledků vyberte **Server Tableau** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Tableau Server

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí serveru Tableau pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Tableau serveru.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí serveru Tableau, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace Tableau serveru SSO](#configure-tableau-server-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele serveru Tableau](#create-tableau-server-test-user)** , abyste měli protějšek B. Simon na serveru Tableau, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **serveru Tableau** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://azure.<domain name>.link`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://azure.<domain name>.link`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Předchozí hodnoty nejsou reálné hodnoty. Aktualizujte hodnoty skutečnou adresou URL a identifikátorem na stránce konfigurace serveru Tableau, která je vysvětlena dále v tomto kurzu.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. V části **nastavení serveru Tableau** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k serveru Tableau.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Tableau Server**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-tableau-server-sso"></a>Konfigurace jednotného přihlašování Tableau serveru

1. Pokud chcete pro vaši aplikaci nakonfigurovat jednotné přihlašování, musíte se přihlásit k tenantovi serveru Tableau jako správce.

2. Na kartě **Konfigurace** vyberte **identita uživatele & přístup**a pak vyberte kartu metoda **ověřování** .

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na stránce **Konfigurace** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. V případě **metody ověřování**vyberte SAML.

    b. Zaškrtněte políčko **Povolit ověřování SAML pro server**.

    c. Tableau návratová adresa URL serveru: adresa URL, na kterou budou uživatelé serveru Tableau přístup, například <http://tableau_server>. Použití `http://localhost` se nedoporučuje. Použití adresy URL s koncovým lomítkem (například `http://tableau_server/`) není podporováno. Zkopírujte **návratovou adresu URL serveru Tableau** a vložte ji do TEXTOVÉHO pole **URL pro přihlášení** v **základní části Konfigurace SAML** v Azure Portal

    d. ID entity SAML – ID entity jednoznačně identifikuje vaši instalaci serveru Tableau na IdP. Sem můžete zadat adresu URL serveru Tableau, pokud se vám to líbí, ale nemusí se jednat o adresu URL vašeho serveru Tableau. Zkopírujte **ID entity SAML** a vložte je do textového pole **identifikátor** v **základní části Konfigurace SAML** v Azure Portal

    e. Klikněte na **Stáhnout soubor METADAT XML** a otevřete ho v aplikaci textový editor. Vyhledejte adresu URL služby pro příjemce kontrolního výrazu s http post a index 0 a zkopírujte adresu URL. Nyní jej vložte do pole Komu **Adresa URL odpovědi** v **základní části konfigurace SAML** v Azure Portal

    f. Vyhledejte soubor federačních metadat stažený z Azure Portal a pak ho nahrajte do **souboru metadat SAML IDP**.

    g. Zadejte názvy atributů, které IdP používá k ukládání uživatelských jmen, zobrazovaných jmen a e-mailových adres.

    h. Klikněte na **Uložit**.

    > [!NOTE]
    > Zákazník musí nahrávat všechny certifikáty v konfiguraci Tableau serveru SAML pro jednotné přihlašování a v toku jednotného přihlašování se bude ignorovat. Pokud potřebujete pomáhat s konfigurací SAML na serveru Tableau, přečtěte si článek [Konfigurace SAML](https://help.tableau.com/current/online/en-us/saml_config_azure_ad.htm).

### <a name="create-tableau-server-test-user"></a>Vytvořit testovacího uživatele serveru Tableau

Cílem této části je vytvořit uživatele s názvem B. Simon na serveru Tableau. Musíte zřídit všechny uživatele na serveru Tableau.

Uživatelské jméno uživatele by se mělo shodovat s hodnotou, kterou jste nakonfigurovali ve vlastním atributu Azure AD **uživatelského jména**. Po správném mapování by integrace měla fungovat s konfigurací jednotného přihlašování Azure AD.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na správce serveru Tableau ve vaší organizaci.

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Tableau Server na přístupovém panelu, měli byste se automaticky přihlásit k serveru Tableau, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Tableau Server s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)