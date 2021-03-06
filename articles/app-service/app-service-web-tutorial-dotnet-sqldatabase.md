---
title: 'Kurz: ASP.NET aplikace s databází SQL'
description: Zjistěte, jak nasadit aplikaci C# ASP.NET s databází SQL Serveru do Azure.
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 06/25/2018
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: a9acb55f0a03a6ec1ba0bb6bb38c665b059b672b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80047028"
---
# <a name="tutorial-build-an-aspnet-app-in-azure-with-sql-database"></a>Kurz: Vytvoření aplikace ASP.NET se službou SQL Database v Azure

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. Tento kurz ukazuje, jak nasadit aplikaci ASP.NET založené na datech ve službě App Service a připojit ji k [Azure SQL Database](../sql-database/sql-database-technical-overview.md). Po dokončení máte ASP.NET aplikace spuštěná v Azure a připojená k DATABÁZI SQL.

![Publikovaná ASP.NET aplikace ve službě Azure App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi SQL v Azure
> * Připojit aplikaci ASP.NET ke službě SQL Database
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat protokoly z Azure do terminálu
> * Spravovat aplikaci na webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

Nainstalujte <a href="https://www.visualstudio.com/downloads/" target="_blank">Sivisual Studio 2019</a> s **ASP.NET a zatížením vývoje webu.**

Pokud jste visual studio už nainstalovali, přidejte úlohy v Sadě Visual Studio kliknutím na **Nástroje** > **získat nástroje a funkce**.

## <a name="download-the-sample"></a>Stažení ukázky

- [Stáhněte si ukázkový projekt](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).
- Extrahujte (rozbalte) soubor *dotnet-sqldb-tutorial-master.zip.*

Ukázkový projekt obsahuje základní aplikaci CRUD (vytváření-čtení-aktualizace-odstraňování) v [ASP.NET MVC](https://www.asp.net/mvc) používající [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>Spuštění aplikace

Otevřete soubor *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* v sadě Visual Studio. 

Zadáním `Ctrl+F5` spusťte aplikaci bez zapnutého ladění. Aplikace se zobrazí ve vašem výchozím prohlížeči. Vyberte odkaz **Vytvořit nový** a vytvořte několik položek *úkolů*. 

![Dialogové okno Nový projekt ASP.NET](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.

Aplikace pro připojení k databázi používá kontext databáze. V této ukázce kontext databáze používá připojovací řetězec `MyDbConnection`. Připojovací řetězec je nastavený v souboru *Web.config* a odkazuje se na něj v souboru *Models/MyDatabaseContext.cs*. Název připojovacího řetězce se používá později v kurzu k připojení aplikace Azure k Azure SQL Database. 

## <a name="publish-to-azure-with-sql-database"></a>Publikování do Azure pomocí služby SQL Database

V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt **DotNetAppSqlDb** a vyberte **Publikovat**.

![Publikování z Průzkumníka řešení](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Zkontrolujte, že je vybraná možnost **Microsoft Azure App Service** a klikněte na **Publikovat**.

![Publikování ze stránky přehledu projektu](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

Publikování otevře dialogové okno **Vytvořit službu appservice,** které vám pomůže vytvořit všechny prostředky Azure, které potřebujete ke spuštění ASP.NET aplikace v Azure.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

V dialogovém okně **Vytvoření služby App Service** klikněte na **Přidat účet** a přihlaste se ke svému předplatnému Azure. Pokud jste již přihlášení k účtu Microsoft, ujistěte se, že odpovídá vašemu předplatnému Azure. Pokud jste přihlášeni k účtu Microsoft, který nemá přiřazené předplatné Azure, kliknutím na něj přidejte správný účet. 

> [!NOTE]
> Pokud už jste přihlášení, nevybírejte zatím možnost **Vytvořit**.
>
>
   
![Přihlášení k Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

### <a name="configure-the-web-app-name"></a>Konfigurace názvu webové aplikace

Můžete ponechat vygenerovaný název webové aplikace nebo ho můžete změnit na jiný jedinečný název (platné znaky jsou `a-z`, `0-9` a `-`). Název webové aplikace se používá jako součást výchozí adresy URL vaší aplikace (`<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší webové aplikace). Název webové aplikace musí být jedinečný mezi všemi aplikacemi v Azure. 

![Dialogové okno Vytvoření služby App Service](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [resource-group](../../includes/resource-group.md)]

Vedle pole **Skupina prostředků** klikněte na tlačítko **Nová**.

![Vedle pole Skupina prostředků klikněte na Nová.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

Pojmenujte skupinu prostředků **myResourceGroup**.

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Vedle pole **Plán služby App Service** klikněte na **Nový**. 

V dialogovém okně **Konfigurace plánu služby App Service** nastavte nový plán takto:

![Vytvoření plánu služby App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

| Nastavení  | Navrhovaná hodnota | Další informace |
| ----------------- | ------------ | ----|
|**Plán služeb aplikace**| myAppServicePlan | [Plány služby App Service](../app-service/overview-hosting-plans.md) |
|**Umístění**| Západní Evropa | [Oblast Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
|**Velikost**| Free | [Cenové úrovně](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

### <a name="create-a-sql-server-instance"></a>Vytvoření instance SQL Serveru

Před vytvořením databáze potřebujete [logický server Azure SQL Database](../sql-database/sql-database-features.md). Logický server obsahuje soubor databází spravovaných jako skupina.

Klikněte na **Vytvořit službu SQL Database**.

![Vytvoření databáze SQL](media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

V dialogovém okně **Konfigurace služby SQL Database** klikněte na **Nový** vedle **SQL Server**. 

Vygeneruje se jedinečný název serveru. Tento název se použije jako součást výchozí adresy URL vašeho logického serveru – `<server_name>.database.windows.net`. Musí být jedinečný mezi všemi instancemi logických serverů v Azure. Název serveru můžete změnit, ale pro účely tohoto kurzu ponechte vygenerovanou hodnotu.

Přidejte uživatelské jméno a heslo správce. Požadavky na složitost hesla najdete v tématu [Zásady hesel](/sql/relational-databases/security/password-policy).

Toto uživatelské jméno a heslo si zapamatujte. Později je budete potřebovat ke správě instance logického serveru.

> [!IMPORTANT]
> Přestože je vaše heslo v připojovacích řetězcích maskované (v sadě Visual Studio i ve službě App Service), skutečnost, že se někde uchovává, rozšiřuje prostor pro útok na vaši aplikace. App Service může toto riziko odstranit pomocí [identit spravovaných služeb](overview-managed-identity.md), které úplně odstraňují potřebu uchovávat tajné klíče v kódu nebo konfiguraci aplikace. Další informace najdete v části [Další kroky](#next-steps).

![Vytvoření instance SQL Serveru](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

Klikněte na tlačítko **OK**. Dialogové okno **Konfigurace služby SQL Database** ještě nezavírejte.

### <a name="create-a-sql-database"></a>Vytvoření databáze SQL

V dialogovém okně **Konfigurace služby SQL Database**: 

* Ponechte výchozí vygenerovaný **Název databáze**.
* Do pole **Název připojovacího řetězce** zadejte *MyDbConnection*. Tento název se musí shodovat s připojovacím řetězcem, na který se odkazuje v souboru *Models/MyDatabaseContext.cs*.
* Vyberte **OK**.

![Konfigurace služby SQL Database](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

V dialogovém okně **Vytvoření služby App Service** se zobrazí prostředky, které jste nakonfigurovali. Klikněte na **Vytvořit**. 

![prostředky, které jste vytvořili](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)

Jakmile průvodce dokončí vytváření prostředků Azure, publikuje vaši aplikaci ASP.NET do Azure. Spustí se váš výchozí prohlížeč na adrese URL nasazené aplikace. 

Přidejte několik položek úkolů.

![Publikovaná aplikace ASP.NET v aplikaci Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Blahopřejeme! Vaše aplikace ASP.NET řízená daty je spuštěná ve službě Azure App Service.

## <a name="access-the-sql-database-locally"></a>Místní přístup ke službě SQL Database

Sada Visual Studio umožňuje snadno zkoumat a spravovat vaši novou službu SQL Database v **Průzkumníku objektů systému SQL Server**.

### <a name="create-a-database-connection"></a>Vytvoření připojení k databázi

V nabídce **Zobrazení** vyberte **Průzkumník objektů systému SQL Server**.

V horní části **Průzkumníka objektů systému SQL Server** klikněte na tlačítko **Přidat SQL Server**.

### <a name="configure-the-database-connection"></a>Konfigurace připojení k databázi

V dialogovém okně **Připojení** rozbalte uzel **Azure**. Tady jsou uvedené všechny vaše instance služby SQL Database v Azure.

Vyberte službu SQL Database, kterou jste vytvořili dříve. V dolní části se automaticky vyplní připojení, které jste vytvořili.

Zadejte heslo správce databáze, které jste vytvořili dříve, a klikněte na **Připojit**.

![Konfigurace připojení k databázi ze sady Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>Povolení klientských připojení z vašeho počítače

Otevře se dialogové okno **Vytvoření nového pravidla brány firewall**. Ve výchozím nastavení instanci databáze SQL umožňuje jenom připojení ze služeb Azure, jako je vaše aplikace Azure. Abyste se mohli připojit k databázi, vytvořte v instanci služby SQL Database pravidlo brány firewall. Toto pravidlo brány firewall povolí veřejnou IP adresu vašeho místního počítače.

V dialogovém okně je veřejná IP adresa vašeho počítače již vyplněná.

Ujistěte se, že je vybraná možnost **Přidat IP adresu mého klienta**, a klikněte na **OK**. 

![Nastavení brány firewall pro instanci služby SQL Database](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Jakmile sada Visual Studio dokončí vytváření nastavení brány firewall pro vaši instanci SQL Serveru, vaše připojení se zobrazí v **Průzkumníku objektů systému SQL Server**.

Tady můžete provádět nejběžnější databázové operace, jako je spouštění dotazů, vytváření zobrazení a uložených procedur a další. 

Rozšiřte připojení > **databáze** > **&lt;databáze>**  >  **tabulky**. Klikněte pravým tlačítkem na tabulku `Todoes` a vyberte **Zobrazit data**. 

![Zkoumání objektů služby SQL Database](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Aktualizace aplikace pomocí migrace Code First

Pomocí známých nástrojů ve Visual Studiu můžete aktualizovat databázi a aplikaci v Azure. V tomto kroku pomocí migrace Code First v rozhraní Entity Framework provedete změnu schématu databáze a publikujete ji do Azure.

Další informace o použití migrace Entity Framework Code First najdete v tématu [Začínáme s migrací Entity Framework 6 Code First s použitím MVC 5](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="update-your-data-model"></a>Aktualizace datového modelu

Otevřete _Models\Todo.cs_ v editoru kódu. Do třídy `ToDo` přidejte následující vlastnost:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Místní spuštění migrace Code First

Spuštěním několika příkazů aktualizujte místní databázi. 

V nabídce **Nástroje** klikněte na **Správce balíčků NuGet** > **Konzola správce balíčků**.

V okně konzoly správce balíčků povolte migraci Code First:

```powershell
Enable-Migrations
```

Přidejte migraci:

```powershell
Add-Migration AddProperty
```

Aktualizujte místní databázi:

```powershell
Update-Database
```

Zadáním `Ctrl+F5` spusťte aplikaci. Otestujte odkazy Upravit, Podrobnosti a Vytvořit.

Pokud se aplikace načte bez chyb, migrace Code First proběhla úspěšně. Vaše stránka však vypadá stále stejně, protože logika vaší aplikace tuto novou vlastnost ještě nepoužívá. 

### <a name="use-the-new-property"></a>Použití nové vlastnosti

Proveďte v kódu několik změn, aby aplikace využívala vlastnost `Done`. Pro zjednodušení budete v tomto kurzu měnit jen zobrazení `Index` a `Create`, aby se vlastnost projevila v praxi.

Otevřete _Controllers\TodosController.cs_.

Vyhledejte metodu `Create()` na řádku 52 a přidejte vlastnost `Done` do seznamu vlastností v atributu `Bind`. Po dokončení bude podpis vaší metody `Create()` vypadat podobně jako následující kód:

```csharp
public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
```

Otevřete _Views\Todos\Create.cshtml_.

V kódu Razor byste měli vidět prvek `<div class="form-group">`, který používá `model.Description`, a pak další prvek `<div class="form-group">`, který používá `model.CreatedDate`. Přímo za tyto dva prvky přidejte další prvek `<div class="form-group">`, který používá `model.Done`:

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

Otevřete _Views\Todos\Index.cshtml_.

Vyhledejte prázdný prvek `<th></th>`. Přímo nad tento prvek přidejte následující kód Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Vyhledejte prvek `<td>`, který obsahuje pomocné metody `Html.ActionLink()`. _Nad_ tento prvek `<td>` přidejte další prvek `<td>` s následujícím kódem Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

To je všechno, co potřebujete, aby se v zobrazení `Index` a `Create` projevily změny. 

Zadáním `Ctrl+F5` spusťte aplikaci.

Teď můžete přidat položku úkolu a zaškrtnout možnost **Hotovo**. Položka by se měla zobrazit na domovské stránce jako dokončená. Nezapomeňte, že v zobrazení `Edit` se pole `Done` nezobrazí, protože jste zobrazení `Edit` nezměnili.

### <a name="enable-code-first-migrations-in-azure"></a>Povolení migrace Code First v Azure

Teď, když vaše změna kódu funguje, včetně migrace databáze, publikujete ji do aplikace Azure a aktualizujete databázi SQL pomocí migrace Code First.

Stejně jako předtím klikněte pravým tlačítkem na svůj projekt a vyberte **Publikovat**.

Kliknutím na **Konfigurovat** otevřete nastavení publikování.

![Otevřené nastavení publikování](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

V průvodci klepněte na tlačítko **Další**.

Ujistěte se, že v části **MyDatabaseContext (MyDbConnection)** je vyplněný připojovací řetězec pro vaši službu SQL Database. Možná budete muset vybrat databázi **myToDoAppDb** z rozevíracího seznamu. 

Vyberte **Spustit migraci Code First (spustí se při spuštění aplikace)** a pak klikněte na **Uložit**.

![Povolení migrace prvního kódu v aplikaci Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>Publikování provedených změn

Teď, když jste povolili migrace code first v aplikaci Azure, publikujte změny kódu.

Na stránce publikování klikněte na **Publikovat**.

Zkuste znovu přidat položky úkolů a vyberte **Done** (Hotovo). Měly by se zobrazit na vaší domovské stránce jako dokončené položky.

![Aplikace Azure po první migraci kódu](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Všechny vaše existující položky úkolů jsou nadále zobrazené. Při opětovném publikování aplikace ASP.NET nedojde ke ztrátě existujících dat ve službě SQL Database. Migrace Code First také změní pouze schéma dat, ale existující data ponechá beze změny.


## <a name="stream-application-logs"></a>Streamování protokolů aplikací

Trasování zpráv můžete streamovat přímo z aplikace Azure do Visual Studia.

Otevřete _Controllers\TodosController.cs_.

Každá akce začíná metodou `Trace.WriteLine()`. Tento kód se přidá, aby vám ukázal, jak přidat trasovací zprávy do vaší aplikace Azure.

### <a name="open-server-explorer"></a>Otevření Průzkumníka serveru

V nabídce **Zobrazení** vyberte **Průzkumník serveru**. Protokolování pro aplikaci Azure můžete nakonfigurovat v **Průzkumníkovi serveru**. 

### <a name="enable-log-streaming"></a>Povolení streamování protokolů

V **Průzkumníku serveru** rozbalte **Azure** > **App Service**.

Rozbalte skupinu prostředků **myResourceGroup,** kterou jste vytvořili při prvním vytvoření aplikace Azure.

Klikněte pravým tlačítkem myši na aplikaci Azure a vyberte **Zobrazit protokoly streamování**.

![Povolení streamování protokolů](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

Protokoly se teď streamují do okna **Výstup**. 

![Streamování protokolů v okně Výstup](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

Zatím se však nezobrazují žádné zprávy trasování. Je to proto, že když poprvé vyberete **Zobrazit protokoly streamování**, vaše aplikace Azure nastaví úroveň trasování na `Error`, která pouze zaznamenává chybové události (s `Trace.TraceError()` metodou).

### <a name="change-trace-levels"></a>Změna úrovní trasování

Pokud chcete změnit úrovně trasování, aby se na výstupu zobrazily další zprávy trasování, vraťte se do **Průzkumníka serveru**.

Znovu klikněte pravým tlačítkem myši na aplikaci Azure a vyberte **Zobrazit nastavení**.

V rozevíracím seznamu **Protokolování aplikace (Systém souborů)** vyberte **Podrobné**. Klikněte na **Uložit**.

![Změna úrovně trasování na Podrobné](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> Můžete experimentovat s různými úrovněmi trasování, abyste viděli, jaké typy zpráv se zobrazí pro jednotlivé úrovně. Úroveň **Informace** například zahrnuje všechny protokoly `Trace.TraceWarning()`vytvořené `Trace.TraceError()`protokoly `Trace.TraceInformation()`, a `Trace.WriteLine()`, ale ne protokoly vytvořené společností .
>
>

V prohlížeči znovu přejděte do aplikace http:// *&lt;název aplikace>.azurewebsites.net*, zkuste v Azure kliknout na aplikaci seznamu úkolů. Zprávy trasování se teď streamují do okna **Výstup** v sadě Visual Studio.

```console
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```



### <a name="stop-log-streaming"></a>Zastavení streamování protokolů

Pokud chcete zastavit službu streamování protokolů, klikněte na tlačítko **Zastavit monitorování** v okně **Výstup**.

![Zastavení streamování protokolů](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Správa aplikace Azure

Pokud chcete webovou aplikaci spravovat, přejděte na web [Azure Portal](https://portal.azure.com). Vyhledejte a vyberte **služby App Services**. 

![Hledání azure app služeb](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Vyberte název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Přistáli jste na stránce aplikace. 

Ve výchozím nastavení se na portálu zobrazí stránka **Přehled**. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete také provést základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít. 

![Stránka služby App Service na webu Azure Portal](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit databázi SQL v Azure
> * Připojit aplikaci ASP.NET ke službě SQL Database
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat protokoly z Azure do terminálu
> * Spravovat aplikaci na webu Azure Portal

V dalším kurzu se dozvíte, jak snadno zlepšit zabezpečení připojení ke službě Azure SQL Database.

> [!div class="nextstepaction"]
> [Zabezpečený přístup ke službě SQL Database s využitím spravovaných identit pro prostředky Azure](app-service-web-tutorial-connect-msi.md)
