---
title: 'Kurz: Konfigurace automatického zřizování uživatelů Peakonu pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty ve službě Peakon .
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 0a67dc8069ee71305a47bd5d2a724a61cec234a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063369"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Kurz: Konfigurace Peakonu pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny ve Peakonu a Azure Active Directory (Azure AD) k konfiguraci Azure AD tak, aby automaticky zřizovala a odřizovala uživatele nebo skupiny peakonu.

> [!NOTE]
>  Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky

* Tenanta Azure AD.
* [Tenant Peakon](https://peakon.com/us/pricing/).
* Uživatelský účet ve Peakonu s oprávněními správce.

## <a name="assigning-users-to-peakon"></a>Přiřazení uživatelů ke službě Peakon

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup ke službě Peakon. Jakmile se rozhodnete, můžete těmto uživatelům a/nebo skupinám přiřadit peakon podle pokynů zde:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Důležité tipy pro přiřazování uživatelů do Peakonu 

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Peakon otestovat konfiguraci automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke službě Peakon musíte v dialogovém okně přiřazení vybrat libovolnou platnou roli specifickou pro aplikaci (pokud je k dispozici). Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-peakon-for-provisioning"></a>Nastavit Peakon pro zřizování

1.  Přihlaste se ke své [Konzoli pro správu Peakonu](https://app.Peakon.com/login). Klikněte na **Konfigurace**. 

    ![Konzola pro správu Peakon](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Vyberte **integrace**.
    
    ![Peakon-zaměstnanec-poskytování](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Povolit **zřizování zaměstnanců**.

    ![Peakon-zaměstnanec-poskytování](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Zkopírujte hodnoty **pro ADRESU URL SCIM 2.0** a Token **nosiče OAuth**. Tyto hodnoty se zanesou do pole **Adresa URL klienta** a **tajný token** na kartě Zřizování vaší aplikace Peakon na webu Azure Portal.

    ![Peakon vytvořit token](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Přidat Peakon z galerie

Chcete-li konfigurovat Peakon pro automatické zřizování uživatelů pomocí Azure AD, musíte přidat Peakon z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Peakon**, vpanelu výsledků vyberte **Peakon** a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Peakon v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Konfigurace automatického zřizování uživatelů na Peakon 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin ve Peakonu na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování na bázi SAML pro Peakon podle pokynů uvedených v [kurzu Peakon Single sign-on](peakon-tutorial.md). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Peakon ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Peakon**.

    ![Odkaz Peakon v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje správce** zadejte hodnoty adresy URL **SCIM 2.0** a **tokenu OAuth Bearer Token** načtené dříve v adrese URL **klienta** a **tajném tokenu.** Klikněte na **Testovat připojení** a ujistěte se, že Azure AD se může připojit k Peakonu. Pokud se připojení nezdaří, ujistěte se, že váš účet Peakon má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

7. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

8. Klikněte na **Uložit**.

9. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s Peakonem**.

    ![Mapování uživatelů Peakon](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Peakon v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Peakon pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy uživatele Peakon](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Peakonu.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Všechny vlastní uživatelské atributy v Peakonu musí být rozšířeny z `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`peakonského vlastního rozšíření uživatele SCIM .

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)