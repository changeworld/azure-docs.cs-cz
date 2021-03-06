---
title: Přehled Jenkinse a Azure
description: Hostujte server pro automatizaci sestavení a nasazení Jenkinse v Azure a použijte výpočetní prostředky a prostředky úložiště Azure k rozšíření vašich kanálů průběžné integrace a nasazení (CI/CD).
keywords: jenkins, azure, devops, overview
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: a9297ebc116d75cfe1d4f37d4e9ada7d5198beae
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77620171"
---
# <a name="azure-and-jenkins"></a>Azure a Jenkins

[Jenkins](https://jenkins.io/) je oblíbený open-source automatizační server, který se používá k nastavení průběžné integrace a doručování (CI/CD) pro softwarové projekty. Nasazení Jenkinse můžete hostovat v Azure nebo můžete rozšířit stávající konfiguraci Jenkinse s využitím prostředků Azure. K dispozici jsou také moduly plug-in Jenkinse pro zjednodušení průběžné integrace a doručování (CI/CD) vašich aplikací do Azure.

Tento článek je úvodem do používání Azure s Jenkinsem a podrobně popisuje základní funkce Azure dostupné pro uživatele Jenkinse. Další informace o začátcích s vlastním serverem Jenkinse v Azure najdete v tématu [Vytvoření serveru Jenkinse v Azure](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Hostování serverů Jenkinse v Azure

Díky hostování Jenkinse v Azure můžete centralizovat automatizaci sestavování a škálovat své nasazení s ohledem na rostoucí požadavky vašich softwarových projektů. K nasazení Jenkinse v Azure můžete použít:
 
- [Šablonu řešení Jenkinse](install-jenkins-solution-template.md) na webu Azure Marketplace.
- [Virtuální počítače Azure](/azure/virtual-machines/linux/overview). Informace o vytvoření instance Jenkinse na virtuálním počítači najdete v našem [kurzu](tutorial-jenkins-github-docker-cicd.md).
- Pokud používáte cluster Kubernetes spuštěný ve službě [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), přečtěte si naše [postupy](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Monitorujte a spravujte nasazení Azure Jenkinse pomocí [protokolů Azure Monitor](/azure/log-analytics/log-analytics-overview) a [azure cli](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Škálování automatizace sestavování na vyžádání

Přidáním agentů sestavení do stávajícího nasazení Jenkinse můžete škálovat kapacitu sestavování Jenkinse s ohledem na rostoucí počet sestavení a složitost úloh a kanálů. Tyto agenty sestavení můžete spustit na virtuálních počítačích Azure pomocí [modulu plug-in Agenti virtuálních počítačů Azure](https://plugins.jenkins.io/azure-vm-agents). Další podrobnosti najdete v našem [kurzu](/azure/jenkins/jenkins-azure-vm-agents).

Po nakonfigurování [instančního objektu Azure](/azure/azure-resource-manager/resource-group-overview) můžou úlohy a kanály Jenkinse využívat tyto přihlašovací údaje k následujícím úlohám:

- Bezpečně uklápěte a archivujte artefakty sestavení ve [službě Azure Storage](/azure/storage/common/storage-introduction) pomocí [modulu plug-in Azure Storage](https://plugins.jenkins.io/windows-azure-storage). Další informace najdete v [postupech pro úložiště Jenkinse](storage-java-jenkins-continuous-integration-solution.md).
- Správa a konfigurace prostředků Azure pomocí [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Nasazení vlastního kódu do služeb Azure

Pomocí modulů plug-in Jenkinse můžete nasazovat aplikace do Azure v rámci kanálů CI/CD Jenkinse. Nasazení do služby [Azure App Service](/azure/app-service/) a [Azure Container Service](/azure/container-service/kubernetes/) vám umožní připravovat, testovat a vydávat aktualizace vašich aplikací bez nutnosti správy základní infrastruktury.

 Moduly plug-in je možné nasadit do následujících služeb a prostředí:

- [Služba Azure App Service na Linuxu](/azure/app-service/containers/app-service-linux-intro). Začněte prostudováním tohoto [kurzu](java-deploy-webapp-tutorial.md).
- [Služba Azure App Service](/azure/app-service/overview). Začněte prostudováním těchto [postupů](deploy-Jenkins-app-service-plugin.md).
