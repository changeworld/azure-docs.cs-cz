---
title: 'Kurz: Streamování dat do Azure Databricks pomocí služby Event Hubs '
description: Zjistěte, jak pomocí Azure Databricks se službou Event Hubs ingestovat streamovaná data z Twitteru a jak tato data číst téměř v reálném čase.
services: azure-databricks
author: lenadroid
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 12/08/2019
ms.author: alehall
ms.openlocfilehash: fa5568a5af483a61b4e0854cbc7c2ade3b8dc4b1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75889151"
---
# <a name="tutorial-stream-data-into-azure-databricks-using-event-hubs"></a>Kurz: Streamování dat do Azure Databricks pomocí služby Event Hubs

V tomto kurzu propojíte systém pro příjem dat s Azure Databricks, abyste mohli streamovat data do clusteru Apache Spark téměř v reálném čase. Pomocí služby Azure Event Hubs nastavíte systém pro příjem dat a pak ho připojíte k Azure Databricks, aby zpracovával přicházející zprávy. Pro přístup ke streamovaným datům použijete rozhraní Twitter API umožňující ingestování tweetů do služby Event Hubs. Jakmile budete mít data v Azure Databricks, můžete je dále analyzovat spouštěním analytických úloh.

Na konci tohoto kurzu budete umět streamovat tweety z Twitteru (které obsahují výraz Azure) a číst tyto tweety v Azure Databricks.

Následující obrázek ukazuje běh aplikace:

![Azure Databricks s event huby](./media/databricks-stream-from-eventhubs/databricks-eventhubs-tutorial.png "Azure Databricks s event huby")

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření twitterové aplikace pro přístup ke streamovaným datům
> * Vytvoření poznámkových bloků v Azure Databricks
> * Připojení knihoven pro službu Event Hubs a rozhraní Twitter API
> * Odeslání tweetů do služby Event Hubs
> * Čtení tweetů ze služby Event Hubs

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

