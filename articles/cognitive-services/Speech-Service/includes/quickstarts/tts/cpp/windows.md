---
title: 'Úvodní příručka: Syntetizovat řeč, C++ (Windows) - služba řeči'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak syntetizovat řeč v jazyce C++ v systému Windows Desktop pomocí sady Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 298d70b4f01da221ca469aaf49d68ed0b91325d3
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274994"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor **helloworld.cpp**.

1. Nahraďte celý kód následujícím fragmentem:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/text-to-speech/helloworld/helloworld.cpp#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Na řádku nabídek zvolte **Soubor** > **Uložit vše**.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. Na řádku nabídek vyberte **sestavení** > **sestavení řešení** k sestavení aplikace. Kód by se teď měl zkompilovat bez chyb.

1. **Chcete-li** > spustit aplikaci **Helloworld,** zvolte Ladění**spouštění ladění** (nebo stiskněte **klávesu F5).**

1. Zadejte anglickou frázi nebo větu. Aplikace přenáší váš text do služby Řeč, která odesílá syntetizovaný projev do aplikace k přehrávání na reproduktoru.

   ![Výstup konzoly po úspěšné syntéze řeči](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]


## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Záznam vlastních hlasových ukázek](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
