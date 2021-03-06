---
title: Uživatelský portál pro Server Azure MFA – Azure Active Directory
description: Začínáme s Azure MFA a portálem User Portal.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1869fac973cd4cd68e1e91be89c25fdf1427f6a5
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653211"
---
# <a name="user-portal-for-the-azure-multi-factor-authentication-server"></a>User Portal pro Azure Multi-Factor Authentication Server

User Portal je web služby IIS, který uživatelům umožňuje se zaregistrovat do služby Azure Multi-Factor Authentication (MFA) a spravovat své účty. Uživatel může změnit svoje telefonní číslo, změnit PIN kód nebo obejít dvoustupňové ověřování při příštím přihlášení.

Uživatelé se k portálu User Portal přihlašují svým normálním uživatelským jménem a heslem a buďto dokončí dvoustupňový ověřovací hovor, nebo se ověří tak, že odpoví na bezpečnostní otázky. Pokud je povolený zápis uživatelů, uživatelé si při prvním přihlášení na portálu User Portal nastaví telefonní číslo a PIN kód.

Správce portálu User Portal je možné nastavit a udělit jim oprávnění přidávat nové uživatele a aktualizovat existující uživatele.

V závislosti na vašem prostředí můžete chtít portál User Portal nasadit na stejný server jako Azure Multi-Factor Authentication Server, nebo na jiný server s přístupem k internetu.

> [!IMPORTANT]
> července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali server MFA před 1.

![Přihlašovací stránka uživatelského portálu serveru MFA](./media/howto-mfaserver-deploy-userportal/portal.png)

> [!NOTE]
> Portál User Portal je k dispozici pouze v Azure Multi-Factor Authentication Serveru. Pokud používáte službu Multi-Factor Authentication v cloudu, odkažte své uživatele na témata [Nastavení účtu pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-first-time.md) nebo [Správa nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md).

## <a name="install-the-web-service-sdk"></a>Instalace sady SDK webové služby

V obou scénářích, pokud sada SDK webové služby Azure Multi-Factor Authentication **není** na Azure Multi-Factor Authentication (MFA) Serveru nainstalovaná, proveďte následující kroky.

1. Otevřete konzolu Multi-Factor Authentication Serveru.
2. Přejděte do části **Sada SDK webové služby** a vyberte **Instalovat sadu SDK webové služby**.
3. Dokončete instalaci s použitím výchozích hodnot, pokud je z nějakého důvodu nepotřebujete změnit.
4. Spojte certifikát TLS/SSL s webem ve službě IIS.

