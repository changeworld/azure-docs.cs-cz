---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 852cf2710993a450fc4eb74b5392a46203c10edc
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274873"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Vytvoření prostředku řeči Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)
> * Ujistěte se, že máte přístup k mikrofonu pro snímání zvuku

## <a name="source-code"></a>Zdrojový kód

Chcete-li do projektu Java přidat novou prázdnou**třídu,** vyberte **možnost Soubor** > **nové** > třídy . V okně **New Java Class** (Nová třída Javy) zadejte do pole **Package** (Balíček) **speechsdk.quickstart** a do pole **Name** (Název) zadejte **Main** (Hlavní).

![Snímek obrazovky s oknem New Java Class (Nová třída Javy)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

Nahraďte obsah souboru *Main.java* následujícím fragmentem:

[!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Vysvětlení kódu

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Vytváření a spouštění aplikací

Stiskněte <kbd>klávesu F11</kbd>nebo vyberte **možnost Spustit** > **ladění**.
Následujících 15 sekund hlasového vstupu z mikrofonu se rozpozná a zaprotokoluje v okně konzoly.

![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

