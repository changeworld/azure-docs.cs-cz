---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c029165a4994e5e3f73399fb00a1775b67128e44
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879006"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Převod řeči na text nebo vlastní převod řeči na text

Kontejner poskytuje rozhraní API koncového bodu dotazu založené na websocket, které jsou přístupné prostřednictvím [sady Speech SDK](../index.yml). Ve výchozím nastavení používá sada Speech SDK služby online řeči. Chcete-li použít kontejner, je třeba změnit metodu inicializace.

> [!TIP]
> Při použití sady Speech SDK s kontejnery není nutné zadat klíč předplatného prostředku Azure Speech [nebo token nosiče ověřování](../rest-speech-to-text.md#authentication).

Podívejte se na následující příklad:

# <a name="c"></a>[C #](#tab/csharp)

Změna z použití tohoto volání inicializace Azure cloudu:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

k tomuto volání pomocí [hostitele](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)kontejneru :

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

Změna z použití tohoto volání inicializace Azure cloudu:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

k tomuto volání pomocí [hostitele](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)kontejneru :

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
