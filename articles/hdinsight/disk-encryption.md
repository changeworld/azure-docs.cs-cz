---
title: Šifrování disku spravované zákazníkem pro Azure HDInsight
description: Tento článek popisuje, jak pomocí vlastního šifrovacího klíče z Azure Key Vault šifrovat data uložená na spravovaných discích v clusterech Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c0521f384a333c3054397fb0ec7c2ab907e54f67
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411755"
---
# <a name="customer-managed-key-disk-encryption"></a>Šifrování disků s využitím klíčů spravovaných zákazníky

Azure HDInsight podporuje šifrování klíčů spravovaných zákazníky pro data na spravovaných discích a disky prostředků připojené k virtuálním počítačům clusteru HDInsight. Tato funkce umožňuje používat Azure Key Vault ke správě šifrovacích klíčů, které zabezpečují data v klidovém stavu ve vašich clusterech HDInsight.

Všechny spravované disky ve službě HDInsight jsou chráněné šifrováním služby Azure Storage Service (SSE). Ve výchozím nastavení jsou data na těchto discích šifrována pomocí klíčů spravovaných společností Microsoft. Pokud povolíte klíče spravované zákazníkem pro HDInsight, poskytnete šifrovací klíče pro HDInsight k použití a správě těchto klíčů pomocí služby Azure Key Vault.

Tento dokument neřeší data uložená ve vašem účtu Azure Storage. Další informace o šifrování Azure Storage najdete v [tématu šifrování Azure Storage pro data v klidovém stavu](../storage/common/storage-service-encryption.md). Vaše clustery mohou mít jeden nebo více připojených účtů azure storage, kde šifrovací klíče může být také microsoft spravované nebo spravované zákazníkem, ale šifrovací služba se liší.

## <a name="introduction"></a>Úvod

Šifrování klíčů spravované zákazníkem je jednostupňový proces zpracovávaný během vytváření clusteru bez dalších nákladů. Jediné, co musíte udělat, je zaregistrovat HDInsight jako spravovanou identitu pomocí služby Azure Key Vault a přidat šifrovací klíč při vytváření clusteru.

Disk y prostředků i spravované disky na každém uzlu clusteru jsou šifrovány symetrickým datovým šifrovacím klíčem (DEK). DEK je chráněn pomocí klíče šifrovací klíč (KEK) z trezoru klíčů. Procesy šifrování a dešifrování jsou zpracovávány výhradně azure HDInsight.

Pokud je brána firewall trezoru klíčů povolena v trezoru klíčů, kde je uložen šifrovací klíč disku, musí být do konfigurace brány firewall trezoru klíčů přidány ip adresy místního zprostředkovatele prostředků HDInsight pro oblast, kde bude cluster nasazen. To je nezbytné, protože HDInsight není důvěryhodná služba trezoru klíčů Azure.

Pomocí portálu Azure nebo velnám Azure CLI můžete bezpečně otočit klíče v trezoru klíčů. Když se klíč otáčí, cluster HDInsight začne nový klíč používat během několika minut. Povolte funkce ochrany klíčů [Funkce obnovitelného odstranění,](../key-vault/key-vault-ovw-soft-delete.md) které chrání před scénáři ransomwaru a náhodným odstraněním. Trezory klíčů bez této funkce ochrany nejsou podporovány.

