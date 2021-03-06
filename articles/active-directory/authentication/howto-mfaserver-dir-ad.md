---
title: Server Azure MFA a služba Active Directory – služba Azure Active Directory
description: Postup integrace Azure Multi-Factor Authentication Serveru se službou Active Directory pro synchronizaci adresářů.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: fceaa203944074b0c3fcf5cb6254f1e87ac16cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480976"
---
# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Integrace adresáře mezi Azure MFA Serverem a službou Active Directory

Pro integraci se službou Active Directory nebo jiným adresářem LDAP použijte část Azure MFA Serveru Integrace adresáře. Můžete nastavit atributy podle schématu adresáře a zapnout automatickou synchronizaci uživatelů.

> [!IMPORTANT]
> července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali server MFA před 1.

## <a name="settings"></a>Nastavení

Ve výchozím nastavení je Azure Multi-Factor Authentication (MFA) Server nakonfigurován pro import nebo synchronizaci uživatelů ze služby Active Directory.  Karta Integrace adresáře vám umožní potlačit výchozí chování a vytvořit vazbu na jiný adresář LDAP, adresář ADAM, nebo konkrétní ovladač domény služby Active Directory.  Taky umožňuje použití Ověřování pomocí protokolu LDAP na proxy serveru LDAP nebo pro Vázání protokolu LDAP jako cíl pro RADIUS, předběžné ověření pro Ověřování IIS nebo pro primární ověření pro portál User Portal.  V následující tabulce jsou popsaná jednotlivá nastavení.

![Úprava konfigurace LDAP na serveru MFA](./media/howto-mfaserver-dir-ad/dirint.png)

> [!NOTE]
> Není zaručeno, že integrace adresářů bude pracovat s jinými adresáři než se službou Active Directory Domain Services.

| Funkce | Popis |
| --- | --- |
| Použít Active Directory |Vyberte možnost Použít Active Directory, pokud chcete používat Active Directory pro importování a synchronizaci.  Toto je výchozí nastavení. <br>Poznámka: Aby integrace služby Active Directory fungovala správně, připojte počítač k doméně a přihlaste se pomocí doménového účtu. |
| Zahrnout důvěryhodné domény |Zaškrtněte políčko **Zahrnout důvěryhodné domény**, pokud chcete, aby se agent pokusil připojit k doménám, kterým důvěřuje aktuální doména, k jiné doméně v doménové struktuře nebo k doménám ve vztahu důvěryhodnosti doménové struktury.  Pokud neimportujete ani nesynchronizujete uživatele z jakékoli důvěryhodné domény, zrušte označení tohoto zatržítka – zvýší se tím výkon.  Ve výchozím nastavení je zatržítko označené. |
| Použít specifickou konfiguraci LDAP |Vyberte možnost Použít LDAP, pokud chcete pro importování a synchronizaci použít specifické nastavení LDAP. Poznámka: Pokud je vybraná možnost Použít LDAP, v uživatelském prostředí se odkazy na Active Directory změní na odkazy na LDAP. |
| Tlačítko Upravit |Tlačítko Upravit umožňuje udělat změny nastavení konfigurace. |
| Použít dotazy na obor atributů |Určuje, jestli se mají použít dotazy na obor atributů.  Dotazy na obor atributů umožňují efektivní prohledávání adresáře a kvalifikaci záznamů podle položek v atributech jiného záznamu.  Azure Multi-Factor Authentication Server používá dotazy na obor atributů pro efektivní dotazování uživatelů, kteří jsou členy skupiny zabezpečení.   <br>Poznámka:  Existují případy, kdy se sice dotazy na obor atributů podporují, ale použít by se neměly.  Active Directory třeba může mít s dotazy na obor atributů problémy, pokud skupina zabezpečení obsahuje členy z více než jedné domény. V takovém případě zaškrtnutí zrušte. |

V následující tabulce jsou popsaná nastavení konfigurace LDAP.