Máte-li dotazy týkající se konfigurace certifikátu TLS/SSL na serveru služby IIS, přečtěte si článek [Jak nastavit ssl ve službě IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

Sada SDK webové služby musí být zabezpečena certifikátem TLS/SSL. Pro tento účel stačí certifikát podepsaný svým držitelem. Importujte certifikát do úložiště Důvěryhodné kořenové certifikační úřady účtu místního počítače na webovém serveru portálu User Portal, aby byl tomuto certifikátu při inicializačním připojení TLS důvěřován.

![Nastavení konfigurace MFA Serveru – sada SDK webové služby](./media/howto-mfaserver-deploy-userportal/sdk.png)

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Nasazení portálu User Portal na stejném serveru, jako je Azure Multi-Factor Authentication Server

K instalaci uživatelského portálu na **stejný server** jako server Azure Multi-Factor Authentication Server jsou vyžadovány následující předpoklady:

* Služba IIS, včetně kompatibility s ASP.NET a IIS 6 meta base (pro IIS 7 a novější).
* Účet s právy správce počítače a případně domény. Tento účet musí mít oprávnění k vytvoření skupin zabezpečení Active Directory.
* Zabezpečte uživatelský portál pomocí certifikátu TLS/SSL.
* Zabezpečte webovou službu Azure Multi-Factor Authentication Web Service SDK pomocí certifikátu TLS/SSL.

Pokud chcete nasadit portál User Portal, postupujte takto:

1. Otevřete konzolu Azure Multi-Factor Authentication Serveru, klikněte v nabídce na levé straně na ikonu **User Portal** a potom klikněte na **Nainstalovat portál User Portal**.
2. Dokončete instalaci s použitím výchozích hodnot, pokud je z nějakého důvodu nepotřebujete změnit.
3. Svázání certifikátu TLS/SSL s webem ve službě IIS

   > [!NOTE]
   > Tento certifikát TLS/SSL je obvykle veřejně podepsaný certifikát TLS/SSL.

4. Otevřete webový prohlížeč z libovolného počítače a přejděte na `https://mfa.contoso.com/MultiFactorAuth`adresu URL, kde byl nainstalován uživatelský portál (Příklad: ). Ujistěte se, že se nezobrazí žádná varování nebo chyby týkající se certifikátu.

![Instalace portálu User Portal pro MFA Server](./media/howto-mfaserver-deploy-userportal/install.png)

Máte-li dotazy týkající se konfigurace certifikátu TLS/SSL na serveru služby IIS, přečtěte si článek [Jak nastavit ssl ve službě IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

## <a name="deploy-the-user-portal-on-a-separate-server"></a>Nasazení portálu User Portal na samostatný server

Pokud server, na kterém je spuštěný Azure Multi-Factor Authentication Server, nemá přístup k internetu, měli byste portál User Portal nainstalovat na **samostatný server s přístupem k internetu**.

Pokud vaše organizace jako jednu z metod ověřování používá aplikaci Microsoft Authenticator a chcete nasadit portál User Portal na vlastním serveru, splňte následující požadavky:

* Použijte Azure Multi-Factor Authentication Server verze 6.0 nebo vyšší.
* Nainstalujte portál User Portal na webovém serveru s přístupem k internetu a s Internetovou informační službou (IIS) verze 6.x nebo vyšší.
* Pokud používáte IIS 6.x, ujistěte se, že je nainstalované ASP.NET v2.0.50727, že je zaregistrované a nastavené na **Povoleno**.
* Pokud používáte IIS 7.x nebo vyšší, vyžaduje se služba IIS, včetně základního ověřování, ASP.NET a kompatibility s IIS 6 meta base.
* Zabezpečte uživatelský portál pomocí certifikátu TLS/SSL.
* Zabezpečte webovou službu Azure Multi-Factor Authentication Web Service SDK pomocí certifikátu TLS/SSL.
* Ujistěte se, že se uživatelský portál může připojit k sdk webové služby Azure Multi-Factor Authentication Web Service přes TLS/SSL.
* Ověřte, že se portál User Portal dokáže ověřit pro sadu SDK webové služby Azure Multi-Factor Authentication pomocí přihlašovacích údajů účtu služby ve skupině zabezpečení PhoneFactor Admins. Tento účet služby a skupina musí existovat ve službě Active Directory, pokud Azure Multi-Factor Authentication Server běží na serveru připojeném k doméně. Tento účet služby a skupina existují místně na Azure Multi-Factor Authentication Serveru, pokud není připojený k doméně.

Instalace portálu uživatele na jiném serveru, než je Azure Multi-Factor Authentication Server, vyžaduje následující kroky:

1. **Na MFA Serveru** přejděte do instalační cesty (příklad: C:\Program Files\Multi-Factor Authentication Server) a zkopírujte soubor **MultiFactorAuthenticationUserPortalSetup64** do umístění, ke kterému má přístup server s přístupem k internetu, na který budete MFA Server instalovat.
2. **Na webovém serveru s přístupem k internetu** spusťte instalační soubor MultiFactorAuthenticationUserPortalSetup64 jako správce, změňte v případě potřeby web, a pokud chcete, změňte virtuální adresář na krátký název.
3. Spojte certifikát TLS/SSL s webem ve službě IIS.

   > [!NOTE]
   > Tento certifikát TLS/SSL je obvykle veřejně podepsaný certifikát TLS/SSL.

4. Přejděte do umístění **C:\inetpub\wwwroot\MultiFactorAuth**.
5. V Poznámkovém bloku upravte soubor Web.Config.

    * Vyhledejte klíč **"USE_WEB_SERVICE_SDK"** a změňte **value="false"** na **value="true"**.
    * Vyhledejte klíč **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** a změňte **value=""** na **value="DOMÉNA\Uživatel"**, kde DOMÉNA\Uživatel je účet služby, který je součástí skupiny PhoneFactor Admins.
    * Vyhledejte klíč **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** a změňte **value=""** na **value="Heslo"**, kde Heslo je heslo pro účet úložiště zadaný na předchozím řádku.
    * Najděte **https://www.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx** hodnotu a změňte tuto zástupnou adresu URL na adresu URL sady SDK webové služby, kterou jsme nainstalovali v kroku 2.
    * Uložte soubor Web.Config a zavřete Poznámkový blok.

6. Otevřete webový prohlížeč z libovolného počítače a přejděte na `https://mfa.contoso.com/MultiFactorAuth`adresu URL, kde byl nainstalován uživatelský portál (Příklad: ). Ujistěte se, že se nezobrazí žádná varování nebo chyby týkající se certifikátu.

Máte-li dotazy týkající se konfigurace certifikátu TLS/SSL na serveru služby IIS, přečtěte si článek [Jak nastavit ssl ve službě IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

## <a name="configure-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurace nastavení portálu User Portal v Azure Multi-Factor Authentication Serveru

Teď, když je portál User Portal nainstalovaný, musíte nakonfigurovat Azure Multi-Factor Authentication Server pro práci s portálem.

1. V konzole Multi-Factor Authentication Serveru klikněte na ikonu **User Portal**. Na kartě Nastavení zadejte adresu URL portálu User Portal do pole **Adresa URL portálu User Portal**. Pokud je zapnutá funkce e-mailu, tato adresa URL se vloží do e-mailů, které se uživatelům pošlou po jejich importování do Azure Multi-Factor Authentication Serveru.
2. Vyberte nastavení, které chcete v portálu User Portal použít. Pokud si například uživatelé můžou zvolit metodu ověřování, zkontrolujte, že je kromě metod, ze kterých si mohou vybrat, zaškrtnutá i možnost **Povolit uživatelům výběr metody**.
3. Na kartě Administrators definujte, kdo by měl být **správci.** Podrobná oprávnění správce můžete vytvořit pomocí zaškrtávacích políček a rozevíracích seznamů v polích Přidat nebo upravit.

Volitelná konfigurace:

- **Bezpečnostní otázky** – Můžete definovat schválené bezpečnostní otázky pro vaše prostředí a jazyk, ve kterém se zobrazí.
- **Předané relace** – Můžete konfigurovat integraci portálu User Portal s webem na základě formuláře, který používá MFA.
- **Důvěryhodné IP adresy** – Můžete uživatelům umožnit přeskočení MFA při ověřování z některé z IP adres ze seznamu důvěryhodných IP adres nebo rozsahů IP adres.

![Konfigurace portálu User Portal pro MFA Server](./media/howto-mfaserver-deploy-userportal/config.png)

Server Azure Multi-Factor Authentication poskytuje pro uživatelský portál několik možností. V následující tabulce je seznam těchto možností a vysvětlení, k čemu se používají.

| Nastavení uživatelského portálu | Popis |
|:--- |:--- |
| Adresa URL portálu User Portal | Zadejte adresu URL, na které se portál hostuje. |
| Primární ověření | Zadejte typ ověřování, které se má použít pro přihlašování k portálu. Ověření Windows, Radius nebo LDAP. |
| Umožnit uživatelům přihlášení | Umožní uživatelům zadat uživatelské jméno a heslo na přihlašovací stránce portálu User Portal. Pokud tato možnost není zapnutá, pole budou zobrazena šedě. |
| Povolit zápis uživatele | Umožní uživateli se zapsat do služby Multi-Factor Authentication na stránce nastavení, která ho požádá o další informace, jako je třeba telefonní číslo. Vyzvat k zadání záložního telefonu umožní uživatelům na telefonu zadat záložní telefonní číslo. Výzva k zadání tokenu OATH jiných výrobců umožní uživatelům zadat token OATH od jiného výrobce. |
| Povolit uživatelům zahájení jednorázového přihlášení | Umožní uživatelům zahájení jednorázového přihlášení. Pokud uživatel nastaví tuto možnost, projeví se to při jeho příštím přihlášení. Vyzvat k zadání počtu sekund jednorázového přihlášení zobrazí uživateli pole pro změnu výchozí hodnoty 300 sekund. Jinak bude přeskočení přihlášení platné jen na 300 sekund. |
| Povolit uživatelům výběr metody | Umožní uživatelům vybrat primární způsob kontaktu. Tento způsob může být telefonní hovor, zpráva SMS, mobilní aplikace nebo token OATH. |
| Povolit uživatelům výběr jazyka | Umožní uživatelům změnit jazyk telefonního hovoru, zprávy SMS, mobilní aplikace nebo tokenu OATH. |
| Povolit uživatelům aktivaci mobilní aplikace | Umožní uživatelům vygenerovat aktivační kód pro dokončení procesu aktivace mobilní aplikace, který se použije se serverem.  Můžete také nastavit počet zařízení, na kterých mohu aplikaci aktivovat, v rozmezí 1 až 10. |
| V nouzové situaci použít bezpečnostní otázky | Povolí bezpečnostní otázky, pokud se nezdaří dvoustupňové ověřování. Můžete zadat počet bezpečnostních otázek, které je potřeba správně zodpovědět. |
| Povolit uživatelům přidružení tokenu OATH jiného výrobce | Povolí uživatelům zadat token OATH jiného výrobce. |
| V nouzové situaci použít token OATH | Umožní použití tokenu OAUTH, pokud se nezdaří dvoustupňové ověřování. Můžete taky zadat časový limit relace v minutách. |
| Povolit protokolování | Povolí protokolování na portálu User Portal. Protokoly se ukládají do: C:\Program Files\Multi-Factor Authentication Server\Logs. |

> [!IMPORTANT]
> Od března 2019 nebudou možnosti telefonního hovoru dostupné uživatelům serveru MFA v tenantech Azure AD zdarma nebo zkušební verze. Sms zprávy nejsou touto změnou ovlivněny. Telefonní hovor bude i nadále k dispozici uživatelům v placených klientech Azure AD. Tato změna má vliv pouze na bezplatné nebo zkušební klienty Azure AD.

Na portálu se tato nastavení uživatelům zviditelní, jakmile jsou povolena a uživatelé jsou přihlášeni na portálu User Portal.

![Správa účtu serveru MFA pomocí uživatelského portálu](./media/howto-mfaserver-deploy-userportal/portalsettings.png)

### <a name="self-service-user-enrollment"></a>Samoobslužná registrace uživatele

Pokud chcete, aby se uživatelé přihlašují a registrují, musíte na kartě Nastavení vybrat **možnosti Povolit uživatelům přihlášení** a Povolit možnosti **registrace uživatele.**

Pokud se například uživatel přihlásí k portálu User Portal poprvé, bude přesměrován na stránku Azure Multi-Factor Authentication – Nastavení uživatele. Podle toho, jak jste nakonfigurovali ověřování Azure Multi-Factor Authentication si uživatel možná bude moci vybrat metodu ověření.

Pokud si vybere metodu ověření hlasovým hovorem nebo je tato možnost předem nastavená, na stránce se zobrazí výzva k zadání primárního telefonního čísla a případně linky. Možná budou moct zadat záložní telefonní číslo.

![Registrace primárního a záložního telefonního čísla](./media/howto-mfaserver-deploy-userportal/backupphone.png)

Pokud musí uživatel při ověřování zadat kód PIN, zobrazí se na stránce výzva k vytvoření kódu PIN. Po zadání telefonního čísla nebo telefonních čísel a PIN kódu (pokud je potřeba) uživatel klikne na tlačítko **Zavolat mi nyní a ověřit mě**. Azure Multi-Factor Authentication provádí ověření telefonního hovoru na primární telefonní číslo uživatele. Uživatel musí hovor přijmout, zadat PIN (pokud je tato funkce aktivní) a stisknout #, tím přejde k dalšímu kroku vlastního zápisu.

Pokud si uživatel vybere metodu ověření zprávou SMS nebo je tato možnost předem nastavená, na stránce se zobrazí výzva k zadání čísla mobilního telefonu. Pokud musí uživatel při ověřování zadat kód PIN, zobrazí se na stránce také výzva k zadání kódu PIN.  Po zadání telefonního čísla a PIN kódu (pokud je potřeba) uživatel klikne na tlačítko **Poslat mi nyní zprávu SMS a ověřit mě**. Azure Multi-Factor Authentication provádí ověření SMS na mobilním telefonu uživatele. Uživatel obdrží zprávu SMS s jednorázovým heslem a odpoví na zprávu s použitím jednorázového hesla spolu s PIN kódem (pokud je potřeba).

![Ověření uživatelského portálu pomocí SMS](./media/howto-mfaserver-deploy-userportal/text.png)

Pokud si uživatel vybere metodu ověření pomocí mobilní aplikace, na stránce se zobrazí výzva k instalaci aplikace Microsoft Authenticator na mobilní zařízení a vygenerování aktivačního kódu. Po nainstalování aplikace uživatel klikne na tlačítko Generovat aktivační kód.

> [!NOTE]
> Pro používání aplikace Microsoft Authenticator je potřeba, aby měl uživatel ve svém zařízení zapnutý příjem nabízených oznámení.

Na stránce se pak zobrazí aktivační kód, adresa URL a čárový kód. Pokud musí uživatel při ověřování zadat kód PIN, zobrazí se na stránce také výzva k zadání kódu PIN. Uživatel zadá aktivační kód a adresu URL do aplikace Microsoft Authenticator nebo pomocí čtečky čárových kódů naskenuje čárový kód a pak klikne na tlačítko Aktivovat.

Po dokončení aktivace uživatel klikne na tlačítko **Ověřit mě nyní**. Azure Multi-Factor Authentication provádí ověření mobilní aplikace uživatele. Uživatel musí zadat PIN (pokud je tato funkce aktivní) a stisknout tlačítko Ověřit, tím přejde k dalšímu kroku vlastního zápisu.

Pokud správci nastavili Azure Multi-Factor Authentication Server tak, aby shromažďoval bezpečnostní otázky a odpovědi, přesměruje uživatele na stránku s bezpečnostními otázkami. Uživatel musí vybrat čtyři bezpečnostní otázky a zadat odpovědi na tyto otázky.

![Bezpečnostní otázky uživatelského portálu](./media/howto-mfaserver-deploy-userportal/secq.png)

Samoobslužný zápis je teď dokončený a uživatel je přihlášený k portálu User Portal. V budoucnu se uživatel může k portálu User Portal kdykoli znovu přihlásit a pokud má od správce oprávnění ke změně metod, bude moci změnit svá telefonní čísla, kódy PIN, metody ověřování a bezpečnostní otázky.

## <a name="next-steps"></a>Další kroky

- [Nasazení webové služby mobilní aplikace Azure Multi-Factor Authentication Serveru](howto-mfaserver-deploy-mobileapp.md)
