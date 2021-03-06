---
title: Instalace aplikace do samostatného clusteru
description: V tomto kurzu se dozvíte, jak nainstalovat aplikaci do samostatného clusteru Service Fabric.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 124fc6035c1d0ad504a34b7db227f0f4675f24f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75613954"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Kurz: Nasazení aplikace do samostatného clusteru Service Fabric

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „jakýkoli operační systém a cloud“. V této sérii kurzů vytvoříte samostatný cluster hostovaný na AWS a nasadíte do něj aplikaci.

Tento kurz je třetí částí série.  Service Fabric samostatné clustery nabízejí možnost zvolit si vlastní prostředí a vytvořit cluster jako součást našeho přístupu "jakýkoli operační systém, jakýkoli cloud" s Service Fabric. V tomto kurzu se dozvíte, jak vytvořit infrastrukturu AWS potřebnou k hostování tohoto samostatného clusteru.

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Stažení ukázkové aplikace
> * Nasazení do clusteru

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* [Nainstalujte SiVisual Studio 2019](https://www.visualstudio.com/) a nainstalujte úlohy vývoje a **ASP.NET a vývoje webu** **azure.**
* [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové hlasovací aplikace

V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Nasazení aplikace do clusteru Service Fabric

Jakmile je aplikace stažená, můžete ji do clusteru nasadit přímo ze sady Visual Studio.

1. Otevřete sadu Visual Studio.

2. Vybrat **soubor** > **otevřít**

3. Přejděte do složky, do které jste naklonovali úložiště git, a vyberte Voting.sln.

4. V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace `Voting` a zvolte **Publikovat**.

5. Vyberte rozevírací seznam pro **Koncový bod připojení** a zadejte veřejný název DNS jednoho z uzlů v clusteru.  Například, `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Všimněte si, že v Azure se plně kvalifikovaný název domény (FQDN) neposkytuje automaticky, ale dá se snadno [nastavit na stránce Přehled virtuálních počítačích.](https://docs.microsoft.com/azure/virtual-machines/linux/portal-create-fqdn)

6. Otevřete váš preferovaný prohlížeč a zadejte adresu clusteru (koncový bod připojení, tato aplikace se nasadí na port 8080 – například ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Odpověď rozhraní API z clusteru](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Další kroky

Ve třetí části této série jste zjistili, jak nasadit aplikaci do clusteru:

> [!div class="checklist"]
> * Stažení ukázkové aplikace
> * Nasazení do clusteru

Přejděte ke čtvrté části série a vyčistěte cluster.

> [!div class="nextstepaction"]
> [Vyčištění prostředků](service-fabric-tutorial-standalone-clean-up.md)