| Funkce | Popis |
| --- | --- |
| Server |Zadejte název hostitele nebo IP adresu serveru, na kterém běží adresář LDAP.  Můžete taky zadat záložní server oddělený středníkem. <br>Poznámka: Pokud je typ vazby SSL (TLS), je vyžadován plně kvalifikovaný název hostitele. |
| Základní rozlišující název |Zadejte rozlišující název objektu základního adresáře, ze kterého začínají všechny dotazy na adresář.  Příklad: dc=abc,dc=com. |
| Typ vazby - Dotazy |Vyberte požadovaný typ vazby pro použití při vytváření vazby pro vyhledávání v adresáři LDAP.  Používá se pro importy, synchronizaci a překlad uživatelského jména. <br><br>  Anonymní – Vytvoří se anonymní vazba.  Hodnoty Rozlišující název vázání a Heslo vázání se nepoužijí.  Toto funguje, pouze pokud adresář LDAP umožňuje anonymní vytváření vazby a oprávnění umožňují dotazování příslušných záznamů a atributů.  <br><br> Jednoduchá – Hodnoty Rozlišující název vázání a Heslo vázání se předají jako prostý text pro vazby k adresáři LDAP.  Tento typ slouží pro testovací účely k ověření, jestli se dá kontaktovat server a jestli má účet vazby odpovídající přístup. Po nainstalování příslušného certifikátu místo toho použijte protokol SSL.  <br><br> SSL – Hodnoty Rozlišující název vázání a Heslo vázání se zašifrují pomocí protokolu SSL pro vazbu k adresáři LDAP.  Nainstalujte v místním prostředí certifikát, kterému adresář LDAP důvěřuje.  <br><br> Windows – Uživatelské jméno vázání a Heslo vázání se použijí pro zabezpečené připojení k ovladači domény služby Active Directory nebo adresáři ADAM.  Pokud je pole Uživatelské jméno vázání prázdné, pro vazbu se použije účet přihlášeného uživatele. |
| Typ vazby - Ověřování |Vyberte požadovaný typ vazby pro použití při ověřování vazby LDAP.  Podívejte se na popis typů vazby v části Typ vazby – Dotazy.  Umožňuje například použít Anonymní vázání pro dotazy a SSL vázání pro zabezpečení ověření vazby LDAP. |
| Rozlišující název vázání nebo Uživatelské jméno vázání |Zadejte rozlišující název uživatelského záznamu pro účet, který se má použít při vytváření vazby k adresáři LDAP.<br><br>Rozlišující název se použije jen, když je Typ vazby nastavený na Jednoduchá nebo SSL.  <br><br>Zadejte uživatelské jméno účtu systému Windows, který se má použít při vytváření vazby k adresáři LDAP, když je Typ vazby nastavený na Windows.  Pokud je pole prázdné, pro vazbu se použije účet přihlášeného uživatele. |
| Heslo vázání |Zadejte heslo vázání pro Rozlišující název vázání nebo uživatelské jméno, které se použije pro vazbu k adresáři LDAP.  Aby se mohlo nastavit heslo pro službu Multi-Factor Auth Server AdSync Service, povolte synchronizaci a zajistěte, aby služba běžela na místním počítači.  Heslo se uloží do uložených uživatelských jmen a hesel Windows pod účtem, na kterém běží služba Multi-Factor Auth Server AdSync Service.  Heslo se také uloží pod účtem, na kterém běží uživatelské prostředí Multi-Factor Auth Serveru a pod účtem, na kterém běží služba Multi-Factor Auth Server Service.  <br><br>Protože se heslo uloží pouze do uložených uživatelských jmen a hesel Windows na místním serveru, opakujte tento krok na každém Multi-Factor Auth Serveru, který potřebuje přístup k tomuto heslu. |
| Omezení velikosti dotazu |Zadejte omezení velikosti pro maximální počet uživatelů, které vrátí vyhledávání v adresáři.  Toto omezení by mělo být stejné jako omezení nastavené v adresáři LDAP.  Pro velká hledání v případech kdy se nepodporuje stránkování, se import a synchronizace pokusí uživatele načíst v dávkách.  Pokud je tady nastavené omezení větší než omezení nastavené v adresáři LDAP, může se stát, že se někteří uživatelé nenačtou. |
| Tlačítko Test |Kliknutím na **Test** můžete otestovat vazbu na server LDAP.  <br><br>Pro testovací vazbu není nutné vybírat možnost **Použít LDAP**. Díky tomu můžete vazbu otestovat před použitím konfigurace LDAP. |

## <a name="filters"></a>Filtry

Filtry vám umožní nastavit kritéria pro kvalifikování záznamů při prohledávání adresáře.  Nastavením filtru můžete upřesnit rozsah objektů, které chcete synchronizovat.  

