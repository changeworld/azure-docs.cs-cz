---
title: 'Úvodní příručka: Computer Vision 2.0 a 2.1 - Extrahovat tištěný a ručně psaný text - REST, Python'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu extrahovat tištěný a ručně psaný text z obrázku pomocí rozhraní API pro počítačové zpracování obrazu s Pythonem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: fafc5234da3e6dfecdae2c8b2e2ba80cbed20b5c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80244797"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-20-and-21-rest-api-and-python"></a>Úvodní příručka: Extrakce tištěného a ručně psaného textu pomocí rozhraní API PRO ZOBRAZENÍ počítače 2.0 a 2.1 a Pythonu

V tomto rychlém startu extrahnete tištěný nebo ručně psaný text z obrázku pomocí rozhraní API REKONČOVÁNÍ Počítače. Pomocí metod [Batch Read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) and Read [Operation Result](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) můžete detekovat text v obraze a extrahovat rozpoznané znaky do strojově čitelného datového proudu znaků. Rozhraní API určí, který model rozpoznávání se má použít pro každý řádek textu, takže podporuje obrázky s vytištěným i ručně psaným textem.

Ve srovnání s počítačovým viděním 2.0 a 2.1 poskytuje verze Computer Vision 3.0 Public Preview:

* ještě lepší přesnost
* změněný výstupní formát
* skóre spolehlivosti pro slova
* podpora španělského i anglického jazyka s dodatečným jazykovým parametrem

#### <a name="version-2"></a>[Verze 2](#tab/version-2)

> [!IMPORTANT]
> Metoda [dávkového čtení](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) běží asynchronně. Tato metoda nevrací žádné informace v textu úspěšné odpovědi. Místo toho vrátí metoda Dávkové čtení identifikátor `Operation-Location` URI v hodnotě pole hlavičky odpovědi. Potom můžete volat tento identifikátor URI, který představuje rozhraní [API výsledek operace čtení,](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) jak zkontrolovat stav a vrátit výsledky volání metody dávkového čtení.

#### <a name="version-3-public-preview"></a>[Verze 3 (Veřejná verze)](#tab/version-3)

> [!IMPORTANT]
> Metoda [dávkového čtení](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d986960601faab4bf452005) běží asynchronně. Tato metoda nevrací žádné informace v textu úspěšné odpovědi. Místo toho vrátí metoda Dávkové čtení identifikátor `Operation-Location` URI v hodnotě pole hlavičky odpovědi. Potom můžete volat tento identifikátor URI, který představuje rozhraní [API výsledek operace čtení,](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d9869604be85dee480c8750) jak zkontrolovat stav a vrátit výsledky volání metody dávkového čtení.

---

