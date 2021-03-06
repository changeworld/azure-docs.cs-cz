---
title: Přístup k testovacímu prostředí v Azure DevTest Labs | Microsoft Docs
description: V tomto kurzu budete přistupovat k testovacímu prostředí vytvořenému pomocí služby Azure DevTest Labs, nárokovat virtuální počítače, používat je a potom je vracet.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/18/2019
ms.author: spelluru
ms.openlocfilehash: ee9a68df685095244fc9471b7d4ab0f6cee0642d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68360264"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Kurz: Přístup k testovacímu prostředí v Azure DevTest Labs
V tomto kurzu použijete testovací prostředí vytvořené v tématu [Kurz: Vytvoření testovacího prostředí v Azure DevTest Labs](tutorial-create-custom-lab.md).

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Nárokování virtuálního počítače v testovacím prostředí
> * Připojení k virtuálnímu počítači
> * Vrácení virtuálního počítače

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="access-the-lab"></a>Přístup k testovacímu prostředí

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce vlevo vyberte **Všechny prostředky**. 
3. Jako typ prostředku vyberte **DevTest Labs**. 
4. Vyberte testovací prostředí. 

    ![Výběr testovacího prostředí](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Nárokování virtuálního počítače

1. V seznamu **Nárokovatelné virtuální počítače** vyberte **... ** (tři tečky) a vyberte **Nárokovat počítač**.

    ![Nárokování virtuálního počítače](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Zkontrolujte, jestli virtuální počítač vidíte v seznamu **Moje virtuální počítače**.

    ![Můj virtuální počítač](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

1. V seznamu vyberte svůj virtuální počítač. Zobrazí se **stránka virtuálního počítače** pro váš virtuální počítač. Na panelu nástrojů vyberte **Připojit**.

    ![Připojení k virtuálnímu počítači](./media/tutorial-use-custom-lab/connect-button.png)
2. Stažený soubor **RDP** uložte na pevný disk a použijte ho pro připojení k virtuálnímu počítači. Zadejte uživatelské jméno a heslo, které jste uvedli při vytváření virtuálního počítače v předchozí části. 

    Pokud se chcete připojit k virtuálnímu počítači s Linuxem, musí pro něj být povolený přístup přes SSH nebo protokol RDP. Postup připojení k virtuálnímu počítači s Linuxem přes protokol RDP najdete v tématu [Instalace a konfigurace Vzdálené plochy pro připojení k virtuálnímu počítači s Linuxem v Azure](../virtual-machines/linux/use-remote-desktop.md). 

    > [!NOTE]
    > Existují i jiné způsoby, jak se dostat na stránku virtuálního počítače pro váš virtuální počítač. Zde jsou některé z nich: 
    > 
    > 1. Vyhledejte všechny virtuální počítače ve vašem předplatném. Vyberte virtuální počítač v seznamu virtuálních počítačů, abyste se dostali na stránku **Virtuální počítač.**
    > 2. Přejděte na stránku **Skupina prostředků** pro skupinu prostředků. Potom vyberte virtuální počítač ze seznamu prostředků ve skupině prostředků, abyste se dostali na stránku **Virtuální počítač.** 
    >
    > Nepoužívejte tlačítko **Připojit** na panelu nástrojů na stránce **Virtuální počítač,** ke kterému se dostanete pomocí těchto možností. Místo toho přejděte na stránku **Virtuální počítač** ze stránky **DevTest Labs,** jak je znázorněno v tomto článku, a pak použijte tlačítko **Připojit** na panelu nástrojů.


## <a name="unclaim-the-vm"></a>Vrácení virtuálního počítače
Jakmile virtuální počítač přestanete používat, vraťte ho pomocí následujícího postupu: 

1. Na stránce virtuálního počítače vyberte na panelu nástrojů možnost **Vrátit**. 

    ![Vrácení virtuálního počítače](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. Virtuální počítač se před vrácením vypne. Stav této operace můžete zobrazit v oznámeních.  
3. Přejděte zpět na stránku DevTest Lab kliknutím na název testovacího prostředí v nabídce s popisem cesty v horní části. 
    
    ![Přejděte zpět do testovacího prostředí](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. Zkontrolujte, že se virtuální počítač zobrazuje v seznamu **claimable virtuálních počítačů** seznamu v dolní části.

    
## <a name="next-steps"></a>Další kroky
Tento kurz vám ukázal, jak přistupovat k testovacímu prostředí vytvořenému pomocí služby Azure DevTest Labs a jak ho používat. Další informace o přístupu k virtuálním počítačům a o jejich používání v testovacím prostředí najdete v tématu 

> [!div class="nextstepaction"]
> [Postupy: Použití virtuálních počítačů v testovacím prostředí](devtest-lab-add-vm.md)