![Konfigurace filtrování adresářů na serveru MFA](./media/howto-mfaserver-dir-ad/dirint2.png)

Azure Multi-Factor Authentication má tyto tři možnosti filtru:

* **Filtr kontejneru** - Zadejte kritéria filtru, která se použijí pro kvalifikaci záznamů kontejneru při prohledávání adresáře.  Pro Active Directory a ADAM se obvykle používá (|(objectClass=organizationalUnit)(objectClass=container)).  Pro jiné adresáře LDAP použijte kritéria filtru, která kvalifikují každý typ objektu kontejneru v závislosti na schématu adresáře.  <br>Poznámka: Pokud je toto pole prázdné, použije se výchozí hodnota ((objectClass=organizationalUnit)(objectClass=container)).
* **Filtr skupiny zabezpečení** - Zadejte kritéria filtru, která se použijí pro kvalifikaci záznamů skupiny zabezpečení při prohledávání adresáře.  Pro Active Directory a ADAM se obvykle používá (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).  Pro jiné adresáře LDAP použijte kritéria, která kvalifikují každý typ objektu skupiny zabezpečení v závislosti na schématu adresáře.  <br>Poznámka: Pokud je toto pole prázdné, použije se výchozí hodnota (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).
* **Filtr uživatele** - Zadejte kritéria filtru, která se použijí pro kvalifikaci záznamů uživatele při prohledávání adresáře.  Pro Active Directory a ADAM se obvykle používá (&(objectClass=user)(objectCategory=person)).  Pro jiné adresáře LDAP použijte (objectClass=inetOrgPerson) nebo něco podobného v závislosti na schématu adresáře. <br>Poznámka: Pokud je toto pole prázdné, použije se výchozí hodnota (&(objectCategory=person)(objectClass=user)).

## <a name="attributes"></a>Atributy

Atributy můžete podle potřeby upravit pro konkrétní adresář.  To vám umožní přidat vlastní atributy a nastavit synchronizaci pouze na atributy, které potřebujete. Pro hodnotu každého pole atributu použijte název atributu definovaný ve schématu adresáře. Následující tabulka poskytuje další informace k jednotlivým funkcím.

Atributy můžete zadat ručně a nemusí se shodovat s atributem v seznamu atributů.

![Přizpůsobení atributů integrace adresářů na serveru MFA](./media/howto-mfaserver-dir-ad/dirint3.png)

