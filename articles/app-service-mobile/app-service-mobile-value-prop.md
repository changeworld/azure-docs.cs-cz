---
title: O mobilních aplikacích
description: Zjistěte, jaké výhody App Service přináší pro vaše firemní mobilní aplikace.
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.tgt_pltfrm: mobile-multiple
ms.topic: overview
ms.date: 06/25/2019
ms.openlocfilehash: 33548f202046310b91fc79d38ac7d8fb18a8727e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79499421"
---
# <a name="about-mobile-apps-in-azure-app-service"></a><a name="getting-started"> </a>Informace o funkci Mobile Apps v Azure App Service

Azure App Service nabízí plně spravovanou [platformu jako službu](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) pro profesionální vývojáře. Tato služba přináší bohatou sadu možností pro webové, mobilní a integrační scénáře. 

Funkce Mobile Apps služby Azure App Service nabízí podnikovým vývojářům a systémovým integrátorům vysoce škálovatelnou a globálně dostupnou platformu pro vývoj mobilních aplikací.

![Vizuální přehled možností funkce Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Proč Mobile Apps?
S funkcí Mobile Apps můžete:

* **Vytvářet nativní a multiplatformní aplikace:** Ať už vytváříte nativní aplikace pro iOS, Android a Windows, nebo multiplatformní aplikace pomocí Xamarinu nebo Cordovy (PhoneGap), můžete využívat službu App Service díky nativním sadám SDK.
* **Nastavit připojení k podnikovým systémům:** S funkcí Mobile Apps můžete v řádu minut přidat podnikové přihlašování a připojovat se k místním nebo cloudovým firemním prostředkům.
* **Vytvářet aplikace připravené pro režim offline se synchronizací dat:** Větší produktivitu mobilních pracovníků zajistíte vytvářením aplikací, které fungují offline a využívají funkci Mobile Apps k synchronizaci dat na pozadí, když se obnoví připojení se zdroji firemních dat nebo s rozhraními API softwaru jako služby (SaaS).
* **Odesílat nabízená oznámení milionům příjemců v řádu sekund:** Zákazníky oslovíte pomocí rychlých nabízených oznámení, která můžete odesílat na jakékoli zařízení, přizpůsobovat podle jejich potřeb a zasílat v nejvhodnější dobu.

## <a name="mobile-apps-features"></a>Funkce Mobile Apps
Pro vývoj mobilních řešení využívajících cloud jsou důležité následující funkce:

* **Ověřování a autorizace:** Podpora zprostředkovatelů identity, mezi které patří Azure Active Directory pro podnikové ověřování i poskytovatelé služeb sociálních sítí, jako je Facebook, Google, Twitter a účty Microsoft. Funkce Mobile Apps nabízí službu OAuth 2.0 pro každého zprostředkovatele. Můžete také integrovat sadu SDK pro daného zprostředkovatele, aby se zajistila specifická funkcionalita.

    Projděte si další informace o [funkcích ověřování].

* **Přístup k datům:** Funkce Mobile Apps poskytuje zdroj dat OData verze 3, který skvěle funguje s mobilními zařízeními a je propojený se službou Azure SQL Database nebo místním SQL Serverem. Protože tato služba může být založena na rozhraní Entity Framework, můžete ji snadno integrovat s jinými poskytovateli dat NoSQL i SQL, včetně [Azure Table Storage], MongoDB, [Azure Cosmos DB] a poskytovateli rozhraní API pro SaaS, jako je Office 365 a Salesforce.com.

* **Offline synchronizace:** Klientské sady SDK usnadňují vytváření robustních mobilních aplikací s rychlou odezvou, které pracují s offline datovou sadou. Tuto datovou sadu můžete automaticky synchronizovat s daty back-end, včetně podpory řešení konfliktů.

  Projděte si další informace o [datových funkcích].

* **Nabízená oznámení:** Klientské sady SDK se hladce integrují s registračními funkcemi služby Azure Notification Hubs, takže můžete současně zasílat nabízená oznámení milionům uživatelů.

  Projděte si další informace o [funkcích pro nabízená oznámení].

* **Klientské sady SDK:** K dispozici je kompletní paleta klientských sad SDK, které zahrnují nativní vývoj ([iOS], [Android] a [Windows]), multiplatformní vývoj ([Xamarin.iOS a Xamarin.Android], [Xamarin.Forms]) a vývoj hybridních aplikací ([Apache Cordova]). Všechny klientské sady SDK jsou k dispozici s licencí MIT a jako open-source.

## <a name="azure-app-service-features"></a>Funkce Azure App Service
U systémů pro mobilní zařízení v provozním prostředí jsou užitečné následující funkce platformy:

* **Automatické škálování:** Se službou App Service můžete rychle vertikálně nebo horizontálně navyšovat kapacitu, aby bylo možné zvládnout jakoukoli zátěž vyplývající z příchozích zákaznických požadavků. Počet a velikost virtuálních počítačů můžete vybrat ručně nebo je možné u back-endu mobilní aplikace nastavit automatické škálování podle plánu nebo momentální zátěže.

  Projděte si další informace o [automatickém škálování].

* **Přípravná prostředí:** App Service umožňuje spuštění několika verzí webu, takže můžete provádět testování A/B, testovat v provozním prostředí v rámci většího plánu DevOps nebo místně fázovat nový back-end.

  Projděte si další informace o [přípravná prostředí].

* **Průběžné nasazování:** App Service umožňuje integraci s běžnými systémy _správy zdrojového kódu_ (SCM), které umožňují jednoduché nasazení nové verze back-endu.

  Projděte si další informace o [možnostech nasazení](../app-service/deploy-local-git.md).

* **Virtuální sítě:** App Service umožňuje připojení k místním prostředkům přes virtuální síť, Azure ExpressRoute nebo hybridní připojení.

  Projděte si další informace pro [hybridní připojení], [virtuální sítě] a [ExpressRoute].

* **Izolovaná a vyhrazená prostředí:** Pro zabezpečené spouštění aplikací Azure App Service je možné App Service spustit v plně izolovaném a vyhrazeném prostředí. Toto prostředí je ideální pro úlohy aplikací vyžadující velké škálování, izolaci nebo zabezpečený přístup k síti.

  Další informace o [prostředích služby App Service].

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s funkcí Mobile Apps ve službě Azure App Service, dokončete [úvodní] kurz. Tento kurz popisuje základy vytváření libovolného mobilního back-endu a klienta. Zabývá se také integrací ověřování, offline synchronizací a nabízenými oznámeními. Kurz můžete absolvovat vícekrát, pokaždé pro jinou klientskou aplikaci.

Pokud chcete další informace o funkci Mobile Apps, prohlédněte si naši [mapu kurzů].
Další informace o platformě Služby aplikací Azure najdete v [tématu Azure App Service].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Začínáme]: app-service-mobile-ios-get-started.md
[Azure Table storage]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[funkcích ověřování]: ./app-service-mobile-auth.md
[datových funkcích]: ./app-service-mobile-offline-data-sync.md
[funkcích pro nabízená oznámení]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS a Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatické škálování]: ../app-service/manage-scale-up.md
[přípravná prostředí]: ../app-service/deploy-staging-slots.md
[hybridní připojení]: ../biztalk-services/integration-hybrid-connection-overview.md
[virtuální sítě]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[Prostředí služby App Service]: ../app-service/environment/intro.md
[mapa kurzů]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
[Azure App Service]: ../app-service/overview.md
