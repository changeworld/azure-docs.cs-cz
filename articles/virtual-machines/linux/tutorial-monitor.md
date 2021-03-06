---
title: Výuka – sledování virtuálních počítačů Linuxu v Azure
description: V tomto kurzu se dozvíte, jak sledovat výkon a zjištěné součásti aplikací spuštěné na virtuálních počítačích Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: b06342d5034b820be4e6fd49436546a5aa7b7e02
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75749796"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Kurz: Sledování virtuálního počítače s Linuxem v Azure

Azure monitoring používá agenty ke shromažďování spouštěcích a výkonnostních dat z virtuálních klíčů Azure, ukládat tato data do úložiště Azure a zpřístupnit je prostřednictvím portálu, modulu Azure PowerShell a rozhraní příkazového příkazového příkazu Azure. Pokročilé monitorování se dodává s Azure Monitor pro virtuální počítače shromažďováním metrik výkonu, zjišťováním součástí aplikací nainstalovaných na virtuálním počítači a zahrnuje grafy výkonu a mapování závislostí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolení diagnostiky spouštění na virtuálním počítači
> * Zobrazení diagnostiky spouštění
> * Zobrazení metrik hostitele virtuálního počítače
> * Povolení Azure Monitoru pro virtuální počítače
> * Zobrazení metrik výkonu virtuálních visu
> * Vytvoření upozornění

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/powershell](https://shell.azure.com/powershell)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-vm"></a>Vytvoření virtuálního počítače

Pokud chcete vidět, jak funguje diagnostika a metriky, potřebujete virtuální počítač. Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Následující příklad vytvoří skupinu prostředků *myResourceGroupMonitor* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Teď pomocí příkazu [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) vytvořte virtuální počítač. Následující příklad vytvoří virtuální počítač *myVM*, a pokud klíče SSH ještě neexistují, vytvoří je v umístění *~/.ssh/*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Povolení diagnostiky spouštění

Při spouštění virtuálních počítačů s Linuxem zaznamenává diagnostické rozšíření výstup spouštění a uloží ho v úložišti Azure. Tato data můžete použít k odstraňování problémů při spouštění virtuálních počítačů. Po vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure není diagnostika spouštění automaticky povolená.

Před povolením diagnostiky spouštění je třeba vytvořit účet úložiště pro ukládání protokolů spouštění. Účty úložiště musí mít globální jedinečný název v rozmezí 3 až 24 znaků a musí obsahovat pouze čísla a malá písmena. Účet úložiště vytvoříte příkazem [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). V tomto příkladu se k vytvoření jedinečného názvu účtu úložiště použil náhodný řetězec.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Při povolování diagnostiky spouštění je potřeba identifikátor URI pro kontejner úložiště objektů blob. Následující příkaz se dotazuje na účet úložiště za účelem vrácení tohoto identifikátoru URI. Hodnota identifikátoru URI je uložená v názvech proměnných *bloburi*, které se používají v dalším kroku.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Teď povolte diagnostiku spouštění pomocí příkazu [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). Hodnota `--storage` je identifikátor URI objektu blob získaný v předchozím kroku.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Zobrazení diagnostiky spouštění

Pokud je povolená diagnostika spouštění, zapíše se při každém spuštění a vypnutí virtuálního počítače informace o procesu spouštění do souboru protokolu. V tomto příkladu nejprve zrušte přidělení virtuálního počítače příkazem [az OM deallocate](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) takto:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Nyní spusťte virtuální počítač pomocí příkazu [az vm start](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-start) následujícím způsobem:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Data diagnostiky spouštění pro *myVM* můžete získat pomocí příkazu [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) takto:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Zobrazení metrik hostitele

Virtuální počítač s Linuxem má vyhrazeného hostitele v Azure, který s ním komunikuje. Metriky se pro hostitele shromažďují automaticky a lze je zobrazit na portálu Azure Portal následujícím způsobem:

1. Na webu Azure Portal vyberte **Skupiny prostředků**, zvolte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.
1. Pokud chcete získat informace o výkonu virtuálního počítače hostitele, vyberte v okně virtuálního počítače **Metriky** a potom v části **Dostupné metriky** vyberte některou z metrik *[hostitele]*.

    ![Zobrazení metrik hostitele](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Povolit pokročilé monitorování

Povolení monitorování virtuálního počítače Azure pomocí Azure Monitoru pro virtuální počítače:

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.

2. Na stránce Virtuální počítač včásti **Monitorování** vyberte **Přehledy (náhled).**

3. Na stránce **Přehledy (náhled)** vyberte **Vyzkoušet .**

    ![Povolení Azure Monitoru pro virtuální počítače pro virtuální počítač](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Na stránce **Registrace přehledů monitorování Azure,** pokud máte existující pracovní prostor Log Analytics ve stejném předplatném, vyberte ho v rozevíracím seznamu.  

    Seznam předem vybere výchozí pracovní prostor a umístění, kde se virtuální jazyk nasadí v předplatném. 

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Analýzy protokolů pro ukládání dat monitorování z virtuálního aplikace, přečtěte [si tématu Vytvoření pracovního prostoru Analýzy protokolů](../../azure-monitor/learn/quick-create-workspace.md). Pracovní prostor Log Analytics musí patřit do jedné z [podporovaných oblastí](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Po povolení monitorování možná budete muset počkat několik minut, než budete moct zobrazit metriky výkonu pro virtuální hod.

![Povolení zpracování nasazení monitorování Azure Monitor pro virtuální počítače](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Zobrazení metrik výkonu virtuálních visu

Azure Monitor pro virtuální počítače obsahuje sadu grafů výkonu, které cílí na několik klíčových ukazatelů výkonu (KU), které vám pomohou určit, jak dobře si virtuální počítač vede. Chcete-li získat přístup z virtuálního počítače, proveďte následující kroky.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.

2. Na stránce Virtuální počítač včásti **Monitorování** vyberte **Přehledy (náhled).**

3. Vyberte kartu **Výkon.**

Tato stránka obsahuje nejen grafy využití výkonu, ale také tabulku zobrazující každý zjištěný logický disk, jeho kapacitu, využití a celkový průměr podle jednotlivých měr.

## <a name="create-alerts"></a>Vytváření upozornění

Na základě konkrétních metrik výkonu můžete vytvořit výstrahy. Výstrahy lze například použít k upozornění, že průměrné využití procesoru překračuje prahovou hodnotu nebo že volné místo na disku kleslo pod určitou velikost. Výstrahy ze zobrazují v portálu Azure Portal nebo je lze odeslat e-mailem. V reakci na vygenerované výstrahy můžete také aktivovat runbooky Azure Automation nebo Azure Logic Apps.

Následující příklad vytvoří výstrahu týkající se průměrného využití procesoru.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.

2. Klikněte na tlačítko **Pravidla výstrah** v okně virtuálního počítače a potom na **Přidat upozornění metriky** v horní části okna výstrahy.

3. Zadejte **Název** výstrahy, například *mojePravidloVystrahy*.

4. Pokud chcete spustit výstrahu, pokud procento využití procesoru překročí hodnotu 1,0 po dobu pěti minut, ponechte výchozí výběr všech ostatních nastavení.

5. Volitelně můžete zaškrtnutím políčka *Vlastníci, přispěvatelé a čtenáři e-mailů* odesílat oznámení e-mailem. Výchozí akce je zobrazení oznámení na portálu.

6. Klikněte na tlačítko **OK.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a zobrazili výkon virtuálního počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků a virtuálního počítače
> * Povolení diagnostiky spouštění ve virtuálním počítači
> * Zobrazení diagnostiky spouštění
> * Zobrazení metrik hostitele
> * Povolení Azure Monitoru pro virtuální počítače
> * Zobrazení metrik virtuálního počítače
> * Vytvoření upozornění

V dalším kurzu se dozvíte něco o Azure Security Center.

> [!div class="nextstepaction"]
> [Správa zabezpečení virtuálního počítače](../../security/fundamentals/overview.md)