| Funkce | Popis |
| --- | --- |
| Jedinečný identifikátor |Zadejte název atributu, který slouží jako jedinečný identifikátor kontejneru, skupiny zabezpečení a záznamů uživatele.  V Active Directory je to obvykle objectGUID. V jiných implementacích LDAP se může používat entryUUID nebo něco podobného.  Výchozí hodnota je objectGUID. |
| Typ jedinečného identifikátoru |Vyberte typ atributu jedinečného identifikátoru.  V Active Directory má atribut objectGUID typ GUID. V jiných implementacích LDAP se může používat typ Pole bajtů ASCII nebo Řetězec.  Výchozí hodnota je GUID. <br><br>Je důležité tento typ nastavit správně, protože na synchronizační položky se odkazuje pomocí jejich jedinečného identifikátoru. Typ jedinečného identifikátoru se používá pro přímé vyhledání objektu v adresáři.  Pokud se typ nastaví na Řetězec, když adresář ve skutečnosti ukládá hodnotu jako pole znaků ASCII, nebude synchronizace fungovat správně. |
| Rozlišující název |Zadejte název atributu, který obsahuje rozlišující název pro každý záznam.  V Active Directory je to obvykle distinguishedName. V jiných implementacích LDAP se může používat entryDN nebo něco podobného.  Výchozí hodnota je distinguishedName. <br><br>Pokud atribut obsahující pouze rozlišující název neexistuje, může být použit atribut cesty reklam.  Část cesty „LDAP://\<server\>/“ se automaticky odstraní a zůstane jen rozlišující název objektu. |
| Název kontejneru |Zadejte název atributu, který v záznamu kontejneru obsahuje název.  Hodnota tohoto atributu se zobrazí v Hierarchii kontejneru při importu z Active Directory nebo při přidávání synchronizačních položek.  Výchozí hodnota je name. <br><br>Pokud různé kontejnery používají pro své názvy různé atributy, použijte k oddělení několika atributů názvu kontejneru středník.  Pro zobrazení názvu objektu kontejneru se použije první atribut názvu kontejneru, který se v něm najde. |
| Název skupiny zabezpečení |Zadejte název atributu, který v záznamu skupiny zabezpečení obsahuje název.  Hodnota tohoto atributu se zobrazí v seznamu Skupiny zabezpečení při importu z Active Directory nebo při přidávání synchronizačních položek.  Výchozí hodnota je name. |
| Uživatelské jméno |Zadejte název atributu, který v záznamu uživatele obsahuje uživatelské jméno.  Hodnota tohoto atributu se použije jako uživatelské jméno pro Multi-Factor Auth Server.  Můžete zadat i druhý, záložní atribut.  Druhý atribut se použije pouze v případě, že první atribut neobsahuje hodnotu pro uživatele.  Výchozí hodnoty jsou userPrincipalName a sAMAccountName. |
| Jméno |Zadejte název atributu, který v záznamu uživatele obsahuje jméno.  Výchozí hodnota je givenName. |
| Příjmení |Zadejte název atributu, který v záznamu uživatele obsahuje příjmení.  Výchozí hodnota je sn. |
| E-mailová adresa |Zadejte název atributu, který v záznamu uživatele obsahuje e-mailovou adresu.  Na e-mailovou adresu se uživateli pošle uvítací e-mail a budou se posílat e-maily s aktualitami.  Výchozí hodnota je mail. |
| Uživatelská skupina |Zadejte název atributu, který v záznamu uživatele obsahuje uživatelskou skupinu.  Uživatelská skupina se může použít pro filtrování uživatelů v agentovi a v sestavách v Portálu pro správu Multi-Factor Auth Serveru. |
| Popis |Zadejte název atributu, který v záznamu uživatele obsahuje popis.  Popis se používá jen pro vyhledávání.  Výchozí hodnota je description. |
| Jazyk telefonního hovoru |Zadejte název atributu, který obsahuje krátký název jazyka pro hlasové hovory s uživatelem. |
| Jazyk textové zprávy |Zadejte název atributu, který obsahuje krátký název jazyka pro SMS zprávy posílané uživateli. |
| Jazyk mobilní aplikace |Zadejte název atributu, který obsahuje krátký název jazyka pro textové zprávy v mobilní aplikaci uživatele. |
| Jazyk tokenu OATH |Zadejte název atributu, který obsahuje krátký název jazyka pro textové zprávy tokenu OATH pro uživatele. |
| Telefon do zaměstnání |Zadejte název atributu, který v záznamu uživatele obsahuje telefonní číslo do zaměstnání.  Výchozí hodnota je telephoneNumber. |
| Telefon domů |Zadejte název atributu, který v záznamu uživatele obsahuje telefonní číslo domů.  Výchozí hodnota je homePhone. |
| Pager |Zadejte název atributu, který v záznamu uživatele obsahuje číslo pageru.  Výchozí hodnota je pager. |
| Mobilní telefon |Zadejte název atributu, který v záznamu uživatele obsahuje číslo na mobilní telefon.  Výchozí hodnota je mobile. |
| Fax |Zadejte název atributu, který v záznamu uživatele obsahuje číslo faxu.  Výchozí hodnota je facsimileTelephoneNumber. |
| IP telefon |Zadejte název atributu, který v záznamu uživatele obsahuje číslo IP telefonu.  Výchozí hodnota je ipPhone. |
| Vlastní |Zadejte název atributu, který v záznamu uživatele obsahuje vlastní číslo telefonu.  Výchozí hodnota je prázdná. |
| Linka |Zadejte název atributu, který v záznamu uživatele obsahuje linku telefonního čísla.  Hodnota pole Linka se použije jako linka jen pro primární telefonní číslo.  Výchozí hodnota je prázdná. <br><br>Pokud atribut Linka není zadaný, můžete linky zadat v rámci atributu telefon. V takovém případě před linku zadejte znak „x“, aby se mohla správně parsovat.  Například hodnota 555-123-4567 x890 by znamenala telefonní číslo 555-123-4567 a linku 890. |
| Tlačítko Obnovit výchozí |Pokud kliknete na **Obnovit výchozí**, všechny atributy se obnoví na výchozí hodnoty.  Výchozí hodnoty by měly fungovat s normální službou Active Directory nebo schématem ADAM. |

