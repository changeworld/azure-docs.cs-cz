---
title: Úvodní příručka – pomocí příkazového příkazového příkazu Azure vytvořte frontu služby Service Bus | Dokumenty společnosti Microsoft
description: V tomto rychlém startu se dozvíte, jak pomocí příkazového příkazu Azure vytvořit frontu service bus. Potom použijete ukázkovou aplikaci Java k odesílání zpráv a přijímání zpráv z fronty.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: java
ms.topic: quickstart
ms.date: 04/10/2019
ms.author: spelluru
ms.openlocfilehash: e34599b12b8b0c487c6813038951b051d1eaf425
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "66400265"
---
# <a name="quickstart-use-the-azure-cli-to-create-a-service-bus-queue"></a>Úvodní příručka: Vytvoření fronty služby Service Bus pomocí příkazového příkazového příkazu Azure
Tento rychlý start popisuje, jak odesílat a přijímat zprávy pomocí service bus pomocí Azure CLI a knihovny Java service bus. Pokud vás zajímají techničtější podrobnosti, můžete si [přečíst vysvětlení](#understand-the-sample-code) klíčových prvků vzorového kódu.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Požadavky
Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][free account] před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Pokud ke spuštění Cloud Shellu použijete tlačítko **Try It,** přihlaste se do Azure pomocí svých přihlašovacích údajů. 

Pokud jste cloudové prostředí spustili ve webovém prohlížeči přímo nebo na webu Azure Portal, přepněte na **Bash,** pokud se v levém horním rohu Cloud Shellu zobrazí **PowerShell.** 

## <a name="use-the-azure-cli-to-create-resources"></a>Použití azure cli k vytvoření prostředků
Ve službě Cloud Shell zřiďte v příkazovém řádku Bash pomocí následujících příkazů prostředky služby Service Bus. Nezapomeňte nahradit všechny zástupné symboly příslušnými hodnotami: Ukázkový program Javy očekává, že název fronty bude BasicQueue, takže jej neměňte. Můžete chtít zkopírovat nebo vložit příkazy jeden po druhém, takže můžete nahradit hodnoty před jejich spuštěním. 

```azurecli-interactive
# Create a resource group
resourceGroupName="myResourceGroup"

az group create --name $resourceGroupName --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create --resource-group $resourceGroupName --name $namespaceName --location eastus

# Create a Service Bus queue
az servicebus queue create --resource-group $resourceGroupName --namespace-name $namespaceName --name BasicQueue

# Get the connection string for the namespace
connectionString=$(az servicebus namespace authorization-rule keys list --resource-group $resourceGroupName --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv)
```

Po dokončení všech příkazů zkopírujte vybraný připojovací řetězec a název fronty a vložte je do dočasného umístění, třeba do Poznámkového bloku. Budete je potřebovat v dalším kroku.

## <a name="send-and-receive-messages"></a>Odesílání a příjem zpráv

