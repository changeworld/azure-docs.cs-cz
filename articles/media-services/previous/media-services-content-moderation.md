---
title: Pomocí moderátora mediálního obsahu Azure můžete zjistit možný obsah pro dospělé a pikantní obsah | Dokumenty společnosti Microsoft
description: Mediální procesor Azure Media Content Moderator pomáhá ve videích odhalit potenciální obsah pro dospělé a pikantní obsah.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: 83fe7867a3128ac82597c028452863a1ad681ace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914309"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Použití Moderátora mediálního obsahu Azure k detekci možného obsahu pro dospělé a pikantního obsahu 

> [!NOTE]
> Mediální procesor **Azure Media Content Moderator** bude vyřazen. Datum vyřazení naleznete v tématu [starších součástí.](legacy-components.md)

## <a name="overview"></a>Přehled
Mediální procesor **Azure Media Content Moderator** (MP) umožňuje pro vaše videa používat uprostředná rážování s pomocí počítače. Ve videích například můžete chtít detekovat potenciálně nevhodný obsah nebo obsah pro dospělé a označený obsah nechat zkontrolovat týmy lidského moderování.

Mp **Moder mediálního obsahu Azure** je momentálně ve verzi Preview.

Tento článek obsahuje podrobnosti o **Moderátora mediálního obsahu Azure** a ukazuje, jak jej používat s sadou Media Services SDK pro rozhraní .NET.

## <a name="content-moderator-input-files"></a>Vstupní soubory moderátora obsahu
Video soubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="content-moderator-output-files"></a>Výstupní soubory moderátora obsahu
Moderovaný výstup ve formátu JSON zahrnuje automaticky detekované snímky a klíčové snímky. Klíčové snímky jsou vráceny s skóre spolehlivosti pro možný obsah pro dospělé nebo pikantní. Obsahují také logický příznak označující, zda je doporučena kontrola. Příznak doporučení recenze je přiřazen hodnoty založené na vnitřníprahové hodnoty pro dospělé a pikantní skóre.

## <a name="elements-of-the-output-json-file"></a>Prvky výstupního souboru JSON

Úloha vytvoří výstupní soubor JSON, který obsahuje metadata o zjištěných záběrech a klíčových snímcích a o tom, zda obsahují obsah pro dospělé nebo pikantní.

Výstup JSON obsahuje následující prvky:

### <a name="root-json-elements"></a>Kořenové prvky JSON

| Element | Popis |
| --- | --- |
| version |Verze Content Moderator. |
| Časové osy |"Klíšťata" za sekundu videa. |
| posun |Posun času pro časová razítka. Ve verzi 1.0 video API bude tato hodnota vždy 0. Tato hodnota se může v budoucnu změnit. |
| Framerate |Počet snímků ve videu za sekundu. |
| šířka |Šířka výstupního snímku videa v obrazových bodech.|
| height |Výška výstupního snímku videa v obrazových bodech.|
| totalDuration |Doba trvání vstupního videa v "ticks". |
| [Fragmenty](#fragments-json-elements) |Metadata jsou rozdělena do různých segmentů nazývaných fragmenty. Každý fragment je automaticky zjištěný snímek se startem, dobou trvání, číslem intervalu a událostmi. |

### <a name="fragments-json-elements"></a>Fragmenty prvků JSON

|Element|Popis|
|---|---|
| start |Čas zahájení první události v "tiky". |
| doba trvání |Délka fragmentu v "klíšťají". |
| interval |Interval každé položky události v rámci fragmentu v "značky." |
| [Události](#events-json-elements) |Každá událost představuje klip a každý klip obsahuje klíčové snímky zjištěné a sledované během této doby trvání. Jedná se o řadu událostí. Vnější pole představuje jeden časový interval. Vnitřní pole se skládá z 0 nebo více událostí, které se v tomto bodu v čase odehrály.|

### <a name="events-json-elements"></a>Události JSON prvky

|Element|Popis|
|---|---|
| recenzeDoporučeno | `true`nebo `false` v závislosti na tom, zda **adultScore** nebo **racyScore** překračují vnitřní prahové hodnoty. |
| adultScore | Skóre spolehlivosti pro možný obsah pro dospělé na stupnici od 0,00 do 0,99. |
| racyScore | Skóre spolehlivosti pro možný pikantní obsah na stupnici od 0,00 do 0,99. |
| index | index rámce v měřítku od prvního indexu snímku k poslednímu indexu rámce. |
| časové razítko | Umístění rámce v "značek." |
| shotIndex | Index rodičovského snímku. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Rychlý start moderování obsahu a ukázkový výstup

### <a name="task-configuration-preset"></a>Konfigurace úloh (přednastavení)
Při vytváření úlohy pomocí **moderátora mediálního obsahu Azure**je nutné zadat přednastavení konfigurace. Následující přednastavení konfigurace je pouze pro moderování obsahu.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Ukázka kódu .NET

Následující ukázka kódu .NET používá k spuštění úlohy Moderátor obsahu sadou Media Services .NET SDK. Trvá mediální služby Asset jako vstup, který obsahuje video moderovat.
Podívejte se na [rychlý start videa Content Moderator](../../cognitive-services/Content-Moderator/video-moderation-api.md) pro úplný zdrojový kód a projekt sady Visual Studio.


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }
```

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Přehled analýzy Mediálních služeb Azure](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [řešení moderování a kontroly videa](../../cognitive-services/Content-Moderator/video-moderation-human-review.md)moderátora obsahu .

Získejte úplný zdrojový kód a projekt sady Visual Studio z [rychlého startu moderování videa](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Přečtěte si, jak generovat [recenze videa](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) z moderovaného výstupu a [moderovat přepisy](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) v rozhraní .NET.

Podívejte se na podrobný kurz [moderování videa](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md).NET a recenzi . 
