---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s virtuálními počítači s IQNavigator | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IQNavigator virtuálními počítači.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f61681fbe3ec93467db67290fde6548ce62425fc
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75560455"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iqnavigator-vms"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s virtuálními počítači s IQNavigator

V tomto kurzu se dozvíte, jak integrovat virtuální počítače s IQNavigator pomocí Azure Active Directory (Azure AD). Když integrujete virtuální počítače s IQNavigator s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k VIRTUÁLNÍm počítačům s IQNavigator.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k VIRTUÁLNÍm počítačům s IQNavigator pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné IQNavigator virtuálních počítačů s podporou jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.


* Virtuální počítače s IQNavigator podporují jednotné přihlašování **IDP** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.



## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Přidání virtuálních počítačů s IQNavigator z Galerie

Pokud chcete nakonfigurovat integraci virtuálních počítačů s IQNavigator do služby Azure AD, musíte přidat virtuální počítače s IQNavigator z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **IQNavigator virtuální počítače** .
1. Na panelu výsledků vyberte **virtuální počítače s IQNavigator** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-iqnavigator-vms"></a>Konfigurace a testování jednotného přihlašování Azure AD pro virtuální počítače s IQNavigator

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s virtuálními počítači s IQNavigator pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v IQNavigator virtuálních počítačích.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s virtuálními počítači s IQNavigator, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné](#configure-iqnavigator-vms-sso)** přihlašování pro virtuální počítače s IQNavigator – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte virtuální počítače s IQNavigator pro testování](#create-iqnavigator-vms-test-user)** , abyste měli protějšek B. Simon ve virtuálních počítačích IQNavigator, které jsou propojené s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **virtuálních počítačů s IQNavigator** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL: `iqn.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

    c. Klikněte na **nastavit další adresy URL**.

    d. Do textového pole **stav přenosu** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a stavu přenosu. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory virtuálních počítačů s IQNavigator](https://www.beeline.com/support-iqn/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. IQNavigator aplikace očekává jedinečnou hodnotu identifikátoru uživatele v deklaraci identifikátoru názvu. Zákazník může namapovat správnou hodnotu deklarace identifikátoru názvu. V tomto případě jsme namapovali uživatele. UserPrincipalName pro účely ukázky V závislosti na nastaveních vaší organizace byste ale měli namapovat správnou hodnotu.

    ![image](common/edit-attribute.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k VIRTUÁLNÍm počítačům s IQNavigator.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **virtuální počítače s IQNavigator**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-iqnavigator-vms-sso"></a>Konfigurace jednotného přihlašování virtuálních počítačů s IQNavigator

Pokud chcete nakonfigurovat jednotné přihlašování na straně **virtuálních počítačů s IQNavigator** , musíte odeslat **adresu URL federačních metadat aplikace** [týmu podpory virtuálních počítačů s IQNavigator](https://www.beeline.com/support-iqn/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-iqnavigator-vms-test-user"></a>Vytvořit testovacího uživatele pro virtuální počítače s IQNavigator

V této části vytvoříte na virtuálních počítačích s IQNavigator uživatele s názvem Britta Simon. Práce s [IQNavigator virtuálními počítači podporuje tým](https://www.beeline.com/support-iqn/) k přidávání uživatelů na platformě virtuálních počítačů IQNavigator. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici virtuální počítače s IQNavigator, měli byste se automaticky přihlásit k VIRTUÁLNÍm počítačům s IQNavigator, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si virtuální počítače s IQNavigator pomocí Azure AD](https://aad.portal.azure.com/)