Pokud chcete upravit atributy, klikněte na kartě Atributy na **Upravit.**  Tím se vyvolá okno, ve kterém můžete atributy upravovat. Výběrem **...** vedle libovolného atributu otevřete okno, kde můžete zvolit, jaké atributy se mají zobrazit.

![Upravit mapování atributů adresáře na serveru MFA](./media/howto-mfaserver-dir-ad/dirint4.png)

## <a name="synchronization"></a>Synchronizace

Synchronizace udržuje databázi uživatelů Azure MFA synchronizovanou s uživateli v Active Directory nebo jiném adresáři protokolu LDAP (Lightweight Directory Access Protocol). Tento proces se podobá ručnímu importování uživatelů z Active Directory, ale se pravidelně dotazuje, jestli nedošlo ke změnám uživatelů a skupin zabezpečení v Active Directory, které by měl zpracovat.  Zároveň zakazuje a odstraňuje uživatele, kteří byli odebráni z kontejneru, skupiny zabezpečení nebo služby Active Directory.

Služba Multi-Factor Auth ADSync je služba systému Windows, která pravidelně dotazuje službu Active Directory.  Nesmíte si ji plést se službou Azure AD Sync nebo Azure AD Connect  Přestože je služba Multi-Factor Auth ADSync postavená na podobném základu kódu, je specifická pro Azure Multi-Factor Authentication Server.  instaluje se v zastaveném stavu a pokud je nakonfigurovaná k tomu, aby se spustila, spustí ji Multi-Factor Auth Server.  Pokud máte více serverů s Multi-Factor Auth Serverem, může Multi-Factor Auth ADSync běžet jen na jednom serveru.

Služba Multi-Factor Auth ADSync používá rozšíření serveru DirSync LDAP od Microsoftu pro efektivní dotazování na změny.  Tento řídicí volající DirSync musí mít oprávnění „directory get changes“ a oprávnění rozšířeného řídicího přístupu DS-Replication-Get-Changes.  Ve výchozím nastavení se tato oprávnění udělují účtům Správce a LocalSystem na řadičích domény.  Služba Multi-Factor Auth AdSync ve výchozím nastavení běží pod účtem LocalSystem.  Je proto nejjednodušší službu spustit na řadiči domény.  Pokud tuto službu nakonfigurujete tak, aby vždy prováděla úplnou synchronizaci, může běžet pod účtem s oprávněními nižší úrovně.  Není to tak efektivní, ale nejsou k tomu potřeba úplná práva.

Pokud adresář LDAP podporuje ovládací prvek DirSync a je pro něj nakonfigurován, pak bude dotazování na uživatele a skupinu zabezpečení fungovat stejně, jako to funguje se službou Active Directory.  Pokud adresář LDAP ovládací prvek DirSync nepodporuje, pak se při každém cyklu provede úplná synchronizace.

![Synchronizace objektů adresáře se serverem MFA](./media/howto-mfaserver-dir-ad/dirint5.png)

Následující tabulka obsahuje další informace k jednotlivým nastavením na kartě Synchronizace.