> [!Note]
> Tento kurz nelze provést pomocí **bezplatného zkušebního předplatného Azure**.
> Pokud máte bezplatný účet, přejděte na svůj profil a změňte předplatné na **průběžně placené**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/). Potom [odeberte limit útraty](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)a [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) pro virtuální procesory ve vaší oblasti. Když vytvoříte pracovní prostor Azure Databricks, můžete vybrat **zkušební (premium - 14denní jednotku DBU)** a poskytnout tak pracovnímu prostoru přístup k bezplatným dbům Azure Databricks Azure na 14 dní.

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, ujistěte se, že splňujete následující požadavky:
- Obor názvů služby Azure Event Hubs.
- Centrum událostí v rámci tohoto oboru názvů.
- Připojovací řetězec pro přístup k oboru názvů služby Event Hubs. Připojovací řetězec by měl mít formát podobný tomuto: `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.
- Název zásady sdíleného přístupu a klíč zásady pro službu Event Hubs.

Tyto požadavky můžete splnit dokončením kroků v článku [Vytvoření oboru názvů služby Azure Event Hubs a centra událostí](../event-hubs/event-hubs-create.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte pomocí portálu Azure pracovní prostor služby Azure Databricks.

1. Na webu Azure Portal vyberte Vytvořit data **o prostředku** > **+ Analytics** > Azure**Databricks**.

    ![Datové cihly na webu Azure Portal](./media/databricks-stream-from-eventhubs/azure-databricks-on-portal.png "Datové cihly na webu Azure Portal")

3. V části **Služba Azure Databricks** zadejte hodnoty pro vytvoření pracovního prostoru Databricks.

    ![Vytvoření pracovního prostoru Azure Databricks](./media/databricks-stream-from-eventhubs/create-databricks-workspace.png "Vytvoření pracovního prostoru Azure Databricks")

    Zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../azure-resource-manager/management/overview.md). |
    |**Umístění**     | Vyberte **USA – východ 2**. Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).        |
    |**Cenová úroveň**     |  Zvolte úroveň **Standard** nebo **Premium**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

4. Vytvoření účtu trvá několik minut. Během vytváření účtu portál zobrazí **odeslání nasazení pro Azure Databricks** dlaždice na pravé straně. Možná se budete muset posunout do pravé části řídicího panelu, aby se dlaždice zobrazila. V horní části obrazovky se také zobrazí indikátor průběhu. Průběh můžete sledovat v obou oblastech.

    ![Dlaždice nasazení Datových cihel](./media/databricks-stream-from-eventhubs/databricks-deployment-tile.png "Dlaždice nasazení Datových cihel")

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

1. Na webu Azure Portal přejděte do pracovního prostoru Databricks, který jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Cluster**.

    ![Datové cihly v Azure](./media/databricks-stream-from-eventhubs/databricks-on-azure.png "Datové cihly v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/databricks-stream-from-eventhubs/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

    Přijměte všechny výchozí hodnoty kromě následujících:

   * Zadejte název clusteru.
   * Pro tento článek vytvořte cluster s **6.0** runtime.
   * Ujistěte se, že jste zaškrtli zaškrtávací políčko **Ukončit po \_ \_ minutách nečinnosti.** Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.

   Vyberte velikost uzlu pracovníka clusteru a ovladače vhodná pro technická kritéria a [rozpočet](https://azure.microsoft.com/pricing/details/databricks/).

     Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Pro příjem streamovaných tweetů je potřeba vytvořit aplikaci na Twitteru. Postupujte podle pokynů k vytvoření aplikace Twitter a poznamenejte si hodnoty, které potřebujete k dokončení tohoto kurzu.

1. Ve webovém prohlížeči přejděte na [Twitter pro vývojáře](https://developer.twitter.com/en/apps)a vyberte **Vytvořit aplikaci**. Může se zobrazit zpráva, že je třeba požádat o vývojářský účet Twitter. Nebojte se tak učinit, a poté, co vaše žádost byla schválena, měli byste vidět potvrzovací e-mail. Schválení vývojářského účtu může trvat několik dní.

    ![Potvrzení vývojářského účtu Twitteru](./media/databricks-stream-from-eventhubs/databricks-twitter-dev-confirmation.png "Potvrzení vývojářského účtu Twitteru")

2. Na stránce **Create an application** (Vytvoření aplikace) zadejte podrobnosti o nové aplikaci a pak vyberte **Create your Twitter application** (Vytvořit aplikaci Twitter).

    ![Podrobnosti o aplikaci Twitter](./media/databricks-stream-from-eventhubs/databricks-provide-twitter-app-details.png "Podrobnosti o aplikaci Twitter")

    ![Podrobnosti o aplikaci Twitter](./media/databricks-stream-from-eventhubs/databricks-provide-twitter-app-details-create.png "Podrobnosti o aplikaci Twitter")

3. Na stránce aplikace vyberte kartu **Klíče a tokeny** a zkopírujte hodnoty pro **klíč rozhraní API příjemce** a tajný klíč rozhraní API pro **spotřebitele**. Chcete-li generovat přístupové tokeny, vyberte také možnost **Vytvořit** v části **Tajný klíč přístupového tokenu a přístupového tokenu.** Zkopírujte hodnoty **Access Token** (Přístupový token) a **Access Token Secret** (Tajný klíč přístupového tokenu).

    ![Podrobnosti o aplikaci Twitter](./media/databricks-stream-from-eventhubs/twitter-app-key-secret.png "Podrobnosti o aplikaci Twitter")

Uložte hodnoty, které jste načetli pro aplikaci Twitter. Tyto hodnoty budete potřebovat v pozdější části kurzu.

## <a name="attach-libraries-to-spark-cluster"></a>Připojení knihoven ke clusteru Spark

V tomto kurzu k odesílání tweetů do služby Event Hubs použijete rozhraní Twitter API. Použijete také [konektor služby Event Hubs pro Apache Spark](https://github.com/Azure/azure-event-hubs-spark) ke čtení a zápisu dat do služby Azure Event Hubs. Pokud chcete tato api používat jako součást clusteru, přidejte je jako knihovny do Azure Databricks a přidružte je k clusteru Spark. Následující pokyny ukazují, jak přidat knihovnu.

1. V pracovním prostoru Azure Databricks vyberte **Clustery**a zvolte existující cluster Spark. V nabídce clusteru zvolte **Knihovny** a klepněte na **tlačítko Instalovat nové**.

   ![Dialogové okno Přidat knihovnu](./media/databricks-stream-from-eventhubs/databricks-add-library-locate-cluster.png "Přidání knihovny vyhledejte cluster")

   ![Dialogové okno Přidat knihovnu](./media/databricks-stream-from-eventhubs/databricks-add-library-install-new.png "Přidat knihovnu nainstalovat nové")

2. Na stránce Nová knihovna vyberte pro **Zdroj** **Maven**. Jednotlivě zadejte následující souřadnice pro konektor Spark Event Hubs a twitterové rozhraní API do **souřadnic**.

   * Konektor služby Event Hubs pro Spark – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.12`
   * Rozhraní Twitter API – `org.twitter4j:twitter4j-core:4.0.7`