Tento rychlý start můžete spustit jako podrobný návod pomocí Jupyter Notebooku na webu [MyBinder](https://mybinder.org). Pokud chcete spustit Binder, vyberte následující tlačítko:

[![Tlačítko spouštění Pořadače](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) než začnete.

- Pokud chcete spustit tuto ukázku v místním prostředí, musíte mít nainstalovaný jazyk [Python](https://www.python.org/downloads/).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Můžete získat bezplatný zkušební klíč od [try cognitive services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Nebo postupujte podle pokynů v [tématu Vytvoření účtu služeb Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) abyste se přihlásili k odběru počítačového vidění a získali klíč. Potom [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec koncového `COMPUTER_VISION_SUBSCRIPTION_KEY` klíče `COMPUTER_VISION_ENDPOINT`a služby s názvem a , resp.

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

#### <a name="version-2"></a>[Verze 2](#tab/version-2)

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující kód.
1. Volitelně můžete nahradit `image_url` hodnotu adresy URL jiného obrázku, ze kterého chcete extrahovat text.
1. Uložte kód jako soubor s příponou `.py`. Například, `get-text.py`.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python get-text.py`.

```python
import os
import sys
import requests
import time
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

text_recognition_url = endpoint + "vision/v2.1/read/core/asyncBatchAnalyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/dd/Cursive_Writing_on_Notebook_paper.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
data = {'url': image_url}
response = requests.post(
    text_recognition_url, headers=headers, json=data)
response.raise_for_status()

# Extracting text requires two API calls: One call to submit the
# image for processing, the other to retrieve the text found in the image.

# Holds the URI used to retrieve the recognized text.
operation_url = response.headers["Operation-Location"]

# The recognized text isn't immediately available, so poll to wait for completion.
analysis = {}
poll = True
while (poll):
    response_final = requests.get(
        response.headers["Operation-Location"], headers=headers)
    analysis = response_final.json()
    print(analysis)
    time.sleep(1)
    if ("recognitionResults" in analysis):
        poll = False
    if ("status" in analysis and analysis['status'] == 'Failed'):
        poll = False

polygons = []
if ("recognitionResults" in analysis):
    # Extract the recognized text, with bounding boxes.
    polygons = [(line["boundingBox"], line["text"])
                for line in analysis["recognitionResults"][0]["lines"]]

# Display the image and overlay it with the extracted text.
plt.figure(figsize=(15, 15))
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image)
for polygon in polygons:
    vertices = [(polygon[0][i], polygon[0][i+1])
                for i in range(0, len(polygon[0]), 2)]
    text = polygon[1]
    patch = Polygon(vertices, closed=True, fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(vertices[0][0], vertices[0][1], text, fontsize=20, va="top")
```

#### <a name="version-3-public-preview"></a>[Verze 3 (Veřejná verze)](#tab/version-3)

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující kód.
1. Volitelně můžete nahradit `image_url` hodnotu adresy URL jiného obrázku, ze kterého chcete extrahovat text.
1. Uložte kód jako soubor s příponou `.py`. Například, `get-text.py`.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python get-text.py`.

```python
import json
import os
import sys
import requests
import time
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
from PIL import Image
from io import BytesIO

missing_env = False
# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']
else:
    print("From Azure Cogntivie Service, retrieve your endpoint and subscription key.")
    print("\nSet the COMPUTER_VISION_ENDPOINT environment variable, such as \"https://westus2.api.cognitive.microsoft.com\".\n")
    missing_env = True

if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("From Azure Cogntivie Service, retrieve your endpoint and subscription key.")
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable, such as \"1234567890abcdef1234567890abcdef\".\n")
    missing_env = True

if missing_env:
    print("**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

text_recognition_url = endpoint + "/vision/v3.0-preview/read/analyze"

# Set image_url to the URL of an image that you want to recognize.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/dd/Cursive_Writing_on_Notebook_paper.jpg"

# Set the langauge that you want to recognize. The value can be "en" for English, and "es" for Spanish
language = "en"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
data = {'url': image_url}
response = requests.post(
    text_recognition_url, headers=headers, json=data, params={'language': language})
response.raise_for_status()

# Extracting text requires two API calls: One call to submit the
# image for processing, the other to retrieve the text found in the image.

# Holds the URI used to retrieve the recognized text.
operation_url = response.headers["Operation-Location"]

# The recognized text isn't immediately available, so poll to wait for completion.
analysis = {}
poll = True
while (poll):
    response_final = requests.get(
        response.headers["Operation-Location"], headers=headers)
    analysis = response_final.json()
    
    print(json.dumps(analysis, indent=4))

    time.sleep(1)
    if ("analyzeResult" in analysis):
        poll = False
    if ("status" in analysis and analysis['status'] == 'failed'):
        poll = False

polygons = []
if ("analyzeResult" in analysis):
    # Extract the recognized text, with bounding boxes.
    polygons = [(line["boundingBox"], line["text"])
                for line in analysis["analyzeResult"]["readResults"][0]["lines"]]

# Display the image and overlay it with the extracted text.
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image)
for polygon in polygons:
    vertices = [(polygon[0][i], polygon[0][i+1])
                for i in range(0, len(polygon[0]), 2)]
    text = polygon[1]
    patch = Polygon(vertices, closed=True, fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(vertices[0][0], vertices[0][1], text, fontsize=20, va="top")
plt.show()
```

---

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková webová stránka provede analýzu a zobrazí úspěšnou odpověď v okně příkazového řádku, podobně jako v následujícím příkladu:

#### <a name="version-2"></a>[Verze 2](#tab/version-2)

```json
{
  "status": "Succeeded",
  "recognitionResult": {
    "lines": [
      {
        "boundingBox": [
          2,
          52,
          65,
          46,
          69,
          89,
          7,
          95
        ],
        "text": "dog",
        "words": [
          {
            "boundingBox": [
              0,
              59,
              63,
              43,
              77,
              86,
              3,
              102
            ],
            "text": "dog"
          }
        ]
      },
      {
        "boundingBox": [
          6,
          2,
          771,
          13,
          770,
          75,
          5,
          64
        ],
        "text": "The quick brown fox jumps over the lazy",
        "words": [
          {
            "boundingBox": [
              0,
              4,
              92,
              5,
              77,
              71,
              0,
              71
            ],
            "text": "The"
          },
          {
            "boundingBox": [
              74,
              4,
              189,
              5,
              174,
              72,
              60,
              71
            ],
            "text": "quick"
          },
          {
            "boundingBox": [
              176,
              5,
              321,
              6,
              306,
              73,
              161,
              72
            ],
            "text": "brown"
          },
          {
            "boundingBox": [
              308,
              6,
              387,
              6,
              372,
              73,
              293,
              73
            ],
            "text": "fox"
          },
          {
            "boundingBox": [
              382,
              6,
              506,
              7,
              491,
              74,
              368,
              73
            ],
            "text": "jumps"
          },
          {
            "boundingBox": [
              492,
              7,
              607,
              8,
              592,
              75,
              478,
              74
            ],
            "text": "over"
          },
          {
            "boundingBox": [
              589,
              8,
              673,
              8,
              658,
              75,
              575,
              75
            ],
            "text": "the"
          },
          {
            "boundingBox": [
              660,
              8,
              783,
              9,
              768,
              76,
              645,
              75
            ],
            "text": "lazy"
          }
        ]
      },
      {
        "boundingBox": [
          2,
          84,
          783,
          96,
          782,
          154,
          1,
          148
        ],
        "text": "Pack my box with five dozen liquor jugs",
        "words": [
          {
            "boundingBox": [
              0,
              86,
              94,
              87,
              72,
              151,
              0,
              149
            ],
            "text": "Pack"
          },
          {
            "boundingBox": [
              76,
              87,
              164,
              88,
              142,
              152,
              54,
              150
            ],
            "text": "my"
          },
          {
            "boundingBox": [
              155,
              88,
              243,
              89,
              222,
              152,
              134,
              151
            ],
            "text": "box"
          },
          {
            "boundingBox": [
              226,
              89,
              344,
              90,
              323,
              154,
              204,
              152
            ],
            "text": "with"
          },
          {
            "boundingBox": [
              336,
              90,
              432,
              91,
              411,
              154,
              314,
              154
            ],
            "text": "five"
          },
          {
            "boundingBox": [
              419,
              91,
              538,
              92,
              516,
              154,
              398,
              154
            ],
            "text": "dozen"
          },
          {
            "boundingBox": [
              547,
              92,
              701,
              94,
              679,
              154,
              525,
              154
            ],
            "text": "liquor"
          },
          {
            "boundingBox": [
              696,
              94,
              800,
              95,
              780,
              154,
              675,
              154
            ],
            "text": "jugs"
          }
        ]
      }
    ]
  }
}
```

#### <a name="version-3-public-preview"></a>[Verze 3 (Veřejná verze)](#tab/version-3)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-02-11T17:54:10Z",
    "lastUpdatedDateTime": "2020-02-11T17:54:10Z",
    "analyzeResult": {
        "version": "3.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0.5268,
                "width": 1875,
                "height": 361,
                "unit": "pixel",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            8,
                            9,
                            1814,
                            23,
                            1812,
                            138,
                            7,
                            121
                        ],
                        "text": "The quick brown fox jumps over the lazy",
                        "words": [
                            {
                                "boundingBox": [
                                    31,
                                    10,
                                    184,
                                    14,
                                    177,
                                    116,
                                    24,
                                    112
                                ],
                                "text": "The",
                                "confidence": 0.905
                            },
                            {
                                "boundingBox": [
                                    204,
                                    14,
                                    430,
                                    18,
                                    422,
                                    123,
                                    197,
                                    117
                                ],
                                "text": "quick",
                                "confidence": 0.762
                            },
                            {
                                "boundingBox": [
                                    450,
                                    18,
                                    736,
                                    22,
                                    727,
                                    130,
                                    442,
                                    124
                                ],
                                "text": "brown",
                                "confidence": 0.57
                            },
                            {
                                "boundingBox": [
                                    756,
                                    23,
                                    895,
                                    24,
                                    886,
                                    133,
                                    747,
                                    130
                                ],
                                "text": "fox",
                                "confidence": 0.847
                            },
                            {
                                "boundingBox": [
                                    915,
                                    24,
                                    1168,
                                    25,
                                    1158,
                                    136,
                                    906,
                                    133
                                ],
                                "text": "jumps",
                                "confidence": 0.762
                            },
                            {
                                "boundingBox": [
                                    1188,
                                    25,
                                    1400,
                                    26,
                                    1390,
                                    138,
                                    1178,
                                    136
                                ],
                                "text": "over",
                                "confidence": 0.764
                            },
                            {
                                "boundingBox": [
                                    1420,
                                    26,
                                    1566,
                                    25,
                                    1556,
                                    138,
                                    1410,
                                    138
                                ],
                                "text": "the",
                                "confidence": 0.888
                            },
                            {
                                "boundingBox": [
                                    1586,
                                    25,
                                    1812,
                                    24,
                                    1801,
                                    138,
                                    1576,
                                    138
                                ],
                                "text": "lazy",
                                "confidence": 0.57
                            }
                        ]
                    },
                    {
                        "language": "en",
                        "boundingBox": [
                            15,
                            125,
                            186,
                            135,
                            183,
                            226,
                            8,
                            218
                        ],
                        "text": "dog.",
                        "words": [
                            {
                                "boundingBox": [
                                    15,
                                    125,
                                    187,
                                    133,
                                    182,
                                    227,
                                    10,
                                    218
                                ],
                                "text": "dog.",
                                "confidence": 0.424
                            }
                        ]
                    },
                    {
                        "language": "en",
                        "boundingBox": [
                            9,
                            219,
                            1858,
                            232,
                            1856,
                            338,
                            7,
                            317
                        ],
                        "text": "Pack my box with five dozen liquor jugs.",
                        "words": [
                            {
                                "boundingBox": [
                                    11,
                                    234,
                                    191,
                                    228,
                                    189,
                                    316,
                                    8,
                                    319
                                ],
                                "text": "Pack",
                                "confidence": 0.694
                            },
                            {
                                "boundingBox": [
                                    208,
                                    227,
                                    341,
                                    224,
                                    339,
                                    314,
                                    205,
                                    316
                                ],
                                "text": "my",
                                "confidence": 0.57
                            },
                            {
                                "boundingBox": [
                                    380,
                                    223,
                                    530,
                                    220,
                                    528,
                                    313,
                                    378,
                                    314
                                ],
                                "text": "box",
                                "confidence": 0.252
                            },
                            {
                                "boundingBox": [
                                    553,
                                    220,
                                    781,
                                    220,
                                    778,
                                    314,
                                    550,
                                    313
                                ],
                                "text": "with",
                                "confidence": 0.958
                            },
                            {
                                "boundingBox": [
                                    814,
                                    220,
                                    986,
                                    222,
                                    984,
                                    316,
                                    812,
                                    314
                                ],
                                "text": "five",
                                "confidence": 0.617
                            },
                            {
                                "boundingBox": [
                                    1003,
                                    222,
                                    1231,
                                    228,
                                    1229,
                                    320,
                                    1001,
                                    316
                                ],
                                "text": "dozen",
                                "confidence": 0.694
                            },
                            {
                                "boundingBox": [
                                    1303,
                                    230,
                                    1608,
                                    244,
                                    1607,
                                    330,
                                    1302,
                                    321
                                ],
                                "text": "liquor",
                                "confidence": 0.427
                            },
                            {
                                "boundingBox": [
                                    1647,
                                    247,
                                    1855,
                                    260,
                                    1855,
                                    338,
                                    1646,
                                    331
                                ],
                                "text": "jugs.",
                                "confidence": 0.57
                            }
                        ]
                    }
                ]
            }
        ]
    }
}
```

---

## <a name="next-steps"></a>Další kroky

Prozkoumejte aplikaci v Pythonu používající počítačové zpracování obrazu k optickému rozpoznávání znaků (OCR), vytváření chytře oříznutých miniatur, zjišťování, kategorizaci, označení a popis vizuálních prvků, včetně obličejů, v obrázku. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Kurz k rozhraní API pro počítačové zpracování obrazu a Pythonu](../Tutorials/PythonTutorial.md)
