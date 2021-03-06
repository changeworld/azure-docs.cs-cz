---
title: Odesílání a přijímání událostí z Azure Event Hubs pomocí rozhraní .NET (staré)
description: Tento článek poskytuje návod pro vytvoření aplikace .NET Core, která odesílá a přijímá události do/z Azure Event Hubs pomocí starého balíčku Microsoft.Azure.EventHubs.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: a8124e9860ba5703e96f96c90bc0cf37e42baf9c
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398344"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core-microsoftazureeventhubs"></a>Odesílání událostí do nebo přijímání událostí z Centra událostí Azure pomocí .NET Core (Microsoft.Azure.EventHubs)
Tento rychlý start ukazuje, jak odesílat události do centra událostí a přijímat je z centra událostí pomocí **knihovny Microsoft.Azure.EventHubs** .NET Core.

> [!WARNING]
> Tento rychlý start používá starý balíček **Microsoft.Azure.EventHubs.** Pro rychlý start, který používá nejnovější knihovnu **Azure.Messaging.EventHubs,** najdete v tématu [Odesílání a přijímání událostí pomocí knihovny Azure.Messaging.EventHubs](get-started-dotnet-standard-send-v2.md). Pokud chcete přesunout aplikaci z používání staré knihovny na novou, přečtěte si [příručku pro migraci z Microsoft.Azure.EventHubs na Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).

## <a name="prerequisites"></a>Požadavky
Pokud s Azure Event Hubs tečujete, přečtěte si [téma Přehled centra událostí,](event-hubs-about.md) než začnete tento rychlý start. 

Chcete-li tento rychlý start dokončit, potřebujete následující požadavky:

