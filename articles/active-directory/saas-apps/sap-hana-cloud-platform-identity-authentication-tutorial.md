---
title: 'Kurz: Azure Active Directory integrace s ověřováním identity cloudové platformy SAP | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP pro ověřování identity Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95be73bd125c124409585a478fa9707e7b6a2ac2
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289063"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s ověřováním identity Cloud platformou SAP

V tomto kurzu se dozvíte, jak integrovat ověřování identity cloudových platforem SAP pomocí Azure Active Directory (Azure AD). Když integruje ověřování identity cloudové platformy SAP s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k ověřování identity cloudové platformy SAP
* Umožněte, aby se vaši uživatelé automaticky přihlásili k ověřování identity cloudové platformy SAP pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr ověřování identity cloudové platformy SAP – jednotné přihlašování (SSO) s podporou jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Ověřování identity cloudové platformy SAP podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování
* Po nakonfigurování ověřování identity cloudové platformy SAP můžete vyhovět ovládacím prvkům relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

Než se podrobně k technickým podrobnostem, je důležité pochopit koncepty, na které se chystáte se podívat. Ověřování identity a Active Directory Federation Services (AD FS) pro cloudovou platformu SAP vám umožní implementovat jednotné přihlašování napříč aplikacemi nebo službami, které jsou chráněné službou Azure AD (jako IdP) s aplikacemi a službami SAP chráněnými pomocí cloudu SAP. Ověřování identity platformy.

V současné době ověřování identity cloudové platformy SAP funguje jako zprostředkovatel identity proxy pro aplikace SAP. Azure Active Directory v této instalaci slouží jako vedoucí poskytovatel identity. 

Tento vztah znázorňuje následující diagram:

![Vytváří se testovací uživatele služby Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

V rámci této instalace je tenant ověřování identity cloudové platformy SAP nakonfigurovaný jako důvěryhodná aplikace v Azure Active Directory.

Všechny aplikace a služby SAP, které chcete chránit tímto způsobem, se následně nakonfigurují v konzole pro správu ověřování identity v cloudové platformě SAP.

Proto musí být autorizace pro udělení přístupu k aplikacím a službám SAP prováděna při ověřování identity cloudové platformy SAP (na rozdíl od Azure Active Directory).

Konfigurací ověřování identity cloudové platformy SAP jako aplikace prostřednictvím služby Azure Active Directory Marketplace nemusíte konfigurovat jednotlivé deklarace identity nebo kontrolní výrazy SAML.

> [!NOTE]
> Obě strany právě testovaly jenom jednotné přihlašování k webu. Toky, které jsou nezbytné pro komunikaci z App-to-API nebo API-to-API, by měly fungovat, ale ještě nebyly testovány. Budou testovány během následných aktivit.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Přidání ověřování identity cloudové platformy SAP z Galerie

Pokud chcete nakonfigurovat integraci ověřování identity cloudové platformy SAP do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat ověřování identity cloudové platformy SAP z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **ověřování identity cloudové platformy SAP** .
1. Na panelu výsledků vyberte možnost **ověřování identity cloudové platformy SAP** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Konfigurace a testování jednotného přihlašování Azure AD pro ověřování identity v cloudové platformě SAP

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ověřování identity cloudové platformy SAP pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ověřování identity v cloudové platformě SAP.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ověřování identity cloudové platformy SAP, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování pro ověřování identity cloudové platformy SAP](#configure-sap-cloud-platform-identity-authentication-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořit testovacího uživatele ověřování identity pro cloudovou platformu SAP](#create-sap-cloud-platform-identity-authentication-test-user)** – Chcete-li mít protějšek B. Simon v ověřování identity cloudové platformy SAP, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **ověřování identity cloudové platformy SAP** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurační oddíl SAML** , pokud chcete nakonfigurovat v režimu iniciované **IDP**proveďte následující kroky:

    ![Informace o jednotném přihlašování k doméně a adresám URL ověřování identity cloudové platformy SAP](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta podpory pro ověřování identity cloudové platformy SAP](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) . Pokud nerozumíte hodnotě identifikátoru, přečtěte si dokumentaci k ověřování identity cloudové platformy SAP týkající se [Konfigurace klienta SAML 2,0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované pomocí **SP**:

    ![Informace o jednotném přihlašování k doméně a adresám URL ověřování identity cloudové platformy SAP](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu pomocí skutečné přihlašovací adresy URL. Použijte prosím svoji konkrétní přihlašovací adresu URL obchodní aplikace. Pokud máte nějaké pochybnosti, obraťte se na [tým podpory klienta podpory pro ověřování identity cloudové platformy SAP](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) .

1. Aplikace pro ověřování identity v cloudové platformě SAP očekává kontrolní výrazy SAML v konkrétním formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě výše očekává aplikace ověřování identity cloudové platformy SAP v odpovědi SAML několik atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name (Název) | Zdrojový atribut|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a stáhněte **XML metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. V části **nastavení ověřování identity cloudové platformy SAP** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k ověřování identity cloudové platformy SAP.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SAP Cloud Platform ověřování identity ověřování**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Konfigurace jednotného přihlašování pro ověřování identity cloudové platformy SAP

1. Pokud chcete pro vaši aplikaci nakonfigurovat jednotné přihlašování, přečtěte si v konzole pro správu ověřování identity cloudové platformy SAP. Adresa URL má následující vzor: `https://<tenant-id>.accounts.ondemand.com/admin`. Pak si přečtěte dokumentaci týkající se ověřování identity cloudové platformy SAP při [integraci s Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. V Azure Portal klikněte na tlačítko **Uložit** .

3. Pokračujte následujícími možnostmi pouze v případě, že chcete přidat a povolit jednotné přihlašování pro jinou aplikaci SAP. Opakujte kroky uvedené v části **Přidání ověřování identity cloudové platformy SAP z Galerie**.

4. V Azure Portal na stránce integrace aplikace **ověřování identity cloudové platformy SAP** vyberte možnost **propojené přihlašování**.

    ![Konfigurace propojeného přihlašování](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Konfiguraci uložte.

> [!NOTE]
> Nová aplikace využívá konfiguraci jednotného přihlašování předchozí aplikace SAP. Ujistěte se, že používáte stejné podnikové zprostředkovatele identity v konzole pro správu ověřování identity v cloudové platformě SAP.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Vytvořit testovacího uživatele ověřování identity pro cloudovou platformu SAP

Nemusíte vytvářet uživatele v rámci ověřování identity cloudové platformy SAP. Uživatelé, kteří jsou v úložišti uživatelů Azure AD, můžou používat funkce jednotného přihlašování.

Ověřování identity cloudové platformy SAP podporuje možnost federace identit. Tato možnost umožňuje aplikaci ověřit, jestli existují uživatelé ověření podnikovým zprostředkovatelem identity v úložišti uživatelů ověřování identity cloudové platformy SAP.

Možnost federace identit je ve výchozím nastavení zakázána. Pokud je povolená federace identit, může k aplikaci přistupovat jenom uživatelé, kteří jsou importované v rámci ověřování identity cloudové platformy SAP.

Další informace o tom, jak povolit nebo zakázat federaci identity pomocí ověřování identity v cloudové platformě SAP, najdete v části "povolení federace identit pomocí ověřování identity cloudové platformy SAP" v tématu [Konfigurace federace identit s uživatelským úložištěm ověřování identity pro cloudovou platformu SAP](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ověřování identity cloudové platformy SAP na přístupovém panelu byste měli být automaticky přihlášeni k ověřování identity cloudové platformy SAP, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si ověřování identity cloudové platformy SAP pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit ověřování identity v cloudové platformě SAP pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
