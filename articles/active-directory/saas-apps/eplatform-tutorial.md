---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s ePlatform | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ePlatform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c93e008-09f3-4937-9651-262993603f96
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9dd7781263887a21597f32c74bd51854cc0dcfc
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081915"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eplatform"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s ePlatform

V tomto kurzu se dozvíte, jak integrovat ePlatform s Azure Active Directory (Azure AD). Když integrujete ePlatform s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k ePlatform.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k ePlatform svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* ePlatform odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ePlatform podporuje jednotné přihlašování **IDP** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-eplatform-from-the-gallery"></a>Přidání ePlatform z Galerie

Pokud chcete nakonfigurovat integraci ePlatform do služby Azure AD, musíte přidat ePlatform z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ePlatform** .
1. Na panelu výsledků vyberte **ePlatform** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-eplatform"></a>Konfigurace a testování jednotného přihlašování Azure AD pro ePlatform

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ePlatform pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ePlatform.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ePlatform, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte EPLATFORM SSO](#configure-eplatform-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte ePlatform Test User](#create-eplatform-test-user)** -to, abyste měli protějšek B. Simon v ePlatform, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **EPlatform** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. ePlatform aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace ePlatform několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut |
    | ---------------| --------------- |
    | názvu | user.userprincipalname |

1. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

1. V části **podpisový certifikát SAML** zkopírujte **hodnotu kryptografického otisku** a uložte ji do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

1. V části **Nastavení ePlatform** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k ePlatform.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **ePlatform**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-eplatform-sso"></a>Konfigurace jednotného přihlašování ePlatform

Ke konfiguraci jednotného přihlašování na straně **ePlatform** je potřeba odeslat **hodnotu kryptografického otisku** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory ePlatform](https://help.eplatform.co/hc/en-us). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-eplatform-test-user"></a>Vytvořit testovacího uživatele ePlatform

V této části vytvoříte uživatele s názvem B. Simon v ePlatform. Pokud chcete přidat uživatele na platformě ePlatform, pracujte s [týmem podpory ePlatform](https://help.eplatform.co/hc/en-us) . Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

## <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici ePlatform, měli byste se automaticky přihlásit k ePlatform, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si ePlatform s Azure AD](https://aad.portal.azure.com/)