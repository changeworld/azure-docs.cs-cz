---
title: 'Úvodní příručka: Vytvoření a správa topologie Apache Storm – Azure HDInsight'
description: Na úvodním startu se dozvíte, jak vytvořit a monitorovat topologii Apache Storm v Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 24173b553f30f652caf20b1ec7500fd9c4d2f7a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73241220"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Úvodní příručka: Vytvoření a monitorování topologie Apache Storm v Azure HDInsight

Apache Storm je škálovatelný výpočetní systém v reálném čase odolný proti chybám, distribuovaný určený pro zpracování datových proudů. Pomocí Storm v Azure HDInsight můžete vytvořit cloudový cluster Storm, který bude provádět analýzy velkých objemů dat v reálném čase.

V tomto rychlém startu použijete příklad z projektu Apache [storm-starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) k vytvoření a monitorování topologie Apache Storm do existujícího clusteru Apache Storm.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Storm na HDInsight. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **Storm** pro **typ clusteru**.

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Vytvoření topologie

1. Připojte se ke clusteru Storm. Upravte níže uvedený `CLUSTERNAME` příkaz nahrazením názvem clusteru Storm a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Příklad **WordCount** je součástí clusteru `/usr/hdp/current/storm-client/contrib/storm-starter/`HDInsight na adrese . Topologie generuje náhodné věty a spočítá, kolikrát se slova vyskytují. K spuštění topologie **počtu slov** v clusteru použijte následující příkaz:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Monitorování topologie

Storm poskytuje webové rozhraní pro práci s běžící topologie a je součástí clusteru HDInsight.

Ke sledování topologie pomocí uživatelského rozhraní Storm použijte následující kroky:

1. Pokud chcete zobrazit uživatelské rozhraní Storm, otevřete ve webovém prohlížeči adresu `https://CLUSTERNAME.azurehdinsight.net/stormui`. Nahraďte `CLUSTERNAME` názvem svého clusteru.

2. V části **Souhrn topologie**vyberte položku **počtu slov** ve sloupci **Název.** Zobrazí se další informace o topologii.

    ![Řídicí panel Storm s informacemi o topologii Storm Starter WordCount.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    Nová stránka obsahuje následující informace:

    |Vlastnost | Popis |
    |---|---|
    |Topologie statistiky|Základní informace o výkonu topologie uspořádané do časových oken. Výběrem konkrétního časového okna změníte časové okno informací zobrazených v dalších částech stránky.|
    |Hubičky|Základní informace o výtocích, včetně poslední chyby vrácené každým výtokem.|
    |Šrouby|Základní informace o šroubech.|
    |Konfigurace topologie|Podrobné informace o konfiguraci topologie.|
    |Aktivovat|Obnoví zpracování deaktivované topologie.|
    |Deaktivovat|Pozastaví spuštěnou topologii.|
    |Přehodnotit|Upravuje paralelismus topologie. Po změně počtu uzlů v clusteru musíte znovu vyvážit spuštěné topologie. Nové vyvážení upraví paralelismus, aby se vykompenzovalo zvýšení nebo snížení počtu uzlů v clusteru. Další informace naleznete [v tématu Principy paralelismu topologie Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Zabít|Ukončí topologii bouře po zadaném časovém udůsledku.|

3. Na této stránce vyberte položku z oddílu **Spouts** nebo **Bolts**. Zobrazí se informace o vybrané komponentě.

    ![Řídicí panel Storm s informacemi o vybraných součástech.](./media/apache-storm-quickstart/hdi-component-summary.png)

    Na nové stránce se zobrazí následující informace:

    |Vlastnost | Popis |
    |---|---|
    |Statistiky výtoku/šroubů|Základní informace o výkonu komponenty uspořádané do časových oken. Výběrem konkrétního časového okna změníte časové okno informací zobrazených v dalších částech stránky.|
    |Vstupní statistiky (pouze šroub)|Informace o součástech, které vytvářejí data spotřebovaná šroubem.|
    |Výstupní statistiky|Informace o údajích vyzařovaných tímto šroubem.|
    |Exekutoři|Informace o instancích této součásti.|
    |chyby|Chyby způsobené touto komponentou.|

4. Chcete-li zobrazit podrobnosti pro konkrétní instanci komponenty, při zobrazení podrobností o funkcích spout nebo bolt vyberte položku ze sloupce **Port** v oddílu **Vykonavatelé**.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    V tomto příkladu se slovo **seven** vyskytlo 1493957krát. Tolikrát bylo toto slovo zjištěno od spuštění této topologie.

## <a name="stop-the-topology"></a>Zastavení topologie

Vraťte se na stránku **Souhrn topologie**, kde naleznete topologii počtu slov a pak vyberte tlačítko **Zastavit** z oddílu **Topologie akce**. Po zobrazení výzvy zadejte hodnotu 10 jako počet sekund, po které se má počkat před zastavením topologie. Po uplynutí časového limitu se topologie už při návštěvě oddílu **Uživatelské rozhraní Storm** řídicího panelu nezobrazí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Pokud chcete odstranit cluster, přečtěte si informace [o odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového příkazu k Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili příklad z projektu Apache [storm-starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) k vytvoření a monitorování topologie Apache Storm do existujícího clusteru Apache Storm. Přejdete k dalšímu článku, kde se dozvíte základy správy a monitorování topologie Apache Storm.

> [!div class="nextstepaction"]
>[Nasazení a správa topologie Apache Storm na Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)