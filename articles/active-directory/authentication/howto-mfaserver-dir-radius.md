---
title: RADIUS a server Azure MFA – služba Azure Active Directory
description: Nasazení ověření služby RADIUS a serveru Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 552226c35d4d129f73b96b689871708950b7ffb1
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652953"
---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>Integrace ověření služby RADIUS se serverem Azure Multi-Factor Authentication

RADIUS je standardní protokol pro přijímání požadavků na ověření a zpracování těchto požadavků. Azure Multi-Factor Authentication Server může fungovat jako server RADIUS. Jeho vložením mezi klienta RADIUS (zařízení VPN) a cíl ověřování můžete přidat dvoustupňové ověřování. Cíl ověřování může být služba Active Directory, adresář LDAP nebo jiný server RADIUS. Aby služba Azure Multi-Factor Authentication (MFA) fungovala, je nutné Azure MFA Server nakonfigurovat tak, aby komunikoval s klientskými servery i s cílem ověřování. Azure MFA Server přijímá požadavky od klienta protokolu RADIUS, ověřuje přihlašovací údaje proti cíli ověřování, přidává ověřování Azure Multi-Factor Authentication a odesílá odpověď zpět do klienta protokolu RADIUS. Požadavek na ověření bude úspěšný pouze v případě, že uspěje primární ověřování i ověřování Multi-Factor Authentication.

> [!IMPORTANT]
> Tento článek je určen pouze pro uživatele Serveru Azure MFA. Pokud používáte cloudové Azure MFA, místo toho se podívejte, jak [integrovat s radius ověřování pro Azure MFA](howto-mfa-nps-extension.md).
>
> července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali server MFA před 1.

> [!NOTE]
> Server MFA podporuje pouze PAP (protokol ověřování hesla) a protokoly MSCHAPv2 (protokol ověřování Challenge Handshake společnosti Microsoft) RADIUS při fungování jako server RADIUS.  Když server MFA funguje jako proxy server protokolu RADIUS na jiný server protokolu RADIUS, který podporuje tento protokol, je možné použít jiné protokoly, například protokol EAP (Extensible Authentication Protocol).
>
> V této konfiguraci nejsou funkční jednosměrné tokeny SMS a OATH, protože MFA Server nemůže vytvořit úspěšné odpovědi RADIUS na výzvy k ověření pomocí alternativních protokolů.

![Ověřování poloměru na serveru MFA](./media/howto-mfaserver-dir-radius/radius.png)

## <a name="add-a-radius-client"></a>Přidání klienta protokolu RADIUS

Pro konfiguraci ověřování pomocí protokolu RADIUS nainstalujte server Azure Multi-Factor Authentication na server Windows. Pokud máte prostředí služby Active Directory, server by měl být připojen k doméně uvnitř sítě. Pomocí následujícího postupu nakonfigurujte server Azure Multi-Factor Authentication:

1. V rámci Azure Multi-Factor Authentication Serveru klikněte na ikonu Ověřování pomocí protokolu RADIUS v levé nabídce.
2. Zaškrtněte políčko **Povolit ověřování pomocí protokolu RADIUS**.
3. Na kartě klienti změňte porty Ověřování a Monitorování, pokud služba Azure MFA RADIUS potřebuje naslouchat požadavkům protokolu RADIUS na nestandardních portech.
4. Klikněte na **Přidat**.
5. Zadejte IP adresu zařízení/serveru, který se bude ověřovat v Azure Multi-Factor Authentication Serveru, název aplikace (volitelné) a sdílený tajný klíč.

   Název aplikace se zobrazí v sestavách a může se zobrazit v rámci ověřovacích zpráv SMS nebo ověřovacích zpráv mobilních aplikací.

   Sdílený tajný klíč musí být stejný jak na Azure Multi-Factor Authentication Serveru, tak i v zařízení/serveru.

6. Zaškrtněte políčko **Vyžadovat porovnání uživatele u služby Multi-Factor Authentication**, pokud se všichni uživatelé importovali na server a budou podléhat vícefaktorovému ověřování. Pokud ještě na server nebyl importován velký počet uživatelů nebo budou uživatelé vyloučení z dvoustupňového ověřování, nechte toto políčko nezaškrtnuté.
7. Zaškrtněte políčko **Povolit záložní token OAUTH**, pokud chcete použít hesla OAUTH z aplikací pro mobilní ověřování jako záložní metodu.
8. Klikněte na tlačítko **OK**.

Opakováním kroků 4 až 8 přidejte požadovaný počet dalších klientů protokolu RADIUS.

## <a name="configure-your-radius-client"></a>Konfigurace klienta protokolu RADIUS

1. Klikněte na kartu **Cíl**.
   * Pokud je server Azure MFA nainstalovaný na serveru přilehlém k doméně v prostředí služby Active Directory, vyberte **doménu Windows**.
   * Pokud musí být uživatelé ověřováni proti adresáři LDAP, vyberte **Vázání protokolu LDAP**.
      Vyberte ikonu Integrace adresáře a na kartě Nastavení upravte konfiguraci protokolu LDAP tak, aby server mohl vytvořit vazbu k adresáři. Pokyny ke konfiguraci protokolu LDAP najdete v [průvodci konfigurací serveru proxy protokolu LDAP](howto-mfaserver-dir-ldap.md).
   * Pokud by měli být uživatelé ověřeni na jiném serveru RADIUS, vyberte **možnost RADIUS server (servery)**.
1. Klikněte na **Přidat** a nakonfigurujte server, na který bude Azure MFA Server přes proxy předávat požadavky protokolu RADIUS.
1. V dialogovém okně Přidat server protokolu RADIUS zadejte IP adresu serveru protokolu RADIUS a sdílený tajný klíč.

   Sdílený tajný klíč musí být stejný jak na Azure Multi-Factor Authentication Serveru, tak i na serveru protokolu RADIUS. Změňte port ověřování a port monitorování účtů, pokud server RADIUS využívá jiné porty.

1. Klikněte na tlačítko **OK**.
1. Přidejte Azure MFA Server jako klienta protokolu RADIUS v druhém serveru protokolu RADIUS, aby mohl zpracovávat požadavky na přístup odeslané z Azure MFA Serveru. Použijte stejný sdílený tajný klíč konfigurovaný na Azure Multi-Factor Authentication Serveru.

Opakováním těchto kroků přidejte další servery RADIUS. Pomocí tlačítek **Přesunout nahoru** a **Přesunout dolů** nakonfigurujte pořadí, ve kterém je Azure MFA Server má volat.

Úspěšně jste nakonfigurovali Azure Multi-Factor Authentication Server. Server teď naslouchá na nakonfigurovaných portech požadavkům přístupu protokolu RADIUS z konfigurovaných klientů.

## <a name="radius-client-configuration"></a>Konfigurace klienta protokolu RADIUS

Chcete-li nakonfigurovat klienta RADIUS, postupujte podle pokynů:

* Nakonfigurujte zařízení/server tak, aby se ověřovalo prostřednictvím radiusu na IP adresu serveru Azure Multi-Factor Authentication Server, která funguje jako server RADIUS.
* Použijte stejný sdílený tajný klíč, který byl nakonfigurován dříve.
* Nakonfigurujte časový rozsah RADIUS na 30 až 60 sekund tak, aby byl čas na ověření pověření uživatele, provedení dvoustupňového ověření, přijetí odpovědi a odpověď na požadavek na přístup RADIUS.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak provést [integraci s ověřováním RADIUS](howto-mfa-nps-extension.md), pokud máte službu Azure Multi-Factor Authentication v cloudu. 
