---
title: 'Kurz: Mapování existujícího vlastního názvu DNS'
description: Zjistěte, jak přidat existující vlastní název domény DNS (individuální doména) k webové aplikaci, back-endu mobilní aplikace nebo aplikaci API ve službě Azure App Service.
keywords: app service, azure app service, domain mapping, domain name, existing domain, hostname
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: adc9b60ce1c31076a91ec44b9656752b464e024d
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811783"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Kurz: Mapování existujícího vlastního názvu DNS na službu Azure App Service

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. Tento kurz ukazuje, jak mapovat existující vlastní název DNS na Azure App Service.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Mapovat subdoménu (například `www.contoso.com`) pomocí záznamu CNAME
> * Mapovat kořenovou doménu (například `contoso.com`) pomocí záznamu A
> * Mapovat zástupnou doménu (například `*.contoso.com`) pomocí záznamu CNAME
> * Přesměrovat výchozí adresu URL do vlastního adresáře
> * Automatizovat mapování domén pomocí skriptů

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Vytvořit plán služby App Service](/azure/app-service/) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.
* Zakoupit název domény a ověřit, že máte přístup k registru DNS pro vašeho poskytovatele domény (například GoDaddy).

  Abyste například mohli přidat záznamy DNS pro `contoso.com` a `www.contoso.com`, musíte mít možnost konfigurovat nastavení DNS pro kořenovou doménu `contoso.com`.

  > [!NOTE]
  > Pokud nemáte žádný existující název domény, zvažte [zakoupení domény pomocí webu Azure Portal](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Příprava aplikace

Chcete-li namapovat vlastní název DNS na webovou aplikaci, musí být [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) webové aplikace placenou úrovní **(sdílená**, **základní**, **standardní**, **prémiová** nebo **spotřeba** pro funkce Azure). V tomto kroku se ujistíte, že je aplikace App Service na podporované cenové úrovni.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

### <a name="select-the-app-in-the-azure-portal"></a>Výběr aplikace na webu Azure Portal

Vyhledejte a vyberte **služby App Services**.

![Vybrat služby aplikace](./media/app-service-web-tutorial-custom-domain/app-services.png)

Na stránce **App Services** vyberte název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/select-app.png)

Zobrazí se stránka pro správu aplikace App Service.  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

V levém navigačním panelu na stránce aplikace se posuňte do části **Nastavení** a vyberte **Vertikálně navýšit kapacitu (plán služby App Service)**.

