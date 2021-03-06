---
title: 'Úvodní příručka: Vytvoření kanálu CI/CD pro Jazyk Java – projekty Azure DevOps'
description: DevOps Projekty usnadňuje začínáme v Azure. Pomůže vám v několika rychlých krocích spustit aplikaci v libovolné službě Azure.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
monikerRange: vsts
ms.openlocfilehash: 1a276770887bee39972ba8630fb13f52bcbe802d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77049952"
---
# <a name="quickstart-set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-projects"></a>Úvodní příručka: Nastavení kanálu CI/CD pro aplikaci Java s projekty Azure DevOps

V tomto rychlém startu použijete zjednodušené prostředí Azure DevOps Projects k nastavení kanálu průběžné integrace (CI) a průběžného doručování (CD) pro vaši aplikaci Java v Azure Pipelines. Pomocí projektů Azure DevOps můžete nastavit vše, co potřebujete pro vývoj, nasazení a monitorování vaší aplikace. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Účet a organizace [Azure DevOps.](https://azure.microsoft.com/services/devops/)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

DevOps Projekty vytvoří kanál CI/CD v Azure pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. DevOps Projects taky vytvoří prostředky Azure v předplatném Azure dle vašeho výběru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a v levém podokně vyberte **Vytvořit prostředek**. 

   ![Vytvoření prostředku Azure na webu Azure Portal](_img/azure-devops-project-java/continuous-delivery-configuration-full-browser.png)

1. Vyhledejte a vyberte **DevOps Projects**a pak vyberte **Vytvořit**.

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci v Javě.  
Ukázka v Javě zahrnuje výběr několika architektur aplikace.

1. Výchozí architektura ukázky je Spring. Ponechte výchozí nastavení a pak vyberte **Další**.  Výchozí cíl nasazení je Web App for Containers. Rozhraní aplikace, které jste zvolili dříve, určuje typ cíle nasazení služby Azure, který je k dispozici. 

2. Ponechte výchozí službu a pak vyberte **Další**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure 

1. Vytvořte novou organizaci služby Azure DevOps nebo zvolte některou existující organizaci. 
   
   1. Zvolte název projektu. 
   
   1. Vyberte předplatné a umístění Azure, vyberte název aplikace a pak vyberte **Hotovo**.  
   Po několika minutách se na webu Azure Portal zobrazí řídicí panel DevOps Projects. Ukázková aplikace se nastaví v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do Azure. Tento řídicí panel poskytuje přehled o úložišti kódu, kanálu CI/CD a vaší aplikaci v Azure.
   
2. Chcete-li zobrazit spuštěnou aplikaci, vyberte **možnost Procházet.**
   
   ![Zobrazení řídicího panelu aplikace na webu Azure Portal](_img/azure-devops-project-java/azure-devops-application-dashboard.png) 

DevOps Projekty automaticky nakonfiguroval ci sestavení a vydání aktivační události.  Teď jste připraveni při práci na aplikaci v Javě spolupracovat s týmem s využitím procesu CI/CD, který automaticky nasazuje nejnovější práci na web.

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

DevOps Projects vytvoří úložiště Git v Azure Repos nebo GitHub. Chcete-li zobrazit úložiště a provést změny kódu v aplikaci, postupujte takto:

1. Na levé straně řídicího panelu Projekty DevOps vyberte odkaz pro hlavní větev.  
Tento odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Chcete-li zobrazit adresu URL klonování úložiště, vyberte **klonovat** v pravém horním rohu prohlížeče.   
    Úložiště Git můžete naklonovat do svého oblíbeného integrovaného vývojového prostředí (IDE). V dalších několika krocích můžete k provedení změn kódu a jejich potvrzení přímo do hlavní větve použít webový prohlížeč.

1. Na levé straně prohlížeče přejděte do **souboru src/main/webapp/index.html.**

1. Vyberte **Upravit**a pak proveďte změnu části textu.
    Změňte například text v některé ze značek div.

1. Vyberte **Potvrdit**a uložte změny.

1. V prohlížeči přejděte na řídicí panel Projekty DevOps.   
Nyní byste měli vidět probíhající sestavení. Právě provedené změny jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Zkontrolujte kanál CI/CD

 V předchozím kroku projekty DevOps automaticky nakonfigurovaly úplný kanál CI/CD. Prozkoumejte kanál a podle potřeby ho upravte. Postupujte podle následujících kroků, abyste se seznámili s kanály sestavení a vydání.

1. V horní části řídicího panelu Projekty DevOps vyberte **Sestavit kanály**.  
Tento odkaz otevře kartu prohlížeče a kanál sestavení pro váš nový projekt.

1. Přejděte na pole **Stav** a vyberte tři tečky (...).  
    Tato akce otevře nabídku, kde můžete spustit několik aktivit, jako je například řazení do fronty nové sestavení, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úkoly pro kanál sestavení.  
Sestavení provádí různé úkoly, jako je načítání zdrojů z úložiště Git, obnovení závislostí a publikování výstupů, které se používají pro nasazení.

1. V horní části kanálu buildu, vyberte název kanálu buildu.

1. Změňte název kanálu sestavení na něco popisnějšího, vyberte **Uložit & fronty**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.   
V podokně **Historie** se zobrazí záznam auditu posledních změn pro sestavení.  Azure Pipelines sleduje všechny změny, které jsou provedeny v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **možnost Aktivační události**.   
 DevOps Projekty automaticky vytvořili aktivační událost CI a každé potvrzení do úložiště spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.   
V závislosti na scénáři můžete určit zásady zachovat nebo odebrat určitý počet sestavení.

1. Vyberte **Sestavit a uvolnit**a pak vyberte **Verze**.  
 DevOps Projects vytvoří kanál vydání pro správu nasazení do Azure.

1. Vlevo vyberte tři tečky (...) vedle kanálu vydání a pak vyberte **Upravit**.  
Kanál verze obsahuje kanál, který definuje proces vydání.  
    
12. V části **Artefakty** vyberte **Zahodit**.  
Kanál sestavení, který jste zkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Vedle ikony **Přetažení** vyberte **aktivační událost průběžného nasazení**.  
Tento kanál vydání má povolenou aktivační událost CD, která spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžadují ruční spuštění. 

1. Vlevo vyberte **Úkoly**.   
Úkoly jsou aktivity, které provádí proces nasazení. V tomto příkladu byla vytvořena úloha pro nasazení do služby Azure App Service.

1. Na pravé straně vyberte **Zobrazit vydání**.  
Toto zobrazení ukazuje historii vydaných verzí.

1. Vyberte tři tečky (...) vedle jedné z vašich verzí a pak vyberte **Otevřít**.  
Existuje několik nabídek k prozkoumání, jako je například souhrn vydání, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.   
Toto zobrazení zobrazuje potvrzení kódu, které jsou přidruženy k určitému nasazení. 

1. Vyberte **Protokoly**.  
Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po nich.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure App Service a další související prostředky můžete odstranit, když už je nepotřebujete. Funkce **Odstranit** na řídicím panelu Projekty devOps.

## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD byly automaticky vytvořeny kanály sestavení a vydání. Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Další informace o kanálu CI/CD najdete v následujících tématech:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