Když vytvoříte obor názvů a frontu a máte potřebná pověření, jste připraveni odesílat a přijímat zprávy. Kód si můžete zkontrolovat v [této složce s ukázkami na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

1. Naklonujte [úložiště GitHub service busu](https://github.com/Azure/azure-service-bus/) v počítači vydáním následujícího příkazu:

   ```bash
   git clone https://github.com/Azure/azure-service-bus.git
   ```

1. Změňte svůj aktuální adresář na složku s ukázkou použitím lomítek jako oddělovačů cest:

   ```bash
   cd azure-service-bus/samples/Java/azure-servicebus/QueuesGettingStarted
   ```

1. Proveďte následující příkaz, kterým vytvoříte aplikaci:
   
   ```bash
   mvn clean package -DskipTests
   ```

1. Chcete-li spustit program, vyjezte po nahrazení připojovacího řetězce hodnotou, kterou jste zkopírovali dříve, následující příkaz:

   ```bash
   java -jar ./target/queuesgettingstarted-1.0.0-jar-with-dependencies.jar -c "<SERVICE BUS NAMESPACE CONNECTION STRING>" 
   ```

1. Sledujte, jak se do fronty odešle 10 zpráv. Řazení zpráv není zaručeno, ale můžete vidět odeslané, pak potvrzené a přijaté, spolu s daty datové části:

    ```
    Message sending: Id = 0
    Message sending: Id = 1
    Message sending: Id = 2
    Message sending: Id = 3
    Message sending: Id = 4
    Message sending: Id = 5
    Message sending: Id = 6
    Message sending: Id = 7
    Message sending: Id = 8
    Message sending: Id = 9
            Message acknowledged: Id = 9
            Message acknowledged: Id = 3
                                    Message received:
                                                    MessageId = 9,
                                                    SequenceNumber = 54324670505156609,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.972Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.972Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Nikolaus, name = Kopernikus ]
    
            Message acknowledged: Id = 2
            Message acknowledged: Id = 5
            Message acknowledged: Id = 1
            Message acknowledged: Id = 8
            Message acknowledged: Id = 7
            Message acknowledged: Id = 0
            Message acknowledged: Id = 6
            Message acknowledged: Id = 4
                                    Message received:
                                                    MessageId = 3,
                                                    SequenceNumber = 58828270132527105,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.972Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.972Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Steven, name = Hawking ]
    
                                    Message received:
                                                    MessageId = 2,
                                                    SequenceNumber = 9288674231451649,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.012Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.012Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Marie, name = Curie ]
    
                                    Message received:
                                                    MessageId = 1,
                                                    SequenceNumber = 22799473113563137,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.025Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.025Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Werner, name = Heisenberg ]
    
                                    Message received:
                                                    MessageId = 8,
                                                    SequenceNumber = 67835469387268097,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.028Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.028Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Johannes, name = Kepler ]
    
                                    Message received:
                                                    MessageId = 7,
                                                    SequenceNumber = 4785074604081153,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.020Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.020Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Galileo, name = Galilei ]
    
                                    Message received:
                                                    MessageId = 5,
                                                    SequenceNumber = 13792273858822145,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.027Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.027Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Niels, name = Bohr ]
    
                                    Message received:
                                                    MessageId = 0,
                                                    SequenceNumber = 18295873486192641,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.021Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.021Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Albert, name = Einstein ]
    
                                    Message received:
                                                    MessageId = 6,
                                                    SequenceNumber = 281474976710657,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.019Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.019Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Michael, name = Faraday ]
    
                                    Message received:
                                                    MessageId = 4,
                                                    SequenceNumber = 63331869759897601,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.964Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.964Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Isaac, name = Newton ]
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V prostředí Azure Cloud Shell spusťte následující příkaz a odeberte skupinu prostředků, obor názvů a všechny související prostředky:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Tato část obsahuje další podrobnosti o klíčových částech vzorového kódu. Kód si můžete prohlédnout [zde](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) na úložišti GitHub.

### <a name="get-connection-string"></a>Získat připojovací řetězec

Metoda runApp přečte hodnotu připojovacího řetězce z argumentů do programu. 

```java
public static void main(String[] args) {

    System.exit(runApp(args, (connectionString) -> {
        QueuesGettingStarted app = new QueuesGettingStarted();
        try {
            app.run(connectionString);
            return 0;
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 1;
        }
    }));
}

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
```

### <a name="create-queue-clients-to-send-and-receive"></a>Vytvoření klientů fronty k odesílání a přijímání zpráv

Abyste mohli odesílat a přijímat zprávy, metoda `run()` vytvoří instance klienta fronty, které se skládají z připojovacího řetězce a názvu fronty. Tento kód vytvoří dva klienty fronty – jednoho pro odesílání a jednoho pro přijímání zpráv:

```java
public void run(String connectionString) throws Exception {

    // Create a QueueClient instance for receiving using the connection string builder
    // We set the receive mode to "PeekLock", meaning the message is delivered
    // under a lock and must be acknowledged ("completed") to be removed from the queue
    QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(connectionString, "BasicQueue"), ReceiveMode.PEEKLOCK);
    // We are using single thread executor as we are only processing one message at a time
    ExecutorService executorService = Executors.newSingleThreadExecutor();
    this.registerReceiver(receiveClient, executorService);

    // Create a QueueClient instance for sending and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(connectionString, "BasicQueue"), ReceiveMode.PEEKLOCK);
    this.sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    // wait for ENTER or 10 seconds elapsing
    waitForEnter(10);

    // shut down receiver to close the receive loop
    receiveClient.close();
    executorService.shutdown();
}
``` 

### <a name="construct-and-send-messages"></a>Vytvoření a odeslání zpráv

Metoda `sendMessagesAsync()` vytvoří sadu deseti zpráv a pomocí klienta fronty je asynchronně odešle:

```java
CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
    List<HashMap<String, String>> data =
            GSON.fromJson(
                    "[" +
                            "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                            "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                            "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                            "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                            "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                            "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                            "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                            "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                            "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                            "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                            "]",
                    new TypeToken<List<HashMap<String, String>>>() {}.getType());

    List<CompletableFuture> tasks = new ArrayList<>();
    for (int i = 0; i < data.size(); i++) {
        final String messageId = Integer.toString(i);
        Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
        message.setContentType("application/json");
        message.setLabel("Scientist");
        message.setMessageId(messageId);
        message.setTimeToLive(Duration.ofMinutes(2));
        System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
        tasks.add(
                sendClient.sendAsync(message).thenRunAsync(() -> {
                    System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                }));
    }
    return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
}
```

### <a name="receive-messages"></a>Příjem zpráv

Metoda `registerReceiver()` zaregistruje zpětné volání `RegisterMessageHandler` a zároveň nastaví některé možnosti popisovače zpráv:

```java
void registerReceiver(QueueClient queueClient, ExecutorService executorService) throws Exception {

    
    // register the RegisterMessageHandler callback with executor service
    queueClient.registerMessageHandler(new IMessageHandler() {
                                            // callback invoked when the message handler loop has obtained a message
                                            public CompletableFuture<Void> onMessageAsync(IMessage message) {
                                                // receives message is passed to callback
                                                if (message.getLabel() != null &&
                                                        message.getContentType() != null &&
                                                        message.getLabel().contentEquals("Scientist") &&
                                                        message.getContentType().contentEquals("application/json")) {

                                                    byte[] body = message.getBody();
                                                    Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                                                    System.out.printf(
                                                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                                                                    "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                                                            message.getMessageId(),
                                                            message.getSequenceNumber(),
                                                            message.getEnqueuedTimeUtc(),
                                                            message.getExpiresAtUtc(),
                                                            message.getContentType(),
                                                            scientist != null ? scientist.get("firstName") : "",
                                                            scientist != null ? scientist.get("name") : "");
                                                }
                                                return CompletableFuture.completedFuture(null);
                                            }

                                            // callback invoked when the message handler has an exception to report
                                            public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                                                System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                                            }
                                        },
            // 1 concurrent call, messages are auto-completed, auto-renew duration
            new MessageHandlerOptions(1, true, Duration.ofMinutes(1)),
            executorService);

}
```

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořil obor názvů služby Service Bus a další prostředky potřebné k odesílání a přijímání zpráv z fronty. Další informace o psaní kódu pro odesílání a přijímání zpráv, pokračujte v kurzech v části **Odesílat a přijímat zprávy.** 

> [!div class="nextstepaction"]
> [Odesílání a příjem zpráv](service-bus-dotnet-get-started-with-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