![Nabídka Vertikálně navýšit kapacitu](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Aktuální úroveň aplikace je zvýrazněná modrým ohraničením. Zkontrolujte, že aplikace není na úrovni **F1**. Vlastní DNS se na úrovni **F1** nepodporuje. 

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Pokud plán služby App Service není úrovně **F1**, zavřete stránku **Vertikálně navýšit kapacitu** a přejděte k části [Mapování záznamu CNAME](#cname).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Vyberte některou z placených úrovní (**D1**, **B1**, **B2**, **B3** nebo kteroukoli úroveň v kategorii **Produkční**). Další možnosti se zobrazí po kliknutí na odkaz **Zobrazit další možnosti**.

Klikněte na **Použít**.

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Až se zobrazí následující oznámení, operace škálování je dokončená.

![Potvrzení operace škálování](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="map-your-domain"></a>Mapování domény

K mapování vlastního názvu DNS na službu App Service můžete použít **záznam CNAME** nebo **záznam A**. Postupujte podle příslušných kroků:

- [Mapování záznamu CNAME](#map-a-cname-record)
- [Mapování záznamu A](#map-an-a-record)
- [Mapování zástupné domény (pomocí záznamu CNAME)](#map-a-wildcard-domain)

> [!NOTE]
> Záznamy CNAME byste měli použít pro všechny vlastní názvy DNS s výjimkou kořenových domén (například `contoso.com`). Pro kořenové domény použijte záznamy A.

### <a name="map-a-cname-record"></a>Mapování záznamu CNAME

V tomto příkladu přidáte záznam CNAME pro subdoménu `www` (například `www.contoso.com`).

#### <a name="access-dns-records-with-domain-provider"></a>Přístup k záznamům DNS u poskytovatele domény

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME

Přidejte záznam CNAME k mapování subdomény na výchozí`<app_name>.azurewebsites.net`název `<app_name>` domény aplikace ( , kde je název vaší aplikace).

Pro příklad domény `www.contoso.com` přidejte záznam CNAME, který mapuje název `www` na `<app_name>.azurewebsites.net`.

Po přidání záznamu CNAME bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Povolení mapování záznamu CNAME v Azure

V levém navigačním panelu na stránce aplikace na webu Azure Portal vyberte **Vlastní domény**.

![Nabídka Vlastní domény](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stránce **Vlastní domény** aplikace přidejte do seznamu plně kvalifikovaný vlastní název DNS (`www.contoso.com`).

Vyberte **+** ikonu vedle **položky Přidat vlastní doménu**.

![Přidat název hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Zadejte plně kvalifikovaný název domény, pro který jste přidali záznam CNAME, například `www.contoso.com`.

Vyberte **Ověřit**.

Zobrazí se stránka **Přidat vlastní doménu.**

Ujistěte se, že **typ záznamu Hostname** je nastaven na **CNAME (www\.example.com nebo subdomény)**.

Vyberte **Přidat vlastní doménu**.

![Přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Může chvíli trvat, než se nová vlastní doména projeví na stránce **Vlastní domény** aplikace. Zkuste aktualizovat prohlížeč, aby se data aktualizovala.

![Přidaný záznam CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

> [!NOTE]
> Popisek **Není zabezpečený** pro vaši vlastní doménu znamená, že ještě není vázán na certifikát TLS/SSL a jakýkoli požadavek HTTPS z prohlížeče do vaší vlastní domény se v závislosti na prohlížeči zobrazí a zobrazí se chyba nebo upozornění. Pokud chcete přidat vazbu TLS, [přečtěte si témat u tématu Zabezpečení vlastního názvu DNS s vazbou TLS/SSL ve službě Azure App Service](configure-ssl-bindings.md).

Pokud jste nějaký krok vynechali nebo jste někde udělali překlep, ve spodní části stránky se zobrazí chyba ověření.

![Chyba ověření](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mapování záznamu A

V tomto příkladu přidáte záznam A pro kořenovou doménu (například `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Zkopírování IP adresy aplikace

K mapování záznamu A potřebujete externí IP adresu aplikace. Tuto IP adresu najdete na stránce **Vlastní domény** aplikace na webu Azure Portal.

V levém navigačním panelu na stránce aplikace na webu Azure Portal vyberte **Vlastní domény**.

![Nabídka Vlastní domény](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stránce **Vlastní domény** zkopírujte IP adresu aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-domain-provider"></a>Přístup k záznamům DNS u poskytovatele domény

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Vytvoření záznamu A

K mapování záznamu A na aplikaci vyžaduje služba App Service **dva** záznamy DNS:

- Záznam **A** pro mapování na IP adresu aplikace.
- Záznam **TXT,** který se mapuje na `<app_name>.azurewebsites.net`výchozí název domény aplikace . Služba App Service používá tento záznam pouze během konfigurace k ověření, že jste vlastníkem vlastní domény. Po ověření a konfiguraci vlastní domény ve službě App Service můžete tento záznam TXT odstranit.

Pro příklad domény `contoso.com` vytvořte záznamy A a TXT podle následující tabulky (`@` obvykle představuje kořenovou doménu).

| Typ záznamu | Hostitel | Hodnota |
| - | - | - |
| A | `@` | IP adresa z části [Zkopírování IP adresy aplikace](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

> [!NOTE]
> Chcete-li přidat subdoménu (například) `www.contoso.com`pomocí záznamu A namísto doporučeného záznamu [CNAME](#map-a-cname-record), měl by záznam A a TXT vypadat jako v následující tabulce:
>
> | Typ záznamu | Hostitel | Hodnota |
> | - | - | - |
> | A | `www` | IP adresa z části [Zkopírování IP adresy aplikace](#info) |
> | TXT | `www` | `<app_name>.azurewebsites.net` |
>

Po přidání záznamů bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Stránka záznamů DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Povolení mapování záznamu A v aplikaci

Zpět na stránce **Vlastní domény** aplikace na webu Azure Portal přidejte do seznamu plně kvalifikovaný vlastní název DNS (například `contoso.com`).

Vyberte **+** ikonu vedle **položky Přidat vlastní doménu**.

![Přidat název hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Zadejte plně kvalifikovaný název domény, pro který jste nakonfigurovali záznam A, například `contoso.com`.

Vyberte **Ověřit**.

Zobrazí se stránka **Přidat vlastní doménu.**

Ujistěte se, že **Typ záznamu názvu hostitele** je nastavený na **Záznam A (www.example.com)**.

Vyberte **Přidat vlastní doménu**.

![Přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Může chvíli trvat, než se nová vlastní doména projeví na stránce **Vlastní domény** aplikace. Zkuste aktualizovat prohlížeč, aby se data aktualizovala.

![Přidaný záznam A](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

> [!NOTE]
> Popisek **Není zabezpečený** pro vaši vlastní doménu znamená, že ještě není vázán na certifikát TLS/SSL a jakýkoli požadavek HTTPS z prohlížeče do vaší vlastní domény se v závislosti na prohlížeči zobrazí a zobrazí se chyba nebo upozornění. Pokud chcete přidat vazbu TLS, [přečtěte si témat u tématu Zabezpečení vlastního názvu DNS s vazbou TLS/SSL ve službě Azure App Service](configure-ssl-bindings.md).

Pokud jste nějaký krok vynechali nebo jste někde udělali překlep, ve spodní části stránky se zobrazí chyba ověření.

![Chyba ověření](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mapování zástupné domény

V tomto příkladu namapujete na aplikaci App Service [zástupný název DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (například `*.contoso.com`) přidáním záznamu CNAME.

#### <a name="access-dns-records-with-domain-provider"></a>Přístup k záznamům DNS u poskytovatele domény

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME

Přidejte záznam CNAME, který namapuje zástupný název`<app_name>.azurewebsites.net`na výchozí název domény aplikace ( ).

Pro příklad domény `*.contoso.com` bude záznam CNAME mapovat název `*` na `<app_name>.azurewebsites.net`.

Po přidání záznamu CNAME bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Povolení mapování záznamu CNAME v aplikaci

Teď můžete do aplikace přidat jakoukoli subdoménu, která odpovídá zástupnému názvu (například `sub1.contoso.com` a `sub2.contoso.com` odpovídají `*.contoso.com`).

V levém navigačním panelu na stránce aplikace na webu Azure Portal vyberte **Vlastní domény**.

![Nabídka Vlastní domény](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Vyberte **+** ikonu vedle **položky Přidat vlastní doménu**.

![Přidat název hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Zadejte plně kvalifikovaný název domény, který odpovídá zástupné doméně (například `sub1.contoso.com`), a pak vyberte **Ověřit**.

Aktivuje se tlačítko **Přidat vlastní doménu.**

Ujistěte se, že **typ záznamu Hostname** je nastaven na **záznam CNAME\.(www example.com nebo jakékoli subdomény)**.

Vyberte **Přidat vlastní doménu**.

![Přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Může chvíli trvat, než se nová vlastní doména projeví na stránce **Vlastní domény** aplikace. Zkuste aktualizovat prohlížeč, aby se data aktualizovala.

Znovu **+** vyberte ikonu a přidejte další vlastní doménu, která odpovídá doméně se zástupnými symboly. Přidejte například `sub2.contoso.com`.

![Přidaný záznam CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

> [!NOTE]
> Popisek **Zabezpečení pro** vaši vlastní doménu znamená, že ještě není vázán na certifikát TLS/SSL a v závislosti na prohlížeči se zobrazí jakýkoli požadavek HTTPS z prohlížeče do vaší vlastní domény a zobrazí se chyba nebo upozornění. Pokud chcete přidat vazbu TLS, [přečtěte si témat u tématu Zabezpečení vlastního názvu DNS s vazbou TLS/SSL ve službě Azure App Service](configure-ssl-bindings.md).

## <a name="test-in-browser"></a>Test v prohlížeči

Přejděte na názvy DNS, které jste předtím nakonfigurovali (například `contoso.com`, `www.contoso.com`, `sub1.contoso.com` a `sub2.contoso.com`).

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Vyřešit 404 "Nebyl nalezen"

Pokud se při přechodu na adresu URL vaší vlastní domény zobrazí chyba HTTP 404 (Nenalezeno), pomocí webu <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a> ověřte, že se doména překládá na IP adresu vaší aplikace. Pokud ne, může to být způsobené jedním z následujících důvodů:

- V konfiguraci vlastní domény chybí záznam A nebo záznam CNAME.
- Prohlížeč uložil do mezipaměti starou IP adresu vaší domény. Vymažte mezipaměť a znovu otestujte překlad DNS. Na počítači s Windows můžete mezipaměť vymazat příkazem `ipconfig /flushdns`.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>Migrace aktivní domény

Pokud chcete do služby App Service migrovat živý web a jeho název domény DNS bez výpadku, přečtěte si článek o [migraci aktivního názvu DNS do služby Azure App Service](manage-custom-dns-migrate-domain.md).

## <a name="redirect-to-a-custom-directory"></a>Přesměrování do vlastního adresáře

Ve výchozím nastavení služba App Service směruje webové požadavky do kořenového adresáře kódu vaší aplikace. Některá webová rozhraní však nezačínají v kořenovém adresáři. Například [Laravel](https://laravel.com/) začíná v podadresáři `public`. Když budeme pokračovat v příkladu DNS `contoso.com`, taková aplikace by byla přístupná na adrese `http://contoso.com/public`. Místo toho byste však měli chtít směrovat `http://contoso.com` do adresáře `public`. Tento krok nezahrnuje překlad DNS, ale přizpůsobení virtuálního adresáře.

Uděláte to tak, že v levém navigačním panelu na stránce vaší webové aplikace vyberete **Nastavení aplikace**. 

V dolní části stránky kořenový virtuální adresář `/` odkazuje ve výchozím nastavení na adresář `site\wwwroot`, což je kořenový adresář kódu vaší aplikace. Změňte ho tak, aby místo toho odkazoval například na adresář `site\wwwroot\public`, a uložte provedené změny.

![Přizpůsobení virtuálního adresáře](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Po dokončení operace by aplikace měla vrátit pravou stránku na `http://contoso.com`kořenové cestě (například).

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Správu vlastních domén můžete automatizovat pomocí skriptů s využitím [Azure CLI](/cli/azure/install-azure-cli) nebo [Azure PowerShellu](/powershell/azure/overview). 

### <a name="azure-cli"></a>Azure CLI 

Následující příkaz přidá do aplikace App Service nakonfigurovaný vlastní název DNS. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Další informace najdete v tématu [Mapování vlastní domény na webovou aplikaci](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující příkaz přidá do aplikace App Service nakonfigurovaný vlastní název DNS.

```powershell  
Set-AzWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net")
```

Další informace najdete v tématu [Přiřazení vlastní domény k webové aplikaci](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Mapovat subdoménu pomocí záznamu CNAME
> * Mapovat kořenovou doménu pomocí záznamu A
> * Mapovat zástupnou doménu pomocí záznamu CNAME
> * Přesměrovat výchozí adresu URL do vlastního adresáře
> * Automatizovat mapování domén pomocí skriptů

Přejdete k dalšímu kurzu, kde se dozvíte, jak svázat vlastní certifikát TLS/SSL s webovou aplikací.

> [!div class="nextstepaction"]
> [Zabezpečení vlastního názvu DNS pomocí vazby TLS/SSL ve službě Azure App Service](configure-ssl-bindings.md)
