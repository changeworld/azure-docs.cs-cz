---
title: Správa portálu Azure EA
description: V tomto článku jsou vysvětlené nejčastější úkoly, které provádí správce portálu Azure EA.
author: bandersmsft
ms.author: banders
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 12276270b0cb2c01a9ac549eb4d115fd7c5d8385
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586283"
---
# <a name="azure-ea-portal-administration"></a>Správa portálu Azure EA

V tomto článku jsou vysvětlené nejčastější úkoly, které provádí správce portálu Azure EA Portal (https://ea.azure.com). Portál Azure EA je online portál pro správu, kde můžou zákazníci spravovat náklady na služby Azure EA. Úvodní informace o portálu Azure EA najdete v článku [Začínáme s portálem Azure EA](ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Přidání nového podnikového správce

Podnikoví správci mají při správě registrace do Azure EA největší oprávnění. Úvodní správce Azure EA byl vytvořen při nastavení smlouvy EA. Nové správce můžete kdykoliv přidat nebo odebrat. Nové správce přidávají jenom stávající správci. Další informace o přidání dalších podnikových správců najdete v tématu [Vytvoření dalšího podnikového správce](ea-portal-get-started.md#create-another-enterprise-administrator). Další informace o rolích a úlohách fakturačního profilu najdete v tématu [Role a úlohy fakturačního profilu](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Aktualizace stavu uživatele z Čeká na vyřízení na Aktivní

Při prvním přidání nových vlastníků účtů do registrace Azure EA se u nových vlastníků zobrazí stav _Čeká na vyřízení_. Jakmile noví vlastníci účtu dostanou úvodní aktivační e-mail, můžou se přihlásit a aktivovat si účet. Jakmile si účet aktivují, stav účtu se aktualizuje z _Čeká na vyřízení_ na _Aktivní_. Vlastník účtu si musí přečíst zprávu upozornění a pak vybrat **Pokračovat**. Při vytvoření komerčního účtu se může novým uživatelům zobrazit výzva k zadání jména a příjmení. Pokud se zobrazí, musí zadat požadované informace, aby mohli pokračovat, a pak se účet aktivuje.

## <a name="add-a-department-admin"></a>Přidání správce oddělení

Jakmile správce Azure EA vytvoří oddělení, může přidat správce oddělení a přidružit je k oddělením. Správce oddělení může vytvářet nové účty. Nové účty jsou potřeba pro vytvářená předplatná Azure EA.

Další informace o přidání správce oddělení najdete v tématu věnovaném [vytvoření správce oddělení Azure EA](ea-portal-get-started.md#add-a-department-administrator).

## <a name="associate-an-account-to-a-department"></a>Přidružit účet k oddělení

Podnikoví správci můžou přidružovat existující účty k oddělením v rámci registrace.

### <a name="to-associate-an-account-to-a-department"></a>Přidružení účtu k oddělení

1. Přihlaste se k portálu Azure EA jako podnikový správce.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Vyberte **Oddělení**.
1. Najeďte myší na řádek s požadovaným účtem a vyberte ikonu tužky na pravé straně.
1. V rozevírací nabídce vyberte požadované oddělení.
1. Vyberte **Uložit**.

## <a name="department-spending-quotas"></a>Kvóty útraty oddělení

Zákazníci se smlouvou Enterprise můžou nastavit nebo změnit kvóty útraty pro každé oddělení v rámci registrace. Výše kvóty útraty se nastavuje pro aktuální období závazku. Pokud nedojde k aktualizaci hodnot, na konci aktuálního období závazku systém prodlouží stávající kvótu útraty na další období závazku.

Správce oddělení může zobrazit kvótu útraty, ale pouze podnikový správce může aktualizovat její výši. Podnikový správce a správce oddělení obdrží oznámení, když dojde k dosažení 50 %, 75 %, 90 % a 100 % kvóty.

### <a name="enterprise-administrator-to-set-the-quota"></a>Nastavení kvóty podnikovým správcem:

 1. Otevřete portál Azure EA.
 1. V levém navigačním panelu vyberte **Manage** (Spravovat).
 1. Vyberte kartu **Oddělení**.
 1. Vyberte požadované oddělení.
 1. Vyberte symbol tužky v části Podrobnosti o oddělení nebo vyberte symbol **+ Přidat oddělení** a přidejte nové oddělení i s kvótou útraty.
 1. V části Podrobnosti o oddělení zadejte do pole Kvóta útraty (USD) výši kvóty útraty v měně registrace (hodnota musí být vyšší než 0).
    - V tuto chvíli můžete upravit také Název oddělení a Nákladové středisko.
 1. Vyberte **Uložit**.

Kvóta útraty oddělení se teď zobrazí na kartě Oddělení v zobrazení Seznam oddělení. Na konci aktuálního závazku se na portálu Azure EA zachovají kvóty útraty pro další období závazku.

Výše kvóty oddělení je nezávislá na aktuálním peněžním závazku a výše kvóty a související upozornění se vztahují pouze na využití první stranou. Kvóta útraty oddělení slouží pouze k informačním účelům a nevynucuje limity útraty.

### <a name="department-administrator-to-view-the-quota"></a>Zobrazení kvóty správcem oddělení:

1. Otevřete portál Azure EA.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Výběrem karty **Oddělení** zobrazte zobrazení Seznam oddělení s kvótami útraty.

Pokud jste nepřímý zákazník, váš distribuční partner musí povolit funkce nákladů.

## <a name="enterprise-user-roles"></a>Role podnikových uživatelů

Na portálu Azure EA můžete spravovat náklady a použití Azure EA. Na portálu Azure EA jsou tři hlavní role:

- Správce smlouvy Enterprise
- Správce oddělení
- Vlastník účtu

Každá role má jinou úroveň přístupu a jiná oprávnění.

Další informace o rolích uživatelů najdete v tématu [Role podnikových uživatelů](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Přidání účtu Azure EA

Účet Azure EA je organizační jednotka portálu Azure EA Portal. Slouží ke správě předplatných a také ke generování sestav. Pokud chcete získat přístup ke službám Azure, potřebujete vytvořit účet nebo vám ho někdo musí vytvořit.

Další informace o účtech Azure najdete v tématu Přidání účtu.

## <a name="enterprise-devtest-offer"></a>Nabídka Enterprise pro vývoj/testování

Jako podnikový správce Azure můžete vlastníkům účtů ve vaší organizaci umožnit vytvářet předplatná na základě nabídky EA pro vývoj/testování. Můžete to provést zaškrtnutím políčka Vývoj/testování u příslušného vlastníka účtu na portálu Azure EA Portal.

Po zaškrtnutí políčka Vývoj/testování informujte daného vlastníka účtu, aby mohl nastavit potřebná předplatná EA pro vývoj/testování pro týmy předplatitelů nabídky Vývoj/testování.

Tato nabídka umožňuje aktivním předplatitelům sady Visual Studio spouštět v Azure vývojové a testovací úlohy za speciální sazby pro vývoj/testování. Poskytuje přístup k celé galerii imagí pro vývoj/testování, včetně imagí Windows 8.1 a Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Nastavení nabídky Enterprise pro vývoj/testování:

1. Přihlaste se jako podnikový správce.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Vyberte kartu **Account** (Účet).
1. Vyberte řádek účtu, kterému chcete povolit přístup k nabídce Vývoj/testování.
1. Vyberte symbol tužky napravo od řádku.
1. Zaškrtněte políčko Vývoj/testování.
1. Vyberte **Uložit**.

Když se uživatel přidá jako vlastník účtu přes portál Azure EA, všechna předplatná Azure na základě nabídky Vývoj/testování s průběžnými platbami nebo nabídek měsíčního kreditu pro předplatitele sady Visual Studio přidružená k danému vlastníkovi účtu se převedou na nabídku EA pro vývoj/testování. Předplatná na základě jiných typů nabídek, jako jsou nabídky průběžných plateb, přidružená k danému vlastníkovi účtu se převedou na nabídky Microsoft Azure Enterprise.

Nabídka Vývoj/testování se v současné době nevztahuje na zákazníky Azure Government.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Převod podnikového účtu do nové registrace

Při převodu účtu se jeho vlastník přenese z jedné registrace do druhé. Do cílové registrace se přesunou všechna související předplatná pod tímto vlastníkem. Převod účtu využijte v případě, že máte více aktivních registrací a chcete přesunout jenom vybrané vlastníky účtů.

Tato část je určená jenom k informativním účelům, protože tuto akci nemůže provést podnikový správce. Pro převod podnikového účtu na novou registraci se vyžaduje žádost o podporu.

Při převodu podnikového účtu do nové registrace mějte na paměti následující body:

- Převádějí se jenom účty, které jsou zadané v požadavku. Pokud zvolíte všechny účty, převedou se všechny.
- Zdrojová registrace zůstane v aktivním nebo prodlouženém stavu. Registraci můžete používat dál, dokud jí nevyprší platnost.

### <a name="prerequisites"></a>Požadavky

K žádosti o převod účtu je potřeba sdělit následující informace:

- Číslo cílové registrace, název účtu a e-mail vlastníka účtu, který se má převést
- U zdrojové registrace uveďte její číslo a převáděný účet.

Další body, na které je potřeba myslet před převodem účtu:

- Cílovou a zdrojovou registraci musí schválit správce EA.
- Pokud převod účtu vašim požadavkům nevyhovuje, můžete zvážit převod registrace.
- Převod účtů přenese všechny služby a předplatná související s konkrétními účty.
- Po dokončení převodu se převedený účet v rámci zdrojové registrace zobrazí jako neaktivní a v cílové registraci naopak jako aktivní.
- Účet zobrazuje koncové datum odpovídající efektivnímu datu převodu zdrojové registrace jako počáteční datum cílové registrace.
- Jakékoliv použití účtu před efektivním datem převodu zůstane v rámci zdrojové registrace.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Převod podnikové registrace do nové registrace

Převod registrace se bere v úvahu v těchto případech:

- Skončilo období závazku aktuální registrace.
- Registrace je ve stavu vypršení platnosti nebo prodloužení a dojednává se nová smlouva.
- Máte více registrací a chcete zkombinovat všechny účty a fakturaci v rámci jediné registrace.

Tato část je určená jenom k informativním účelům, protože tuto akci nemůže provést podnikový správce. Pro převod podnikové registrace na novou se vyžaduje žádost o podporu.

Pokud požádáte o převod celé podnikové registrace do jiné registrace, provedou se následující akce:

- Na novou registraci se převedou všechny služby Azure, všechna předplatná, účty, oddělení a celá struktura registrace, včetně všech správců oddělení EA.
- U registrace se nastaví stav _Přenesené_. Přenesená registrace bude k dispozici jenom pro sestavy, které se týkají historie použití.
- Do přenesené registrace už nemůžete přidávat role ani předplatná. Přenesený stav brání dalšímu použití této registrace.
- Zbývající zůstatek peněžního závazku ve smlouvě se ruší, včetně budoucích podmínek.
-    Pokud registrace, ze které provádíte převod, obsahuje zakoupené rezervované instance, kupní cena rezervovaných instancí zůstane ve zdrojové registraci, ale všechny výhody rezervovaných instancí se převedou do nové registrace.
-    Jednorázový poplatek za nákup na marketplace ani žádné měsíční paušální poplatky již vzniklé ve staré registraci se do nové registrace nepřevedou. Poplatky za marketplace na základě spotřeby se převedou.

### <a name="effective-transfer-date"></a>Platné datum převodu

Platným datem převodu může být datum, které je stejné nebo následuje po počátečním datu cílové registrace.

Využití zdrojové registrace se odečte z peněžního závazku nebo se účtuje jako nadlimitní využití. Využití, ke kterému dojde po platném datu převodu, se přenese do nové registrace, kde se odpovídajícím způsobem odúčtuje.

### <a name="prerequisites"></a>Požadavky

K žádosti o převod registrace je potřeba sdělit následující informace:

- U zdrojové registrace uveďte její číslo.
- U cílové registrace uveďte číslo registrace, do které probíhá převod.
- Platné datum převodu registrace může být stejné datum, jako je počáteční datum cílové registrace, nebo datum pozdější. Vybrané datum neovlivňuje použití u již vydaných faktur za nadlimitní využití.

Další body, na které je potřeba myslet před převodem registrace:

- Cílovou a zdrojovou registraci musí schválit správci EA.
- Pokud převod registrace vašim požadavkům nevyhovuje, zvažte možnost převodu účtu.
- Stav zdrojové registrace se změní na Převedeno nesený a bude k dispozici jenom pro účely generování sestav historie využití.

### <a name="monetary-commitment"></a>Peněžní závazek

Peněžní závazek nejde mezi registracemi převést. Zůstatky peněžních závazků jsou smluvně vázány na registraci, kde byly objednány. V rámci převodu účtu nebo registrace se peněžní závazek nepřevádí.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Převody účtů a registrací nemají vliv na služby

Při převodu účtu nebo registrace nedochází k výpadkům. Pokud jsou poskytnuté všechny požadované informace, může převod proběhnout tentýž den jako požadavek.

## <a name="change-account-owner"></a>Změna vlastníka účtu

Na portálu Azure EA můžete převádět předplatná od jednoho vlastníka účtu k jinému. Další informace najdete v tématu o [změně vlastníka účtu](ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Účinek převodu předplatného

Pokud se předplatné Azure převádí do účtu ve stejném tenantovi Azure Active Directory, zachovají si všichni uživatelé, skupiny a instanční objekty, které ke správě prostředků používaly [přístup na základě role (RBAC)](../../role-based-access-control/overview.md), stále svůj přístup.

Zobrazení uživatelů s přístupem RBAC k předplatnému:

1. Na webu Azure Portal otevřete **Předplatná**.
2. Vyberte předplatné, které chcete zobrazit, a pak vyberte **Řízení přístupu (IAM)** .
3. Vyberte **Přiřazení rolí**. Na stránce Přiřazení rolí se zobrazí seznam všech uživatelů, kteří mají k předplatnému přístup RBAC.

Pokud se předplatné převede do účtu jiného tenanta Azure AD, tak všichni uživatelé, skupiny a instanční objekty, které měly ke správě prostředků přístup [RBAC](../../role-based-access-control/overview.md), tento přístup _ztratí_. I když neexistuje přístup RBAC, může být přístup k předplatnému dostupný prostřednictvím bezpečnostních mechanismů, jako je například:

- Certifikáty pro správu, které udělují uživateli oprávnění správce prostředků předplatného. Další informace najdete v tématu o [vytvoření a nahrání certifikátu pro správu Azure](../../cloud-services/cloud-services-certs-create.md).
- Přístupové klíče pro služby, jako je Storage. Další informace najdete v tématu [Přehled účtu Azure Storage](../../storage/common/storage-account-overview.md).
- Oprávnění pro vzdálený přístup ke službám, jako je Azure Virtual Machines.

Pokud příjemce potřebuje omezit přístup k prostředkům Azure, měl by zvážit aktualizaci tajných klíčů přidružených k dané službě. Většinu prostředků můžete aktualizovat takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce centra vyberte **Všechny prostředky**.
3. Vyberte prostředek.
4. Na stránce prostředku vyberte **Nastavení**. Tady se můžete podívat na stávající tajné klíče nebo je můžete aktualizovat.

## <a name="delete-subscription"></a>Odstranění předplatného

Odstranění předplatného, ve kterém jste vlastníkem účtu:

1. Přihlaste se k webu Azure Portal pomocí přihlašovacích údajů přidružených k vašemu účtu.
1. V nabídce centra vyberte **Předplatná**.
1. Na kartě Předplatná v levém horním rohu stránky vyberte předplatné, které chcete zrušit, a výběrem možnosti **Zrušit předplatné** otevřete kartu Zrušení.
1. Zadejte název předplatného, zvolte důvod pro zrušení a vyberte tlačítko **Zrušit předplatné**.

Předplatná můžou rušit pouze správci účtů.

Další informace najdete v části [Co se stane po zrušení předplatného?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription).

## <a name="delete-an-account"></a>Odstranění účtu

Odebrat je možné pouze aktivní účty bez aktivních předplatných.

1. V levém navigačním panelu portálu Enterprise Portal vyberte **Spravovat**.
1. Vyberte kartu **Account** (Účet).
1. V tabulce Účty vyberte účet, který chcete odstranit.
1. Vyberte symbol X na pravé straně řádku účtu.
1. Jakmile účet nebude obsahovat žádná aktivní předplatná, výběrem možnosti **Ano** pod řádkem účtu potvrďte odebrání účtu.

## <a name="update-notification-settings"></a>Aktualizace nastavení oznámení

Registrace podnikových správců probíhá automaticky, aby mohli dostávat oznámení o použití, která se týkají jejich registrace. Každý podnikový správce může změnit interval jednotlivých oznámení nebo může tato oznámení úplně vypnout.

Kontakty pro oznámení se na portálu Azure EA zobrazují v oddílu **Kontakt pro oznámení**. Správou kontaktů pro oznámení zajistíte doručování oznámení Azure EA v organizaci těm správným lidem.

Zobrazení aktuálního nastavení oznámení:

1. Na portálu Azure EA přejděte na **Správa** > **Kontakt pro oznámení**.
2. E-mailová adresa – e-mailová adresa přidružená k účtu Microsoft podnikového správce nebo k pracovnímu či školnímu účtu, na který chodí oznámení.
3. Frekvence oznamování nefakturovaného zůstatku – nastavená frekvence posílání oznámení každému podnikovému správci.

Přidání kontaktu:

1. Vyberte **+ Přidat kontakt**.
2. Zadejte e-mailovou adresu a potvrďte ji.
3. Vyberte **Uložit**.

Nový kontakt pro oznámení se zobrazí v oddílu **Kontakt pro oznámení**. Pokud chcete změnit frekvenci oznamování, vyberte kontakt pro oznámení a pak vyberte symbol tužky, který je napravo od vybraného řádku. Nastavte **denní**, **týdenní**, **měsíční** nebo **žádnou** frekvenci.

Můžete také potlačit _blížící se koncové datum pokrytého období_ a _vypnout a zrušit zřízené blížící se datum_ u oznámení o životním cyklu. Pokud vypnete oznámení o životním cyklu, potlačíte tím oznámení o pokrytém období a o datu ukončení smlouvy.

## <a name="manage-partner-administrators"></a>Správa správců partnerů

Každý správce partnera na portálu Azure EA může přidat nebo odebrat ostatní správce partnerů. Správci partnerů jsou přidružení k partnerským organizacím nepřímých registrací, a ne přímo k registracím.

### <a name="add-a-partner-administrator"></a>Přidání správce partnera

Pokud chcete zobrazit seznam všech registrací přidružených ke stejné partnerské organizaci jako aktuální uživatel, vyberte kartu **Registrace** a zaškrtněte políčko požadované registrace.

1. Přihlaste se jako správce partnera.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Vyberte kartu **Partner**.
1. Vyberte **+ Přidat správce** a vyplňte e-mailovou adresu, kontakt pro oznámení a podrobnosti o oznámení.
1. Vyberte **Přidat**.

### <a name="remove-a-partner-administrator"></a>Odebrání správce partnera

Pokud chcete zobrazit seznam všech registrací přidružených ke stejné partnerské organizaci jako aktuální uživatel, vyberte kartu **Registrace** a zaškrtněte políčko požadované registrace.

1. Přihlaste se jako správce partnera.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Vyberte kartu **Partner**.
1. V části Správce vyberte odpovídající řádek správce, kterého chcete odebrat.
1. Vyberte symbol X na pravé straně.
1. Potvrďte, že skutečně chcete provést odstranění.

## <a name="manage-partner-notifications"></a>Správa oznámení pro partnery

Správci partnerů můžou spravovat frekvenci, s jakou budou u svých registrací dostávat oznámení o jejich využití. Oznámení o nefakturovaném zůstatku dostávají automaticky jednou týdně. Frekvenci jednotlivých oznámení můžou změnit na měsíční, týdenní, denní nebo můžou oznámení úplně vypnout.

Pokud uživatel nedostává oznámení, zkontrolujte, jestli jsou správně nastavená. Použijte k tomu následující postup.

1. Přihlaste se na portál Azure EA jako správce partnera.
2. Vyberte **Spravovat** a pak vyberte kartu **Partner**.
3. V části Správce si prohlédněte seznam správců.
4. Pokud chcete upravit předvolby oznámení, najeďte myší na příslušného správce a vyberte symbol tužky.
5. Podle potřeby zvyšte frekvenci a aktualizujte životní cyklus oznámení.
6. Pokud potřebujete přidat kontakt, vyberte **Přidat**.
7. Vyberte **Uložit**.

![Příklad ukazující pole pro přidání kontaktu ](./media/ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>Zobrazení registrací pro správce partnerů

Správci partnerů můžou zobrazit seznam všech svých přímých i nepřímých registrací na portálu Azure EA. U jednotlivých registrací se společně s číslem registrace, názvem registrace, zůstatkem a nadlimitním využitím zobrazí pole obsahující přehled registrace.

### <a name="view-a-list-of-enrollments"></a>Zobrazení seznamu registrací

1. Přihlaste se jako správce partnera.
1. V navigačním panelu na levé straně stránky vyberte **Spravovat**.
1. Klikněte na kartu **Enrollment** (Registrace).
1. Zaškrtněte políčko u požadované registrace.

V horní části stránky zůstane zobrazení všech registrací, včetně polí pro jednotlivé registrace. Mezi registracemi můžete přecházet také výběrem čísla aktuální registrace v navigačním panelu na levé straně stránky. Zobrazí se automaticky otevírané okno, ve kterém můžete vyhledávat registrace nebo zaškrtnutím odpovídajícího políčka vybrat jinou registraci.

## <a name="azure-sponsorship-offer"></a>Nabídka Azure Sponsorship

Nabídka Azure Sponsorship se týká omezeného sponzorovaného účtu Microsoft Azure. Nabídka je k dispozici jenom na základě e-mailové pozvánky určené omezenému počtu zákazníků, které vybral Microsoft. Pokud máte na nabídku Microsoft Azure Sponsorship nárok, přijde vám e-mailem pozvánka určená pro vaše ID účtu.

Další informace získáte vytvořením [žádosti o podporu při aktivaci nabídky Sponsorship](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Převod na ověřování pomocí pracovního nebo školního účtu

Podnikoví uživatelé Azure můžou převést typ ověřování z ověřování pomocí účtu Microsoft (MSA nebo Live ID) na ověřování pomocí pracovního nebo školního účtu (s využitím Active Directory v Azure).

Začněte následovně:

1. Na portálu Azure EA přidejte pracovní nebo školní účet do požadovaných rolí.
1. Pokud dojde k chybám, účet možná není v Active Directory platný.  Azure používá hlavní název uživatele (UPN), který není vždy stejný jako e-mailová adresa.
1. Ověřte se na portálu Azure EA pomocí pracovního nebo školního účtu.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Převod předplatných z účtů Microsoft na pracovní nebo školní účty:

1. Přihlaste se k portálu pro správu pomocí účtu Microsoft, který je vlastníkem předplatných.
1. Pomocí převodu vlastnictví účtu proveďte přesun na nový účet.
1. Účet Microsoft by teď neměl obsahovat žádná aktivní předplatná a můžete ho odstranit.
1. Všechny odstraněné účty se kvůli zachování historie fakturace budou na portálu dál zobrazovat v neaktivním stavu.  Zaškrtnutím políčka je můžete vyfiltrovat a zobrazit pouze aktivní účty.

## <a name="account-subscription-ownership-faq"></a>Nejčastější dotazy k vlastnictví předplatného účtu

V tomto dokumentu najdete odpovědi na nejčastější dotazy související s vlastnictvím předplatného účtu.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Kolik vlastníků účtů Azure je možné mít pro jedno předplatné?

V jednom předplatném je povolený pouze jeden vlastník účtu.  Další role je možné přidat pomocí řízení přístupu na základě role nebo řízení přístupu (IAM) na kartě předplatného v levém horním rohu stránky na webu [portal.azure.com](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Může být vlastník účtu Azure uvedený u více než jednoho oddělení?

Ne, vlastník účtu může být přidružený pouze k jednomu oddělení. Tato zásada pomáhá zajistit přesné monitorování a rozdělování útraty a nákladů přidružených k odpovídajícímu oddělení v rámci registrace EA na portálu Azure EA Portal.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Může být vlastník účtu Azure uvedený jako skupina zabezpečení?

Ne, vlastníkem předplatného musí být jedinečný účet Microsoft (MSA) nebo ověřování Azure Active Directory (AAD). Pokud chcete v rámci organizace zohlednit nástupnictví, můžete zvážit vytvoření obecných účtů a použití AAD ke správě přístupu k předplatným.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Může jeden uživatel vlastnit více předplatných?

Vlastník účtu Azure může vytvářet a spravovat neomezené množství předplatných.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Jak můžu zobrazit všechna předplatná organizace nebo k nim získat přístup?

V současné době je potřeba k tomu použít zásady. To znamená, že byste museli vyžadovat, aby se pro každé vytvořené předplatné váš účet pomocí řízení přístupu na základě role přidal do role předplatného.

### <a name="where-do-i-go-to-create-a-subscription"></a>Kde můžu vytvořit předplatné?

Abyste mohli vytvořit předplatné nabídky Enterprise Azure (EA), správce vaší registrace EA musí na portálu Azure EA Portal přidat váš účet do role vlastníka účtu. Pak se musíte přihlásit k portálu Azure EA Portal a získat oprávnění k vytváření předplatných nabídek EA. První předplatné EA doporučujeme vytvořit pomocí odkazu + Přidat předplatné na kartě předplatného na portálu EA Portal.  Jakmile však váš účet získá oprávnění, může být snadnější vytvářet předplatná na webu portal.azure.com na kartě předplatného v levém horním rohu stránky, kde můžete předplatné v jednom kroku vytvořit i přejmenovat.

### <a name="who-can-create-a-subscription"></a>Kdo může vytvořit předplatné?

Abyste mohli vytvořit předplatné nabídky Enterprise Azure, musíte na [portálu EA](https://ea.azure.com) mít oprávnění role vlastníka účtu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak vám můžou [rezervace virtuálních počítačů](ea-portal-vm-reservations.md) pomoci ušetřit peníze.
- Pokud potřebujete pomoc při řešení potíží s portálem Azure EA, přečtěte si téma [Řešení potíží s přístupem k portálu Azure EA](ea-portal-troubleshoot.md).