|Typ clusteru |Disk operačního systému (spravovaný disk) |Datový disk (spravovaný disk) |Dočasný datový disk (místní SSD) |
|---|---|---|---|
|Kafka, HBase s akcelerované píše|[Šifrování SSE](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|Šifrování SSE + Volitelné šifrování CMK|Volitelné šifrování CMK|
|Všechny ostatní clustery (Spark, Interactive, Hadoop, HBase bez zrychlených zápisů)|Šifrování SSE|Není dostupné.|Volitelné šifrování CMK|

## <a name="get-started-with-customer-managed-keys"></a>Začínáme s klíči spravovanými zákazníky

Chcete-li vytvořit cluster HDInsight s podporou klíčů spravovaný zákazníkem, provedeme následující kroky:

1. Vytvoření spravovaných identit pro prostředky Azure
1. Vytvoření trezoru klíčů Azure
1. Vytvořit klíč
1. Vytvořit zásady přístupu
1. Vytvoření clusteru HDInsight s povoleným klíčem spravovaným zákazníkem
1. Otočení šifrovacího klíče

## <a name="create-managed-identities-for-azure-resources"></a>Vytvoření spravovaných identit pro prostředky Azure

Vytvořte spravovanou identitu přiřazenou uživatelem k ověření v trezoru klíčů.

Viz [Vytvoření spravované identity přiřazené uživateli](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) pro konkrétní kroky. Další informace o tom, jak spravované identity fungují ve Službě Azure HDInsight, najdete [v tématu Spravované identity v Azure HDInsight](hdinsight-managed-identities.md). Nezapomeňte uložit ID prostředku spravované identity při přidání do zásadpřístupu trezoru klíčů.

## <a name="create-azure-key-vault"></a>Vytvoření trezoru klíčů Azure

Vytvoření trezoru klíčů Konkrétní kroky najdete [v tématu Vytvoření trezoru klíčů Azure.](../key-vault/quick-create-portal.md)

HDInsight podporuje jenom Azure Key Vault. Pokud máte vlastní trezor klíčů, můžete importovat klíče do azure key vault. Nezapomeňte, že v trezoru klíčů musí být povoleno **obnovitelné odstranění.** Další informace o importu existujících klíčů naleznete na stránce [O klíčích, tajných klíčích a certifikátech](../key-vault/about-keys-secrets-and-certificates.md).

## <a name="create-key"></a>Vytvořit klíč

1. Z nového trezoru klíčů přejděte na **Nastavení** > **kláves** > **+ Generovat/importovat**.

    ![Generovat nový klíč v azure key vault](./media/disk-encryption/create-new-key.png "Generovat nový klíč v azure key vault")

1. Zadejte název a vyberte **Vytvořit**. Udržovat výchozí **typ klíče** **RSA**.

    ![generuje název klíče](./media/disk-encryption/create-key.png "Generovat název klíče")

1. Po návratu na stránku **Klíče** vyberte klíč, který jste vytvořili.

    ![seznam klíčů trezoru klíčů](./media/disk-encryption/key-vault-key-list.png)

1. Vyberte verzi, kterou chcete otevřít na stránce **Verze klíče.** Pokud používáte vlastní klíč pro šifrování clusteru HDInsight, musíte zadat identifikátor URI klíče. Zkopírujte **identifikátor klíče** a někam ho uložte, dokud nebudete připraveni vytvořit cluster.

    ![získat identifikátor klíče](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Vytvořit zásady přístupu

1. V novém trezoru klíčů přejděte na**zásady** >  **Přístupu** > k nastavení **+ Přidat zásady přístupu**.

    ![Vytvoření nových zásad přístupu k úložišti klíčů Azure](./media/disk-encryption/key-vault-access-policy.png)

1. Na stránce **Přidat zásady přístupu** zadejte následující informace:

    |Vlastnost |Popis|
    |---|---|
    |Klíčová oprávnění|Vyberte **Získat**, **Rozbalit klíč**a **Zalomit klíč**.|
    |Tajná oprávnění|Vyberte **získat**, **nastavit**a **odstranit**.|
    |Vybrat jistinu|Vyberte uživatelem přiřazenou spravovanou identitu, kterou jste vytvořili dříve.|

    ![Nastavit zásadu přístupu k úložišti klíčů Azure Pro Azure Key Vault](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Vyberte **Přidat**.

1. Vyberte **Uložit**.

    ![Uložení zásad přístupu k úložišti klíčů Azure](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Vytvoření clusteru pomocí šifrování disku spravovaného zákazníkem

Nyní jste připraveni vytvořit nový cluster HDInsight. Klíč spravovaný zákazníkem lze použít pouze pro nové clustery během vytváření clusteru. Šifrování nelze odebrat z clusterů klíčů spravovaných zákazníkem a klíč spravovaný zákazníkem nelze přidat do existujících clusterů.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Během vytváření clusteru zadejte úplný **identifikátor klíče**, včetně verze klíče. Například, `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Je také nutné přiřadit spravovanou identitu ke clusteru a poskytnout identifikátor URI klíče.

![Vytvořit nový cluster](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Použití Azure CLI

Následující příklad ukazuje, jak pomocí rozhraní příkazového příkazu Azure vytvořit nový cluster Apache Spark s povoleným šifrováním disku. Další informace naleznete v [tématu Azure CLI az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>Otočení šifrovacího klíče

Mohou existovat scénáře, kde můžete chtít změnit šifrovací klíče používané clusterem HDInsight po jeho vytvoření. To lze snadno přes portál. Pro tuto operaci musí mít cluster přístup k aktuálnímu klíči i k zamýšlenému novému klíči, jinak se operace otočení klíče nezdaří.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Chcete-li klíč otočit, potřebujete identifikátor URI úložiště základního klíče. Jakmile to uděláte, přejděte do části vlastností clusteru HDInsight na portálu a klikněte na **Změnit klíč** v části Adresa **URL šifrovacího klíče disku**. Zadejte novou adresu URL klíče a odešlete jej otočit.

![otočení šifrovacího klíče disku](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Použití Azure CLI

Následující příklad ukazuje, jak otočit klíč šifrování disku pro existující cluster HDInsight. Další informace naleznete v [tématu Azure CLI az hdinsight rotate-disk-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Chcete-li používat klíče spravované zákazníky pomocí šablony Správce prostředků, aktualizujte šablonu následujícími změnami:

1. V souboru **azuredeploy.json** přidejte do objektu prostředků následující vlastnost:

    ```json
       "diskEncryptionProperties":
         {
                 "vaultUri": "[parameters('diskEncryptionVaultUri')]",
                  "keyName": "[parameters('diskEncryptionKeyName')]",
                  "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
                   "msiResourceId": "[parameters('diskEncryptionMsiResourceId')]"
         }

1. In the **azuredeploy.parameters.json** file, add the following parameters. You can get the values of these parameters from the Key Vault URI and the managed Identity. For example, if you have the following URI and identity values,
    * Sample key vault URI: https://<KeyVault_Name>.vault.azure.net/keys/clusterkey/<Cluster_Key_Value>
    * Sample user-assigned managed identity: "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>

    The parameters in the **azuredeploy.parameters.json** file are:

    ```json
   "diskEncryptionVaultUri": {
            "value": "https://<KeyVault_Name>.vault.azure.net"
        },
        "diskEncryptionKeyName": {
            "value": "clusterkey"
        },
        "diskEncryptionKeyVersion": {
            "value": "<Cluster_Key_Value>"
        },
        "diskEncryptionMsiResourceId": {
            "value": "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>"
        }
    ```

## <a name="faq-for-customer-managed-key-encryption"></a>Nejčastější dotazy k šifrování klíčů spravovaným zákazníkem

**Jak cluster HDInsight přistupuje k trezoru klíčů?**

HDInsight přistupuje k vaší instanci Azure Key Vault pomocí spravované identity, kterou přidružíte k clusteru HDInsight. Tuto spravovanou identitu lze vytvořit před nebo během vytváření clusteru. Je také nutné udělit spravovanou identitu přístup do trezoru klíčů, kde je uložen klíč.

**Je tato funkce dostupná pro všechny clustery na HDInsight?**

Šifrování klíčů spravovaných zákazníkem je k dispozici pro všechny typy clusterů kromě Spark 2.1 a 2.2.

**Mohu k šifrování různých disků nebo složek použít více klíčů?**

Ne, všechny spravované disky a disky prostředků jsou šifrovány stejným klíčem.

**Co se stane, když cluster ztratí přístup k trezoru klíčů nebo ke klíči?**

Pokud cluster ztratí přístup ke klíči, zobrazí se upozornění na portálu Apache Ambari. V tomto stavu se operace **Změnit klíč** nezdaří. Po obnovení přístupu ke klíčům zmizí upozornění Ambari a operace, jako je například střídání klíčů, lze úspěšně provést.

![upozornění ambari pro přístup ke klíči](./media/disk-encryption/ambari-alert.png)

**Jak lze obnovit cluster, pokud jsou klíče odstraněny?**

Vzhledem k tomu, že jsou podporovány pouze "Obnovitelné odstranění" povolené klíče, pokud jsou klíče obnoveny v trezoru klíčů, cluster by měl znovu získat přístup ke klíčům. Chcete-li obnovit klíč Azure Key Vault, naleznete [v tématu Zpět-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) nebo [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Které typy disků jsou šifrovány? Jsou disky operačního systému nebo disky prostředků také šifrovány?**

Disky prostředků a datové/spravované disky jsou šifrovány. Disky operačního systému nejsou šifrovány.

**Pokud je cluster škálován, budou nové uzly bez problémů podporovat klíče spravované zákazníkem?**

Ano. Cluster potřebuje přístup ke klíči v trezoru klíčů během škálování. Stejný klíč se používá k šifrování spravovaných disků i disků prostředků v clusteru.

**Jsou v mé lokalitě k dispozici klíče spravované zákazníkem?**

Klíče spravované zákazníkem HDInsight jsou k dispozici ve všech veřejných cloudech a národních cloudech.

## <a name="next-steps"></a>Další kroky

* Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault](../key-vault/key-vault-overview.md).
* [Přehled podnikového zabezpečení v Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