3. Vyberte **Install** (Nainstalovat).

4. V nabídce clusteru zkontrolujte, zda jsou obě knihovny správně nainstalovány a připojeny.

    ![Kontrola knihoven](./media/databricks-stream-from-eventhubs/databricks-add-library-check.png "Kontrola knihoven")

6. Zopakujte tyto kroky pro balíček Twitteru `twitter4j-core:4.0.7`.

## <a name="create-notebooks-in-databricks"></a>Vytvoření poznámkových bloků v Databricks

V této části vytvoříte v pracovním prostoru Databricks dva poznámkové bloky s následujícími názvy:

- **SendTweetsToEventHub** –Poznámkový blob producenta, který použijete k získání tweetů z Twitteru a jejich streamování do služby Event Hubs.
- **ReadTweetsFromEventHub** – Poznámkový blok konzumenta, který použijete ke čtení tweetů ze služby Event Hubs.

1. V levém podokně vyberte **Pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datových cihlách](./media/databricks-stream-from-eventhubs/databricks-create-notebook.png "Vytvoření poznámkového bloku v datových cihlách")

2. V dialogovém okně **Vytvořit poznámkový blok** zadejte název **SendTweetsToEventHub**, vyberte jazyk **Scala** a vyberte cluster Spark, který jste vytvořili dříve.

    ![Vytvoření poznámkového bloku v datových cihlách](./media/databricks-stream-from-eventhubs/databricks-notebook-details.png "Vytvoření poznámkového bloku v datových cihlách")

    Vyberte **Vytvořit**.

3. Zopakováním těchto kroků vytvořte i poznámkový blok **ReadTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Odeslání tweetů do služby Event Hubs

V poznámkovém bloku **SendTweetsToEventHub** vložte následující kód a nahraďte zástupné symboly hodnotami pro obor názvů Event Hubs a aplikaci Twitter, kterou jste vytvořili dříve. Tento poznámkový blok v reálném čase streamuje tweety s klíčovým slovem Azure do služby Event Hubs.

