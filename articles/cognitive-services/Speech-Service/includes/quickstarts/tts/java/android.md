---
title: 'Úvodní příručka: Syntetizační řeč, Java (Android) - Služba řeči'
titleSuffix: Azure Cognitive Services
description: Naučte se syntetizovat řeč v Jazyce Java v systému Android pomocí sady Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yulili
ms.openlocfilehash: d114e75a08f31a664772b84e19ec4d93b453af0b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275281"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)

## <a name="create-user-interface"></a>Vytvoření uživatelského rozhraní

Vytvoříme základní uživatelské rozhraní pro aplikaci. Upravte rozložení hlavní aktivity `activity_main.xml`. Zpočátku rozložení obsahuje záhlaví s názvem aplikace a TextView obsahující text "Hello World!".

1. Klikněte na element TextView. Změňte atribut ID v pravém `outputMessage`horním rohu na a přetáhněte ho na dolní obrazovku. Odstraňte jeho text.

1. Z palety v levém horním rohu okna `activity_main.xml` přetáhněte tlačítko na prázdné místo nad textem.

1. V atributech tlačítka na pravé straně zadejte u atributu `onClick` hodnotu `onSpeechButtonClicked`. Napíšeme metodu s tímto názvem, která bude obsluhovat událost tohoto tlačítka.  Změňte jeho atribut ID v pravém horním rohu na `button`.

1. Přetáhněte prostý text do prostoru nad tlačítkem; změňte atribut ID na `speakText`a změňte atribut textu na `Hi there!`.

1. Pomocí ikony kouzelné hůlky v horní části návrháře vymezte omezení rozložení.


    ![Snímek obrazovky s ikonou kouzelné hůlky](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Text a grafické znázornění uživatelského rozhraní by teď měly vypadat takto:

![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor `MainActivity.java`. Veškerý kód v tomto souboru nahraďte následujícím kódem.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onSpeechButtonClicked` je obslužná rutina pro kliknutí na tlačítko, jak bylo uvedeno výše. Stisknutí tlačítka spustí syntézu řeči.

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Také řetězec `YourServiceRegion` nahraďte [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Připojte k vývojovému počítači zařízení s Androidem. Nezapomeňte na tomto zařízení povolit [režim vývoje a ladění USB](https://developer.android.com/studio/debug/dev-options). Případně vytvořte [emulátor Android](https://developer.android.com/studio/run/emulator).

1. Chcete-li vytvořit aplikaci, stiskněte kombinaci kláves Ctrl+F9 nebo zvolte **Vytvořit** > **projekt** z panelu nabídek.

1. Chcete-li aplikaci spustit, stiskněte Shift+F10 nebo zvolte **Spustit** > **spustit aplikaci**.

1. V okně cíle nasazení, které se zobrazí, zvolte zařízení Android nebo emulátor.

   ![Snímek obrazovky okna s výběrem cíle nasazení](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Zadejte text a stisknutím tlačítka v aplikaci zahájíte část syntézy řeči. Uslyšíte syntetizovaný zvuk z výchozího reproduktoru `speech synthesis succeeded` a uvidíte informace na obrazovce.

![Snímek obrazovky aplikace pro Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Záznam vlastních hlasových ukázek](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
