---
title: Transformace dat v cloudu pomocí PowerShellu
description: Tento skript Prostředí PowerShell transformuje data v cloudu spuštěním programu Spark v clusteru Azure HDInsight Spark.
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c09d0532b845472d0ccaac1ad57e3772630bb5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74932050"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShellový skript – transformace dat v cloudu pomocí Azure Data Factory

Tento ukázkový skript Prostředí PowerShell vytvoří kanál, který transformuje data v cloudu spuštěním programu Spark v clusteru Azure HDInsight Spark. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Požadavky
* **Účet Azure Storage**. Vytvořte skript pythonu a vstupní soubor a nahrajte je do úložiště Azure. V tomto účtu úložiště se ukládá výstup z programu Sparku. Cluster Spark na vyžádání používá stejný účet úložiště jako primární úložiště.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Uložení skriptu Pythonu do účtu služby Blob Storage
1. Vytvořte soubor pythonu s názvem **WordCount_Spark.py** s následujícím obsahem: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Nahraďte ** &lt;&gt; název úložiště AccountName** názvem svého účtu Azure Storage. Pak soubor uložte. 
3. Ve službě Azure Blob Storage, vytvořte kontejner nazvaný **adftutorial**, pokud ještě neexistuje. 
4. Vytvořte složku **spark**.
5. Ve složce **spark** vytvořte podsložku **script**. 
6. Do podsložky **script** uložte soubor **WordCount_Spark.py**. 


### <a name="upload-the-input-file"></a>Nahrání vstupního souboru
1. Vytvořte soubor **minecraftstory.txt** a nějakým textem. Program Sparku spočítá slova v tomto textu. 
2. Vytvořte podsložku `inputfiles` pojmenovanou ve `spark` složce kontejneru objektů blob. 
3. Do podsložky `inputfiles` uložte soubor `minecraftstory.txt`. 

## <a name="sample-script"></a>Ukázkový skript
> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují entity Data Factory (propojená služba, datová sada a kanál) na pevném disku v c:\ Složky.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní spojené prostředky:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Chcete-li odebrat objekt pro vytváření dat ze skupiny prostředků, spusťte následující příkaz: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Sada-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Vytvoření datové továrny |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Vytvoří propojenou službu v datové továrně. Propojená služba propojuje úložiště dat nebo výpočetní prostředky s továrnou na data. |
| [Kanál Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Vytvoří kanál v datové továrně. Kanál obsahuje jednu nebo více aktivit, které provádí určitou operaci. V tomto kanálu spark aktivity transformuje data spuštěním programu v clusteru Azure HDInsight Spark. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Vytvoří spuštění pro kanál. Jinými slovy, spustí potrubí. |
| [Spustit get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Získá podrobnosti o spuštění aktivity (aktivita spustit) v kanálu. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Factory PowerShell unavují [ukázky prostředí Azure Data Factory.](../samples-powershell.md)