| Funkce | Popis |
| --- | --- |
| Povolit synchronizaci se službou Active Directory |Pokud je toto políčko zaškrtnuté, služba Multi-Factor Auth Server se bude pravidelně dotazovat služby Active Directory na změny. <br><br>Poznámka: Než služba Multi-Factor Auth Server začne zpracovávat změny, musí se přidat aspoň jedna Synchronizační položka a aspoň jednou provést akce Synchronizovat. |
| Synchronizovat každých |Zadejte dobu, po kterou bude služba Multi-Factor Auth Server čekat mezi jednotlivými dotazy zpracováními změn. <br><br> Poznámka: Zadaná doba je interval mezi začátky jednotlivých cyklů.  Pokud doba zpracování změn překročí tento interval, služba ihned provede další dotazování. |
| Odebrat uživatele, kteří již nejsou ve službě Active Directory |Pokud je tato možnost označená, služba Multi-Factor Auth Server zpracuje náhrobky uživatelů odstraněných z Active Directory a odstraní odpovídající uživatele ze služby Multi-Factor Auth Server. |
| Vždy provádět úplnou synchronizaci |Pokud je tato možnost označená, služba Multi-Factor Auth Server vždy provede úplnou synchronizaci.  Pokud označená není, služba Multi-Factor Auth Server provede přírůstkovou synchronizaci tak, že se bude dotazovat jen na uživatele, kteří se změnili.  Ve výchozím nastavení zatržítko není označené. <br><br>Pokud toto políčko není zaškrtnuté, Azure MFA Server provádí přírůstkovou synchronizaci jen tehdy, když adresář podporuje ovládací prvek DirSync a když má účet vázaný na adresář oprávnění pro provedení přírůstkových dotazů DirSync.  Pokud účet nemá odpovídající oprávnění nebo pokud se synchronizace týká více domén, Azure MFA Server provádí úplnou synchronizaci. |
| Vyžadovat schválení správce, pokud bude zablokováno nebo odstraněno více než X uživatelů |Synchronizační položky se můžou nastavit tak, aby zablokovaly nebo odstranili uživatele, kteří už nejdou členy skupiny zabezpečení nebo kontejneru položky.  Jako pojistku můžete nastavit, že když počet uživatelů, kteří se mají zablokovat nebo odstranit, přesáhne určitou hranici, bude to muset schválit správce.  Pokud je toto políčko zaškrtnuté, bude při překročení určité hranice potřeba schválení.  Výchozí hodnota je 5 a zadat se můžou hodnoty v rozsahu 1 až 999. <br><br> Schválení se získá tak, že se nejdřív správcům pošle e-mail s oznámením. V něm jsou pokyny, že mají správci zkontrolovat a schválit zablokování nebo odstranění uživatelů.  Při spuštění uživatelského prostředí Multi-Factor Auth Serveru se objeví výzva ke schválení. |

Tlačítko **Synchronizovat** vám umožňuje provést úplnou synchronizaci pro označené synchronizační položky.  Při každém přidání, úpravě, odstranění nebo změně pořadí synchronizačních položek je potřeba provést úplnou synchronizaci.  Ta se musí provést i v rámci uvedení služby Multi-Factor Auth AdSync do provozu, protože se tím vytvoří výchozí bod, od kterého bude služba dotazovat přírůstkové změny.  Pokud došlo ke změnám synchronizačních položek, ale neprovedla se úplná synchronizace, zobrazí se výzva k provedení úplné synchronizace.

Tlačítko **Odstranit** umožní správci odstranit jednu nebo víc synchronizačních položek ze seznamu synchronizačních položek Multi-Factor Auth Serveru.

> [!WARNING]
> Jakmile se záznam synchronizační položky odstraní, už se nedá obnovit. Pokud omylem odstraníte záznam synchronizační položky, budete ho muset znovu přidat.

Synchronizační položka nebo položky se odstranily z Multi-Factor Auth Serveru.  Multi-Factor Auth Server nebude tyto synchronizační položky dál zpracovávat.

Tlačítka Přesunout nahoru a Přesunout dolů umožňují správci změnit pořadí synchronizačních položek.  Pořadí je důležité, protože jeden uživatel může být členem víc než jedné synchronizační položky (např. kontejneru a skupiny zabezpečení).  Při synchronizaci se na uživatele použije nastavení z první synchronizační položky v seznamu, ke které je uživatel přidružený.  Synchronizační položky by se proto měly seřadit podle priority.

> [!TIP]
> Po odebrání synchronizačních položek by se měla provést úplná synchronizace.  Po změně pořadí synchronizačních položek by se měla provést úplná synchronizace.  Úplnou synchronizaci můžete provést kliknutím na **Synchronizovat nyní**.

## <a name="multi-factor-authentication-servers"></a>Servery s vícefaktorovým ověřováním

Další servery s vícefaktorovým ověřováním mohou být nastaveny tak, aby sloužily jako záložní proxy server RADIUS, proxy server LDAP nebo ověřování služby IIS. Konfigurace synchronizace se sdílí mezi všemi agenty. Pouze jeden z těchto agentů však může mít spuštěnou službu serveru vícefaktorového ověřování. Tato karta umožňuje vybrat vícefaktorový ověřovací server, který by měl být povolen pro synchronizaci.

![Související vícefaktorové ověřovací servery](./media/howto-mfaserver-dir-ad/dirint6.png)