> [!NOTE]
> Twitter API má určitá omezení požadavků a [kvóty](https://developer.twitter.com/en/docs/basics/rate-limiting.html). Pokud nejste spokojeni s omezením standardní rychlosti v rozhraní Twitter API, můžete v tomto příkladu generovat textový obsah bez použití rozhraní Twitter API. Chcete-li to provést, `test` nastavte `twitter` proměnnou **dataSource** namísto a naplňte seznam **testSource** s upřednostňovaným testovacím vstupem.

```scala
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._
    import scala.collection.immutable._
    import scala.concurrent.Future
    import scala.concurrent.ExecutionContext.Implicits.global

    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val pool = Executors.newScheduledThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sleep(time: Long): Unit = Thread.sleep(time)

    def sendEvent(message: String, delay: Long) = {
      sleep(delay)
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData)
      System.out.println("Sent event: " + message + "\n")
    }

    // Add your own values to the list
    val testSource = List("Azure is the greatest!", "Azure isn't working :(", "Azure is okay.")

    // Specify 'test' if you prefer to not use Twitter API and loop through a list of values you define in `testSource`
    // Otherwise specify 'twitter'
    val dataSource = "test"

    if (dataSource == "twitter") {

      import twitter4j._
      import twitter4j.TwitterFactory
      import twitter4j.Twitter
      import twitter4j.conf.ConfigurationBuilder

      // Twitter configuration!
      // Replace values below with you

      val twitterConsumerKey = "<CONSUMER API KEY>"
      val twitterConsumerSecret = "<CONSUMER API SECRET>"
      val twitterOauthAccessToken = "<ACCESS TOKEN>"
      val twitterOauthTokenSecret = "<TOKEN SECRET>"

      val cb = new ConfigurationBuilder()
        cb.setDebugEnabled(true)
        .setOAuthConsumerKey(twitterConsumerKey)
        .setOAuthConsumerSecret(twitterConsumerSecret)
        .setOAuthAccessToken(twitterOauthAccessToken)
        .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

      val twitterFactory = new TwitterFactory(cb.build())
      val twitter = twitterFactory.getInstance()

      // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
      val query = new Query(" #Azure ")
      query.setCount(100)
      query.lang("en")
      var finished = false
      while (!finished) {
        val result = twitter.search(query)
        val statuses = result.getTweets()
        var lowestStatusId = Long.MaxValue
        for (status <- statuses.asScala) {
          if(!status.isRetweet()){
            sendEvent(status.getText(), 5000)
          }
          lowestStatusId = Math.min(status.getId(), lowestStatusId)
        }
        query.setMaxId(lowestStatusId - 1)
      }

    } else if (dataSource == "test") {
      // Loop through the list of test input data
      while (true) {
        testSource.foreach {
          sendEvent(_,5000)
        }
      }

    } else {
      System.out.println("Unsupported Data Source. Set 'dataSource' to \"twitter\" or \"test\"")
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
```

Pokud chcete poznámkový blok spustit, stiskněte **SHIFT + ENTER**. Zobrazí se výstup podobný následujícímu fragmentu kódu. Každá událost ve výstupu představuje tweet, který obsahuje výraz Azure a ingestuje se do služby Event Hubs.

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure

    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie

    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk

    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Čtení tweetů ze služby Event Hubs

Do poznámkového bloku **ReadTweetsFromEventHub** vložte následující kód a nahraďte zástupné hodnoty hodnotami pro vaši službu Azure Event Hubs, kterou jste vytvořili dříve. Tento poznámkový blok čte tweety, které jste předtím streamovali do služby Event Hubs pomocí poznámkového bloku **SendTweetsToEventHub**.

```scala

    import org.apache.spark.eventhubs._
    import com.microsoft.azure.eventhubs._

    // Build connection string with the above information
    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new com.microsoft.azure.eventhubs.ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val customEventhubParameters =
      EventHubsConf(connStr.toString())
      .setMaxEventsPerTrigger(5)

    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

    incomingStream.printSchema

    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Zobrazí se následující výstup:


    root
     |-- body: binary (nullable = true)
     |-- offset: long (nullable = true)
     |-- seqNumber: long (nullable = true)
     |-- enqueuedTime: long (nullable = true)
     |-- publisher: string (nullable = true)
     |-- partitionKey: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+------+--------------+---------------+---------+------------+
    |body  |offset|sequenceNumber|enqueuedTime   |publisher|partitionKey|
    +------+------+--------------+---------------+---------+------------+
    |[50 75 62 6C 69 63 20 70 72 65 76 69 65 77 20 6F 66 20 4A 61 76 61 20 6F 6E 20 41 70 70 20 53 65 72 76 69 63 65 2C 20 62 75 69 6C 74 2D 69 6E 20 73 75 70 70 6F 72 74 20 66 6F 72 20 54 6F 6D 63 61 74 20 61 6E 64 20 4F 70 65 6E 4A 44 4B 0A 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 37 76 73 37 63 4B 74 76 61 68 20 0A 23 63 6C 6F 75 64 63 6F 6D 70 75 74 69 6E 67 20 23 41 7A 75 72 65]                              |0     |0             |2018-03-09 05:49:08.86 |null     |null        |
    |[4D 69 67 72 61 74 65 20 79 6F 75 72 20 64 61 74 61 62 61 73 65 73 20 74 6F 20 61 20 66 75 6C 6C 79 20 6D 61 6E 61 67 65 64 20 73 65 72 76 69 63 65 20 77 69 74 68 20 41 7A 75 72 65 20 53 51 4C 20 44 61 74 61 62 61 73 65 20 4D 61 6E 61 67 65 64 20 49 6E 73 74 61 6E 63 65 20 7C 20 23 41 7A 75 72 65 20 7C 20 23 43 6C 6F 75 64 20 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 73 4A 48 58 4E 34 74 72 44 6B]            |168   |1             |2018-03-09 05:49:24.752|null     |null        |
    +------+------+--------------+---------------+---------+------------+

    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Vzhledem k tomu, že se výstup zobrazuje v binárním režimu, pomocí následujícího fragmentu kódu ho převeďte na řetězec.

```scala
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so we cast it to string to see the actual content of the message
    val messages =
      incomingStream
      .withColumn("Offset", $"offset".cast(LongType))
      .withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType))
      .withColumn("Timestamp", $"enqueuedTime".cast(LongType))
      .withColumn("Body", $"body".cast(StringType))
      .select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Výstup teď vypadá podobně jako následující fragment kódu:

    root
     |-- Offset: long (nullable = true)
     |-- Time (readable): timestamp (nullable = true)
     |-- Timestamp: long (nullable = true)
     |-- Body: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+-----------------+----------+-------+
    |Offset|Time (readable)  |Timestamp |Body
    +------+-----------------+----------+-------+
    |0     |2018-03-09 05:49:08.86 |1520574548|Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure          |
    |168   |2018-03-09 05:49:24.752|1520574564|Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |
    |0     |2018-03-09 05:49:02.936|1520574542|@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|
    |176   |2018-03-09 05:49:20.801|1520574560|4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |
    +------+-----------------+----------+-------+
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

