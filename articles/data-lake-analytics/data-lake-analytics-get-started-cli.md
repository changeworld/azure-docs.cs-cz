---
title: Vytvoření & dotazu Azure Data Lake Analytics – Azure CLI
description: Přečtěte si, jak pomocí rozhraní příkazového řádku Azure vytvořit účet Azure Data Lake Analytics a odeslat úlohu U-SQL.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 06/18/2017
ms.openlocfilehash: d9fc9bee98391f7272a417324b9c3a540b6adbe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474505"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli"></a>Začínáme s Azure Data Lake Analytics s využitím rozhraní Azure CLI
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Tento článek popisuje, jak používat rozhraní příkazového řádku Azure CLI k vytvoření účtů Azure Data Lake Analytics, odeslání úloh USQL a katalogů. Tato úloha načte soubor hodnot oddělených tabulátory (TSV) a převede ho na soubor hodnot oddělených čárkami (CSV). 

## <a name="prerequisites"></a>Požadavky
Než začnete, budete potřebovat tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 



## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlášení k předplatnému Azure:

```azurecli
az login
```

Zobrazí se výzva k přechodu na adresu URL a zadání ověřovacího kódu.  Pak postupujte podle pokynů k zadání přihlašovacích údajů.

Jakmile jste přihlášení, příkaz login zobrazí seznam vašich předplatných.

Použití konkrétního předplatného:

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Vytvoření účtu Data Lake Analytics
Je nutné, abyste před spuštěním jakékoli úlohy měli účet Data Lake Analytics. Pokud chcete vytvořit účet Data Lake Analytics, je nutné zadat následující položky:

* **Skupina prostředků Azure**. Účet Data Lake Analytics se musí vytvořit v rámci Skupiny prostředků Azure. [Azure Resource Manager](../azure-resource-manager/management/overview.md) umožňuje pracovat s prostředky ve vaší aplikaci jako skupina. Všechny prostředky pro aplikaci můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace.  

Výpis seznamu stávajících skupin prostředků v rámci vašeho předplatného:

```azurecli
az group list
```

Pokud chcete vytvořit novou skupinu prostředků:

```azurecli
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **Název účtu Data Lake Analytics**. Každý účtu Data Lake Analytics má název.
* **Umístění**. Použijte jedno z datových center Azure, které podporuje Data Lake Analytics.
* **Výchozí účet Data Lake Store**: Každý účet Data Lake Analytics má výchozí účet Data Lake Store.

Zobrazení seznamu stávajícího účtu Data Lake Store:

```azurecli
az dls account list
```

Vytvoření nového účtu Data Lake Store:

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

Pokud chcete vytvořit účet Data Lake Analytics, použijte následující syntaxi:

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Po vytvoření účtu můžete použít následující příkazy k vypsání seznamu účtů a zobrazení podrobností o účtu:

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Nahrání dat do Data Lake Store
V tomto kurzu zpracujete několik protokolů hledání.  Protokol hledání se dá uložit buď do úložiště Data Lake Store, nebo do úložiště objektů Azure Blob.

Azure Portal poskytuje uživatelské rozhraní pro kopírování některých ukázkových datových souborů (včetně souboru protokolu hledání) do výchozího účtu Data Lake Store. Pokud chcete nahrát data do výchozího účtu Data Lake Store, informace najdete v části [Příprava zdrojových dat](data-lake-analytics-get-started-portal.md).

Chcete-li nahrávat soubory pomocí příkazového příkazu Azure CLI, použijte následující příkazy:

```azurecli
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Data Lake Analytics má také přístup k úložišti objektů Azure Blob.  Pokud chcete nahrát data do úložiště objektů Azure Blob, informace najdete v tématu [Použití rozhraní příkazového řádku Azure s Azure Storage](../storage/common/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Odesílání úloh Data Lake Analytics
Úlohy Data Lake Analytics se píšou v jazyce U-SQL. Další informace o U-SQL najdete v tématu [Začínáme s jazykem U-SQL](data-lake-analytics-u-sql-get-started.md) a [Referenční informace pro jazyk U-SQL](https://docs.microsoft.com/u-sql/).

**Postup vytvoření skriptu úlohy Data Lake Analytics**

Vytvořte textový soubor s následujícím skriptem U-SQL a uložte ho do pracovní stanice:

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Tento skript U-SQL přečte zdrojový datový soubor pomocí **Extractors.Tsv()** a potom pomocí **Outputters.Csv()** vytvoří soubor .csv.

Pokud zdrojový soubor nekopírujete do jiného umístění, tyto dvě cesty neupravujte.  Data Lake Analytics vytvoří výstupní složku, pokud neexistuje.

U souborů uložených ve výchozích účtech Data Lake Store je jednodušší použít relativní cesty. Můžete také použít absolutní cesty.  Například:

```
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

Pro přístup k souborům v propojených účtech Storage je nutné použít absolutní cesty.  V případě souborů uložených v propojeném účtu Azure Storage je syntaxe následující:

```
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> Kontejner Azure Blob s veřejnými objekty blob není podporován.      
> Kontejner Azure Blob s veřejnými kontejnery není podporován.      
>

**Odesílání úloh**

Při odeslání úlohy použijte následující syntaxi.

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Například:

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Seznam úloh a zobrazení podrobností o úloze**

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Zrušení úloh**

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

## <a name="retrieve-job-results"></a>Načtení výsledků úlohy

Po dokončení úlohy můžete pomocí následujících příkazů zobrazit výpis výstupních souborů a soubory stáhnout:

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destination>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs download --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "<Destination Path and File Name>"
```

Například:

```azurecli
az dls fs download --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Další kroky

* Referenční dokument datového nastavení příkazu KOT Analytics najdete v tématu [Analýza datových jezer](/cli/azure/dla).
* Referenční dokument datového úložiště datových úložišti Dat Store Azure najdete v [tématu Úložiště datových jezer](/cli/azure/dls).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
