---
title: 'Kurz: AuthN/AuthO end-to-end'
description: Zjistěte, jak pomocí ověřování a autorizace pomocí služby App Service zabezpečit aplikace App Service, včetně přístupu ke vzdáleným rozhraním API.
keywords: app service, azure app service, authN, authZ, secure, security, multi-tiered, azure active directory, azure ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/14/2019
ms.custom: seodec18
ms.openlocfilehash: 46750383c1436a2d053d6db7fed858c7c0f8a9fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78226311"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Kurz: Komplexní ověřování a autorizace uživatelů v Azure App Service

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. Kromě toho App Service obsahuje integrovanou podporu [ověřování a autorizace uživatelů](overview-authentication-authorization.md). V tomto kurzu se dozvíte, jak zabezpečit své aplikace pomocí s využitím ověřování a autorizace pomocí služby App Service. V kurzu se používá aplikace ASP.NET Core s front-endem v Angular.js, ta ale slouží pouze jako příklad. Ověřování a autorizace pomocí služby App Service podporují moduly runtime všech jazyků a v tomto kurzu můžete zjistit, jak je použít ve vlastním upřednostňovaném jazyce.

Tento kurz na ukázkové aplikaci ukazuje, jak zabezpečit samostatnou aplikaci (v části [Povolení ověřování a autorizace pro back-endové aplikace](#enable-authentication-and-authorization-for-back-end-app)).

![Jednoduché ověřování a autorizace](./media/app-service-web-tutorial-auth-aad/simple-auth.png)

Kurz také ukazuje, jak zabezpečit vícevrstvé aplikace přistupováním k zabezpečenému back-endovému rozhraní API jménem ověřeného uživatele, a to [z kódu serveru](#call-api-securely-from-server-code) i [z kódu v prohlížeči](#call-api-securely-from-browser-code).

![Pokročilé ověřování a autorizace](./media/app-service-web-tutorial-auth-aad/advanced-auth.png)

Toto je pouze několik z možných scénářů ověřování a autorizace v App Service. 

Tady ucelenější seznam věcí, které se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Povolení integrovaného ověřování a autorizace
> * Zabezpečení aplikací před neověřenými požadavky
> * Použití služby Azure Active Directory jako zprostředkovatele identity
> * Přístup ke vzdálené aplikaci jménem přihlášeného uživatele
> * Zabezpečení volání mezi službami pomocí ověřování tokenu
> * Použití přístupových tokenů z kódu serveru
> * Použití přístupových tokenů z klientského kódu (v prohlížeči)

Podle kroků v tomto kurzu můžete postupovat v systémech macOS, Linux a Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Nainstalujte Git](https://git-scm.com/).
* [Nainstalujte rozhraní .NET Core](https://www.microsoft.com/net/core/).

## <a name="create-local-net-core-app"></a>Vytvoření místní aplikace .NET Core

V tomto kroku nastavíte místní projekt .NET Core. Stejný projekt použijete k nasazení back-endové aplikace API i front-endové webové aplikace.

### <a name="clone-and-run-the-sample-application"></a>Klonování a spuštění ukázkové aplikace

Spuštěním následujících příkazů naklonujte a spusťte ukázkové úložiště.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Přejděte na adresu `http://localhost:5000` a vyzkoušejte přidávání, úpravy a odebírání položek úkolů. 

![Místně spuštěné rozhraní ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/local-run.png)

ASP.NET Core můžete kdykoli zastavit stisknutím `Ctrl+C` v terminálu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Nasazení aplikací do Azure

V tomto kroku nasadíte projekt do dvou aplikací App Service. Jedna je front-endová aplikace a druhá je back-endová aplikace.

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Vytvoření prostředků Azure

Ve službě Cloud Shell spuštěním následujících příkazů vytvořte dvě webové aplikace. Nahraďte `a-z` `0-9` `-` _ \<>a_ _ \<název aplikace back-end>_ dvěma globálně jedinečnými názvy aplikací (platné znaky jsou , a ). Další informace o jednotlivých příkazech najdete v tématu [Rozhraní RESTful API s CORS v Azure App Service](app-service-web-tutorial-rest-api.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> Uložte adresy URL dálkových ovladačů Git pro vaše front-endové a back-endové aplikace, které jsou zobrazeny ve výstupu z aplikace `az webapp create`.
>

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

Po návratu do _okna místního terminálu_ spuštěním následujících příkazů Git proveďte nasazení do back-endové aplikace. Nahraďte _ \<nasazeníLocalGitUrl-of-back-end-app>_ url vzdáleného Gitu, který jste uložili z [prostředků Vytvořit Azure](#create-azure-resources). Po zobrazení výzvy správce mno žílací přihlašovacích údajů správcem přihlašovacích údajů gitu se ujistěte, že jste zadali [přihlašovací údaje pro nasazení](deploy-configure-credentials.md), nikoli přihlašovací údaje, které používáte k přihlášení k portálu Azure.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

V okně místního terminálu spuštěním následujících příkazů Git nasaďte stejný kód do front-endové aplikace. Nahraďte _ \<nasazeníLocalGitUrl-of-front-end-app>_ url vzdáleného Gitu, který jste uložili z [prostředků Vytvořit Azure](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>Přejděte k aplikacím

V prohlížeči přejděte na následující adresy URL, kde se zobrazí dvě funkční aplikace.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

![Rozhraní ASP.NET Core API spuštěné v Azure App Service](./media/app-service-web-tutorial-auth-aad/azure-run.png)

> [!NOTE]
> Pokud se aplikace restartuje, možná si všimnete smazání nových dat. Toto chování je záměrné, protože ukázková aplikace ASP.NET Core využívá databázi v paměti.
>
>

## <a name="call-back-end-api-from-front-end"></a>Volání back-endového rozhraní API z front-endu

V tomto kroku odkážete kód serveru front-endové aplikace na přístup k back-endovému rozhraní API. Později povolíte ověřený přístup z front-endu do back-endu.

### <a name="modify-front-end-code"></a>Úprava front-endového kódu

V místním úložišti otevřete soubor _Controllers/TodoController.cs_. Na začátku `TodoController` třídy přidejte následující řádky a nahraďte _ \<>název back-end-app_ názvem název back-end aplikace:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

Vyhledejte metodu `GetAll()` a nahraďte kód uvnitř složených závorek následujícím kódem:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo").Result;
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

První řádek provede volání `GET /api/Todo` do back-endové aplikace API.

Dále vyhledejte metodu `GetById(long id)` a nahraďte kód uvnitř složených závorek následujícím kódem:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return Content(data, "application/json");
```

První řádek provede volání `GET /api/Todo/{id}` do back-endové aplikace API.

Dále vyhledejte metodu `Create([FromBody] TodoItem item)` a nahraďte kód uvnitř složených závorek následujícím kódem:

```cs
var response = _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", item).Result;
var data = response.Content.ReadAsStringAsync().Result;
return Content(data, "application/json");
```

První řádek provede volání `POST /api/Todo` do back-endové aplikace API.

Dále vyhledejte metodu `Update(long id, [FromBody] TodoItem item)` a nahraďte kód uvnitř složených závorek následujícím kódem:

```cs
var res = _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", item).Result;
return new NoContentResult();
```

První řádek provede volání `PUT /api/Todo/{id}` do back-endové aplikace API.

Dále vyhledejte metodu `Delete(long id)` a nahraďte kód uvnitř složených závorek následujícím kódem:

```cs
var res = _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return new NoContentResult();
```

První řádek provede volání `DELETE /api/Todo/{id}` do back-endové aplikace API.

Uložte všechny provedené změny. V okně místního terminálu nasaďte provedené změny do front-endové aplikace pomocí následujících příkazů Git:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Kontrola změn

Přejděte na adresu `http://<front-end-app-name>.azurewebsites.net` a přidejte několik položek, například `from front end 1` a `from front end 2`.

Přejděte na adresu `http://<back-end-app-name>.azurewebsites.net`, kde se zobrazí položky přidané z front-endové aplikace. Přidejte také několik položek, například `from back end 1` a `from back end 2` a pak aktualizujte front-endovou aplikaci, abyste zjistili, jestli se změny projeví.

![Rozhraní ASP.NET Core API spuštěné v Azure App Service](./media/app-service-web-tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>Konfigurace ověřování

V tomto kroku pro své dvě aplikace povolíte ověřování a autorizaci. Také nakonfigurujete front-endovou aplikaci tak, aby vygenerovala přístupový token, který můžete použít k provádění ověřených volání do back-endové aplikace.

Jako zprostředkovatele identity použijete Azure Active Directory. Další informace najdete v tématu [Konfigurace ověřování pomocí Azure Active Directory pro aplikaci App Services](configure-authentication-provider-aad.md).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Povolení ověřování a autorizace pro back-endovou aplikaci

1. V nabídce [Portál Azure](https://portal.azure.com) vyberte **skupiny prostředků** nebo vyhledejte a vyberte *skupiny prostředků* na libovolné stránce.

1. Ve **skupinách prostředků**vyhledejte a vyberte skupinu prostředků. V **části Přehled**vyberte stránku pro správu back-endové aplikace.

   ![Rozhraní ASP.NET Core API spuštěné v Azure App Service](./media/app-service-web-tutorial-auth-aad/portal-navigate-back-end.png)

1. V levé nabídce back-endové aplikace vyberte **Ověřování / Autorizace**a povolte ověřování pomocí služby App Service výběrem **možnosti Zapnuto**.

1. V části **Akce, která se má provést, když požadavek nebude ověřený** vyberte **Přihlásit se přes Azure Active Directory**.

1. V části **Zprostředkovatelé ověřování**vyberte **Službu Azure Active Directory.** 

   ![Rozhraní ASP.NET Core API spuštěné v Azure App Service](./media/app-service-web-tutorial-auth-aad/configure-auth-back-end.png)

1. Vyberte **Express**, pak přijměte výchozí nastavení, abyste vytvořili novou aplikaci AD a vybrali **OK**.

1. Na stránce **Ověřování / Autorizace** vyberte **Uložit**.

   Jakmile se zobrazí oznámení se zprávou `Successfully saved the Auth Settings for <back-end-app-name> App`, aktualizujte stránku.

1. Znovu vyberte **Azure Active Directory** a pak vyberte **aplikaci Azure AD**.

1. Zkopírujte **ID klienta** aplikace Azure AD do poznámkového bloku. Tuto hodnotu budete potřebovat později.

   ![Rozhraní ASP.NET Core API spuštěné v Azure App Service](./media/app-service-web-tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Povolení ověřování a autorizace pro front-endovou aplikaci

Postupujte podle stejných kroků pro back-endovou aplikaci, ale přeskočte poslední krok. Pro front-endovou aplikaci nepotřebujete ID klienta.

Pokud chcete, přejděte na adresu `http://<front-end-app-name>.azurewebsites.net`. Měla by vás teď přesměrovat na zabezpečenou přihlašovací stránku. Po přihlášení pořád nemáte přístup k datům z back-endové aplikace, protože je stále potřeba provést tři věci:

- Udělení přístupu front-endu do back-endu
- Konfigurace služby App Service tak, aby vracela použitelný token
- Použití tokenu v kódu

> [!TIP]
> Pokud dojde k chybám a překonfigurujete nastavení ověřování nebo autorizace vaší aplikace, tokeny v úložišti tokenů není možné vygenerovat znovu z nového nastavení. Pokud chcete zajistit opětovné vygenerování tokenů, musíte se z aplikace odhlásit a znovu se k ní přihlásit. Snadným způsobem, jak to provést, je použít prohlížeč v privátním režimu a po změně nastavení aplikací zavřít a znovu otevřít prohlížeč v privátním režimu.

### <a name="grant-front-end-app-access-to-back-end"></a>Udělení přístupu front-endové aplikaci do back-endu

Teď, když jste povolili ověřování a autorizaci pro obě své aplikace, využívají obě aplikaci AD. V tomto kroku udělíte front-endové aplikaci oprávnění k přístupu k back-endu jménem uživatele. (Technicky vzato udělíte _aplikaci AD_ front-endu oprávnění k přístupu k _aplikaci_ AD back-endu jménem uživatele.)

1. V nabídce [Portál Azure](https://portal.azure.com) vyberte Azure **Active Directory** nebo vyhledejte a vyberte Azure *Active Directory* z libovolné stránky.

1. Vyberte **Registrace** > aplikací**Vlastněné aplikace**. Vyberte název aplikace front-end a pak vyberte **oprávnění rozhraní API**.

   ![Rozhraní ASP.NET Core API spuštěné v Azure App Service](./media/app-service-web-tutorial-auth-aad/add-api-access-front-end.png)

1. Vyberte **Přidat oprávnění**a pak vyberte **moje api** > **\<back-end-app-name>**.

1. Na stránce **Požádat o oprávnění rozhraní API** pro back-endovou aplikaci vyberte **Delegovaná oprávnění** a **user_impersonation**a pak vyberte **Přidat oprávnění**.

   ![Rozhraní ASP.NET Core API spuštěné v Azure App Service](./media/app-service-web-tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Konfigurace služby App Service tak, aby vracela použitelný přístupový token

Front-endová aplikace má teď požadovaná oprávnění pro přístup k back-endové aplikaci jako přihlášeného uživatele. V tomto kroku nakonfigurujete ověřování a autorizaci pomocí služby App Service tak, abyste získali použitelná přístupový token pro přístup k back-endu. Pro tento krok potřebujete ID klienta back-endu, které jste zkopírovali z [povolení ověřování a autorizace pro back-endovou aplikaci](#enable-authentication-and-authorization-for-back-end-app).

Přihlaste se k [Azure Resource Exploreru](https://resources.azure.com). V horní části stránky klikněte na **Čtení / zápis** a povolte úpravy vašich prostředků Azure.

![Rozhraní ASP.NET Core API spuštěné v Azure App Service](./media/app-service-web-tutorial-auth-aad/resources-enable-write.png)

V levém prohlížeči klikněte na **předplatná** > **_\<>_**  >  **resourceGroups** > **myAuthResourceGroup** > **providers** > **Microsoft.web** > **sites** > **_\<front-end-app-name>_**  >  **config** > **authsettings**.

V zobrazení **authsettings** (nastavení ověřování) klikněte na **Edit** (Upravit). Nastavte `additionalLoginParams` na následující řetězec JSON pomocí ID klienta, které jste zkopírovali. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

![Rozhraní ASP.NET Core API spuštěné v Azure App Service](./media/app-service-web-tutorial-auth-aad/additional-login-params-front-end.png)

Uložte nastavení kliknutím na **PUT**.

Vaše aplikace jsou teď nakonfigurované. Front-end je teď připravený na přístup do back-endu se správným přístupovým tokenem.

Informace o konfiguraci přístupového tokenu pro jiné poskytovatele naleznete v [tématu Aktualizace tokenů zprostředkovatele identity](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="call-api-securely-from-server-code"></a>Zabezpečené volání rozhraní API z kódu serveru

V tomto kroku povolíte dříve upravený kód serveru tak, aby prováděl ověřená volání do back-endového rozhraní API.

Vaše front-endová aplikace má nyní požadované oprávnění a také přidá ID klienta back-endu k parametrům přihlášení. Proto může získat přístupový token k ověření v back-endové aplikaci. App Service poskytuje tento token vašemu kódu serveru tím, že do každého ověřeného požadavku vkládá hlavičku `X-MS-TOKEN-AAD-ACCESS-TOKEN` (viz [Načítání tokenů v kódu aplikace](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!NOTE]
> Tyto hlavičky se vkládají pro všechny podporované jazyky. V jednotlivých jazycích k nim můžete přistupovat standardním způsobem.

V místním úložišti znovu otevřete soubor _Controllers/TodoController.cs_. Pod konstruktor `TodoController(TodoContext context)` přidejte následující kód:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Tento kód do všech volání vzdáleného rozhraní API přidá standardní hlavičku HTTP `Authorization: Bearer <access-token>`. V kanálu provádění požadavků ASP.NET Core MVC se `OnActionExecuting` provede těsně před provedením metody odpovídající akce (například `GetAll()`), takže se v každém z vašich odchozích volání rozhraní API teď uvádí přístupový token.

Uložte všechny provedené změny. V okně místního terminálu nasaďte provedené změny do front-endové aplikace pomocí následujících příkazů Git:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Znovu se přihlaste k `https://<front-end-app-name>.azurewebsites.net`. Na stránce smlouvy o využití uživatelských dat klikněte na **Přijmout**.

Teď byste měli být schopni vytvářet, číst, aktualizovat a odstraňovat data z back-endové aplikace stejně jako dříve. Jediným rozdílem je, že obě aplikace jsou teď zabezpečené ověřováním a autorizací pomocí služby App Service, a to včetně volání mezi službami.

Blahopřejeme! Váš kód serveru teď přistupuje k back-endovým datům jménem ověřeného uživatele.

## <a name="call-api-securely-from-browser-code"></a>Zabezpečené volání rozhraní API z kódu v prohlížeči

V tomto kroku odkážete front-endovou aplikaci Angular.js na back-endové rozhraní API. Tímto způsobem zjistíte, jak načíst přístupový token a provádět s ním volání rozhraní API do back-endové aplikace.

Zatímco kód serveru má přístup k hlavičkám požadavků, klientský kód může stejné přístupové tokeny získat přístupem k `GET /.auth/me` (viz [Načítání tokenů v kódu aplikace](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!TIP]
> V této části se k předvedení zabezpečených volání HTTP používají standardní metody HTTP. Můžete však použít knihovnu [Active Directory Authentication Library (ADAL) pro JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js), která pomůže zjednodušit vzor aplikace Angular.js.
>

### <a name="configure-cors"></a>Konfigurace CORS

V prostředí Cloud Shell povolte CORS na adresu [`az resource update`](/cli/azure/resource#az-resource-update) URL vašeho klienta pomocí příkazu. Nahraďte>back-end-app-name a _ \<_ _ \<zástupné symboly>názvu aplikace._

```azurecli-interactive
az resource update --name web --resource-group myAuthResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<back-end-app-name> --set properties.cors.allowedOrigins="['https://<front-end-app-name>.azurewebsites.net']" --api-version 2015-06-01
```

Tento krok nesouvisí s ověřováním a autorizací. Je však potřeba ho provést, aby prohlížeč povoloval volání rozhraní API mezi doménami z vaší aplikace Angular.js. Další informace najdete v tématu popisujícím [přidání funkcí CORS](app-service-web-tutorial-rest-api.md#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Odkázání aplikace Angular.js na back-endové rozhraní API

V místním úložišti otevřete soubor _wwwroot/index.html_.

Na řádku 51 nastavte proměnnou `apiEndpoint` na adresu URL vaší back-endové aplikace (`https://<back-end-app-name>.azurewebsites.net`). Nahraďte _ \<>název back-end-app_ názvem ve službě App Service.

V místním úložišti otevřete soubor _wwwroot/app/scripts/todoListSvc.js_ a všimněte si, že před všechna volání rozhraní API je připojený koncový bod rozhraní API `apiEndpoint`. Vaše aplikace Angular.js teď volá back-endová rozhraní API. 

### <a name="add-access-token-to-api-calls"></a>Přidání přístupového tokenu do volání rozhraní API

V souboru _wwwroot/app/scripts/todoListSvc.js_ před seznam volání rozhraní API (nad řádek `getItems : function(){`) přidejte do seznamu následující funkci:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Zavoláním této funkce se nastaví výchozí hlavička `Authorization` s přístupovým tokenem. Zavoláte ji v dalším kroku.

V místním úložišti otevřete soubor _wwwroot/app/scripts/app.js_ a vyhledejte následující kód:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Celý blok kódu nahraďte následujícím kódem:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

Tato nová změna přidá mapování `resolve`, které zavolá `/.auth/me` a nastaví přístupový token. Před vytvořením instance kontroleru `todoListCtrl` se ujistí, že máte přístupový token. Díky tomu všechna volání rozhraní API kontrolerem zahrnují token.

### <a name="deploy-updates-and-test"></a>Nasazení aktualizací a test

Uložte všechny provedené změny. V okně místního terminálu nasaďte provedené změny do front-endové aplikace pomocí následujících příkazů Git:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Znovu přejděte na adresu `https://<front-end-app-name>.azurewebsites.net`. Teď byste měli být schopni vytvářet, číst, aktualizovat a odstraňovat data z back-endové aplikace přímo v aplikaci Angular.js.

Blahopřejeme! Váš klientský kód teď přistupuje k back-endovým datům jménem ověřeného uživatele.

## <a name="when-access-tokens-expire"></a>Vypršení platnosti přístupových tokenů

Platnost vašich přístupových tokenů po určité době vyprší. Informace o tom, jak aktualizovat přístupové tokeny, aniž by uživatelé museli znovu ověřit pomocí vaší aplikace, najdete v [tématu Aktualizace tokenů zprostředkovatele identity](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud předpokládáte, že už tyto prostředky nebudete potřebovat, odstraňte skupinu prostředků spuštěním následujícího příkazu ve službě Cloud Shell:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Spuštění tohoto příkazu může trvat přibližně minut.

<a name="next"></a>
## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Povolení integrovaného ověřování a autorizace
> * Zabezpečení aplikací před neověřenými požadavky
> * Použití služby Azure Active Directory jako zprostředkovatele identity
> * Přístup ke vzdálené aplikaci jménem přihlášeného uživatele
> * Zabezpečení volání mezi službami pomocí ověřování tokenu
> * Použití přístupových tokenů z kódu serveru
> * Použití přístupových tokenů z klientského kódu (v prohlížeči)

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na webovou aplikaci.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na službu Azure App Service](app-service-web-tutorial-custom-domain.md)
