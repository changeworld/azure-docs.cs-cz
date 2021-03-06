---
title: Ladění webové aplikace Azure Service Fabric Mesh spuštěné místně
description: V tomto kurzu se dozvíte, jak ladit aplikaci Azure Service Fabric Mesh, která je spuštěná v místním clusteru.
author: dkkapur
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: c36d45919ae8a17026fc91f8e9040f3bb11d3eb0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75494960"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Kurz: Ladění aplikace Service Fabric Mesh spuštěné v místním clusteru pro vývoj

V tomto kurzu, který je druhou částí série, se dozvíte, jak sestavit a ladit aplikaci Azure Service Fabric Mesh v místním vývojovém clusteru.

V tomto tutoriálu se naučíte:

> [!div class="checklist"]
> * Co se stane, když sestavíte aplikaci Azure Service Fabric Mesh
> * Jak nastavit zarážky, abyste mohli sledovat volání služba-služba

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvoření aplikace Service Fabric Mesh v sadě Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Ladění aplikace Service Fabric Mesh spuštěné v místním clusteru pro vývoj
> * [Nasadit aplikaci Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Upgrade aplikace Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Vyčištění prostředků Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, můžete [si vytvořit bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

* Ujistěte se, že jste [nastavili vývojové prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md), což zahrnuje instalaci modulu runtime Service Fabric, sady SDK, Dockeru a sady Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Stažení ukázkové aplikace pro seznam úkolů

Pokud jste nevytvořili ukázkovou aplikaci pro provedení práce v [první části této série kurzů](service-fabric-mesh-tutorial-create-dotnetcore.md), můžete si ji stáhnout. V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Aplikace se nachází v adresáři `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Sestavení a ladění v místním clusteru

Po načtení projektu se image Dockeru automaticky sestaví a nasadí do místního clusteru. Tento proces může chvíli trvat. Pokud chcete monitorovat průběh v sadě Visual Studio v podokně **Výstup**, nastavte v rozevíracím seznamu **Zobrazit výstup z** položku **Nástroje Service Fabric**.

Stisknutím klávesy **F5** zkompilujete a spustíte vaši službu místně. Visual Studio při místním spuštění a ladění projektu provede tyto akce:

* Zkontroluje, jestli je Docker for Windows spuštěný a nastavený tak, aby jako operační systém kontejneru používal Windows.
* Stáhne všechny chybějící základní image Dockeru. Tato část může nějakou dobu trvat.
* Sestaví (nebo znovu sestaví) image Dockeru, která se používá k hostování projektu kódu.
* Nasadí a spustí příslušný kontejner na místním vývojovém clusteru Service Fabric.
* Spustí vaše služby a dostane se k případným zarážkám, které jste nastavili.

Až se místní nasazení dokončí a Visual Studio spustí vaši aplikaci, otevře se okno prohlížeče s výchozí ukázkovou webovou stránkou.

## <a name="debugging-tips"></a>Tipy pro ladění

Proveďte svůj první ladění spustit (F5) mnohem rychleji podle pokynů v [optimalizaci výkonu sady Visual Studio](service-fabric-mesh-howto-optimize-vs.md).

V současné době je problém, který `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` způsobí, že volání nezdaří připojení ke službě. Může k tomu dojít, kdykoli se změní IP adresa hostitele. Řešení je následující:

1. Odeberte aplikaci z místního clusteru (v sadě Visual Studio, **sestavení** > **čistého řešení).**
2. V nástroji Service Fabric Local Cluster Manager vyberte **Stop Local CLuster** (Zastavit místní cluster) a pak **Start Local Cluster** (Spustit místní cluster).
3. Znovu nasaďte aplikaci (v sadě Visual Studio stiskněte **F5**).

Pokud se zobrazí chyba se sdělením, že **žádný místní cluster Service Fabric není spuštěný**, zkontrolujte, že je spuštěný nástroj Service Fabric Local Custer Manager (LCM), klikněte pravým tlačítkem na ikonu LCM na hlavním panelu a pak klikněte na **Start Local Cluster** (Spustit místní cluster). Po jeho spuštění se vraťte do sady Visual Studio a stiskněte klávesu **F5**.

Pokud se po spuštění aplikace zobrazí chyba **404**, může to znamenat, že proměnné prostředí v souboru **service.yaml** nejsou správné. Podle pokynů pro [vytvoření proměnných prostředí](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore#create-environment-variables) zkontrolujte, jestli jsou správně nastavené proměnné `ApiHostPort` a `ToDoServiceName`.

Pokud se zobrazí chyby sestavení v **service.yaml**, zkontrolujte, že se k odsazení řádků používají mezery a ne tabulátory. Navíc je prozatím potřeba sestavit aplikaci s anglickým národním prostředím.

### <a name="debug-in-visual-studio"></a>Ladění v sadě Visual Studio

Při ladění aplikace service fabric sítě v sadě Visual Studio, používáte místní service fabric vývoj clusteru. Pokud chcete zobrazit, jak se položky úkolů načítají z back-endové služby, zaměřte se při ladění na metodu OnGet().
1. V projektu **WebFrontEnd** otevřete **stránky** > **Index.cshtml** > **Index.cshtml.cs** a nastavte zarážku v **OnGet** metoda (řádek 17).
2. V projektu **ToDoService** otevřete **TodoController.cs** a nastavte zarážku v metodě **Get** (řádek 15).
3. Vraťte se do prohlížeče a aktualizujte stránku. Dostanete se k zarážce v metodě `OnGet()` front-endu. Můžete se podívat na proměnnou `backendUrl`, abyste zjistili, jak se proměnné prostředí definované v souboru **service.yaml** zkombinují do adresy URL, která se používá ke kontaktování back-endové služby.
4. Vynechejte (F10) volání `client.GetAsync(backendUrl).GetAwaiter().GetResult())` a dostanete se k zarážce `Get()` kontroleru. V této metodě můžete vidět, jak se seznam položek úkolů načítá ze seznamu v paměti.
5. Až budete hotovi, přestaňte ladit projekt v sadě Visual Studio stisknutím **Shift+F5**.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se dozvěděli:

> [!div class="checklist"]
> * Co se stane, když sestavíte aplikaci Azure Service Fabric Mesh
> * Jak nastavit zarážky, abyste mohli sledovat volání služba-služba

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Nasadit aplikaci Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