- **Předplatné Microsoft Azure**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo použít výhody předplatitele MSDN při vytváření [účtu](https://azure.microsoft.com).
- [Microsoft Visual Studio 2019](https://www.visualstudio.com).
- [Nástroje .NET Core pro sadu Visual Studio 2015 nebo 2017](https://www.microsoft.com/net/core). 
- **Vytvořte obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [portálu Azure](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Chcete-li vytvořit obor názvů a centrum událostí, postupujte podle postupu v [tomto článku](event-hubs-create.md). Potom získat **připojovací řetězec pro obor názvů centra událostí** podle následujících pokynů z článku: Získat [připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto rychlém startu.

## <a name="send-events"></a>Odesílání událostí 
Tato část ukazuje, jak vytvořit konzolovou aplikaci .NET Core pro odesílání událostí do centra událostí. 

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Případně můžete postupovat podle kroků v tomto rychlém startu a vytvořit si vlastní.


### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

Spusťte Visual Studio. V nabídce **Soubor** klikněte na položku **Nový** a potom klikněte na položku **Projekt**. Vytvořte konzolovou aplikaci .NET Core.

![Nový projekt](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

Přidejte [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) balíček NuGet knihovny .NET Core do projektu následujícím postupem: 

1. Klikněte pravým tlačítkem na nově vytvořený projekt a vyberte možnost **Spravovat balíčky NuGet**.
2. Klikněte na kartu **Procházet**, vyhledejte Microsoft.Azure.EventHubs a pak vyberte balíček **Microsoft.Azure.EventHubs**. Klikněte na **Instalovat** a dokončete instalaci, pak zavřete dialogové okno.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

1. Na začátek souboru Program.cs přidejte následující příkazy `using`:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Do třídy `Program` přidejte konstanty pro připojovací řetězec služby Event Hubs a cestu k entitě (název konkrétního centra událostí). Zástupné symboly v závorkách nahraďte odpovídajícími hodnotami, které jste získali při vytváření centra událostí. Ujistěte se, že `{Event Hubs connection string}` je připojovací řetězec na úrovni oboru názvů, a ne řetězec centra událostí. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Do třídy `Program` přidejte následujícím způsobem novou metodu `MainAsync`:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Do třídy `Program` přidejte následujícím způsobem novou metodu `SendMessagesToEventHub`:

    ```csharp
    // Uses the event hub client to send 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Do metody `Main` ve třídě `Program` přidejte následující kód:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Soubor Program.cs by měl vypadat takhle.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Uses the event hub client to send 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Spusťte program a zkontrolujte, že nejsou žádné chyby.

## <a name="receive-events"></a>Příjem událostí
Tato část ukazuje, jak napsat konzolovou aplikaci .NET Core, která přijímá zprávy z centra událostí pomocí [hostitele procesoru událostí](event-hubs-event-processor-host.md). [Host procesoru událostí](event-hubs-event-processor-host.md) je třída .NET, která zjednodušuje příjem událostí z centra událostí správou trvalých kontrolních bodů a paralelních příjemů z těchto center událostí. Pomocí třídy Event Processor Host můžete události rozdělit mezi několik příjemců, i když jsou hostovaní v různých uzlech. Tento příklad ukazuje způsob použití třídy Event Processor Host pro jednoho příjemce.
> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku `EventHubName` `StorageAccountName`z `StorageAccountKey` [GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), nahradit `EventHubConnectionString` a , a `StorageContainerName` řetězce s hodnotami centra událostí a spustit je. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

Spusťte Visual Studio. V nabídce **Soubor** klikněte na položku **Nový** a potom klikněte na položku **Projekt**. Vytvořte konzolovou aplikaci .NET Core.

![Nový projekt](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

Pomocí následujícího postupu do svého projektu přidejte balíčky NuGet knihoven .NET Standard [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) a [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/): 

1. Klikněte pravým tlačítkem na nově vytvořený projekt a vyberte možnost **Spravovat balíčky NuGet**.
2. Klikněte na kartu **Procházet**, vyhledejte **Microsoft.Azure.EventHubs** a pak vyberte balíček **Microsoft.Azure.EventHubs**. Klikněte na **Instalovat** a dokončete instalaci, pak zavřete dialogové okno.
3. Zopakujte kroky 1 a 2 a nainstalujte balíček **Microsoft.Azure.EventHubs.Processor**.

### <a name="implement-the-ieventprocessor-interface"></a>Implementace rozhraní IEventProcessor

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt, klikněte na **Přidat** a pak klikněte na **Třída**. Pojmenujte novou třídu **SimpleEventProcessor**.

2. Otevřete soubor SimpleEventProcessor.cs a přidejte na jeho začátek následující příkazy `using`.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implementujte rozhraní `IEventProcessor`. Celý obsah třídy `SimpleEventProcessor` nahraďte následujícím kódem:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aktualizace metody Main pro použití třídy SimpleEventProcessor

1. Do horní části souboru Program.cs přidejte následující příkazy `using`.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Do třídy `Program` přidejte konstanty pro připojovací řetězec centra událostí, název centra událostí, název kontejneru účtu úložiště, název účtu úložiště a klíč účtu úložiště. Přidejte následující kód a zástupné texty nahraďte odpovídajícími hodnotami:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Do třídy `Program` přidejte následujícím způsobem novou metodu `MainAsync`:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Do metody `Main` přidejte následující řádek kódu:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Soubor Program.cs by měl vypadat takhle:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Spusťte program a zkontrolujte, že nejsou žádné chyby.


## <a name="next-steps"></a>Další kroky
Přečtěte si následující články:

- [Ukázky řízení přístupu na základě rolí (RBAC).](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 
    
    Tyto ukázky používají starou knihovnu **Microsoft.Azure.EventHubs,** ale můžete ji snadno aktualizovat na použití nejnovější **knihovny Azure.Messaging.EventHubs.** Pokud chcete vzorek přesunout z používání staré knihovny do nové, přečtěte si [příručku pro migraci z Microsoft.Azure.EventHubs na Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).)
- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie ve službě Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)


