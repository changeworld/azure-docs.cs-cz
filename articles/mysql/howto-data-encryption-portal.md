---
title: Šifrování dat – portál Azure – Azure Database for MySQL
description: Zjistěte, jak nastavit a spravovat šifrování dat pro databázi Azure pro MySQL pomocí portálu Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: acf3e6273f98d98d5da55cfb5b044677116c44dc
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520814"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Šifrování dat pro Azure Database for MySQL pomocí portálu Azure

Přečtěte si, jak pomocí portálu Azure nastavit a spravovat šifrování dat pro azure databázi pro MySQL.

## <a name="prerequisites-for-azure-cli"></a>Požadavky pro azure cli

* Musíte mít předplatné Azure a být správcem tohoto předplatného.
* V trezoru klíčů Azure vytvořte trezor klíčů a klíč, který se použije pro klíč spravovaný zákazníkem.
* Trezor klíčů musí mít následující vlastnosti, které chcete použít jako klíč spravovaný zákazníkem:
  * [Obnovitelné odstranění](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Vyčištění chráněno](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klíč musí mít následující atributy, které chcete použít jako klíč spravovaný zákazníkem:
  * Bez data vypršení platnosti
  * Není zakázáno
  * Schopen provádět get, wrap klíč, rozbalit operace klíče

## <a name="set-the-right-permissions-for-key-operations"></a>Nastavení správných oprávnění pro klíčové operace

1. V trezoru klíčů vyberte **možnost Přístupové zásady** > **Přidat zásady přístupu**.

   ![Snímek obrazovky s trezorem klíčů se zvýrazněnými zásadami přístupu a funkceMi Přidat zásady přístupu](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Vyberte **Klíčová oprávnění**a vyberte **Možnost Získat**, **Obtékat**, **Rozbalit**a **Objekt zabezpečení**, což je název serveru MySQL. Pokud objekt zabezpečení serveru nelze nalézt v seznamu existujících objektů zabezpečení, je třeba jej zaregistrovat. Při prvním pokusu o nastavení šifrování dat se zobrazí výzva k registraci objektu zabezpečení serveru a selže.

   ![Přehled zásad přístupu](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Vyberte **Uložit**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Nastavení šifrování dat pro Azure Database for MySQL

1. V Azure Database for MySQL vyberte **šifrování dat** a nastavte klíč spravovaný zákazníkem.

   ![Snímek obrazovky s databází Azure pro MySQL se zvýrazněným šifrováním dat](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Můžete buď vybrat trezor klíčů a dvojici klíčů, nebo zadat identifikátor klíče.

   ![Snímek obrazovky s databází Azure pro MySQL se zvýrazněnými možnostmi šifrování dat](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Vyberte **Uložit**.

4. Chcete-li zajistit, aby všechny soubory (včetně dočasných souborů) byly plně zašifrovány, restartujte server.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Použití šifrování dat pro servery pro obnovení nebo repliky

Po Azure Database for MySQL je šifrována s klientem spravovaný klíč uložený v trezoru klíčů, všechny nově vytvořené kopie serveru je také šifrována. Tuto novou kopii můžete vytvořit buď prostřednictvím místní operace nebo operace geografického obnovení, nebo prostřednictvím operace repliky (místní/mezi oblastmi). Takže pro šifrovaný server MySQL můžete použít následující kroky k vytvoření šifrovaného obnoveného serveru.

1. Na serveru vyberte **možnost Obnovení přehledu** > **Restore**.

   ![Snímek obrazovky s databází Azure pro MySQL se zvýrazněnou možností Přehled a Obnovení](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Nebo pro server s podporou replikace vyberte v části **Nastavení** v záhlaví **Replikace**.

   ![Snímek obrazovky s databází Azure pro MySQL se zvýrazněnou možností Replikace](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Po dokončení operace obnovení je nový vytvořený server zašifrován pomocí klíče primárního serveru. Funkce a možnosti na serveru jsou však zakázány a server je nepřístupný. Tím se zabrání jakékoli manipulaci s daty, protože identita nového serveru ještě nebyla udělena oprávnění k přístupu do trezoru klíčů.

   ![Snímek obrazovky s databází Azure pro MySQL se zvýrazněným nepřístupným stavem](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Chcete-li server zpřístupnit, znovu ověřte klíč na obnoveném serveru. Vyberte **možnost Šifrování** > dat**Znovu ověřit klíč**.

   > [!NOTE]
   > První pokus o opětovné ověření se nezdaří, protože instanční objekt nového serveru musí mít přístup k trezoru klíčů. Chcete-li generovat instanční objekt, vyberte **možnost Znovu ověřit klíč**, který zobrazí chybu, ale vygeneruje instanční objekt. Poté naleznete [v těchto krocích](#set-the-right-permissions-for-key-operations) dříve v tomto článku.

   ![Snímek obrazovky s databází Azure pro MySQL se zvýrazněným krokem pro prodloužení platnosti](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Budete muset udělit klíč trezoru přístup k novému serveru.

4. Po registraci instančního objektu znovu obnovte klíč a server obnoví normální funkčnost.

   ![Snímek obrazovky s databází Azure pro MySQL s obnovenou funkcí](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Další kroky

 Další informace o šifrování dat najdete v [tématu Azure Database for MySQL šifrování dat pomocí klíče spravovaného zákazníkem](concepts-data-encryption-mysql.md).