A to je vše! Pomocí Azure Databricks jste úspěšně streamovali data do služby Azure Event Hubs téměř v reálném čase. Následně jste streamovaná data použili pomocí konektoru služby Event Hubs pro Apache Spark. Další informace o použití konektoru služby Event Hubs pro Spark najdete v [dokumentaci ke konektorům](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete cluster ukončit. Pokud to chcete udělat, v levém podokně v pracovním prostoru Azure Databricks vyberte **Clusters** (Clustery). U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions** (Akce) a vyberte ikonu **Terminate** (Ukončit).

![Zastavení clusteru Databricks](./media/databricks-stream-from-eventhubs/terminate-databricks-cluster.png "Zastavení clusteru Databricks")

Pokud cluster ručně neukončíte, bude automaticky ukončen za předpokladu, že jste při vytváření clusteru zaškrtli políčko **Ukončit po \_ \_ minutách nečinnosti.** V takovém případě se cluster automaticky zastaví, pokud byl po zadanou dobu neaktivní.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření aplikace Twitter generující streamovaná data
> * Vytvoření poznámkových bloků v Azure Databricks
> * Přidání knihoven pro službu Event Hubs a rozhraní Twitter API
> * Odeslání tweetů do služby Event Hubs
> * Čtení tweetů ze služby Event Hubs

Přejdete k dalšímu kurzu, kde se dozvíte o provádění analýzy mínění na streamovaných datech pomocí azure databricks a [rozhraní COGNITIVE Services API](../cognitive-services/text-analytics/overview.md).

> [!div class="nextstepaction"]
>[Analýza mínění na streamovaných datech s využitím Azure Databricks](databricks-sentiment-analysis-cognitive-services.md)
