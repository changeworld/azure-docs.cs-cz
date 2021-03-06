---
title: 'Kurz: Migrace místních dat do Úložiště Azure pomocí AzCopy| Dokumenty společnosti Microsoft'
description: V tomto kurzu použijete AzCopy k migraci dat nebo kopírování dat do nebo z objektu blob, tabulky a obsahu souboru. Jednoduše migrujte data z místního úložiště do služby Azure Storage.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: f7155053072b3533503765dc6f4fbf185d21f0d4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74327523"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy

AzCopy je nástroj příkazového řádku určený ke kopírování dat do nebo z úložiště objektů blob v Azure, služby Soubory Azure a úložiště tabulek v Azure pomocí jednoduchých příkazů. Příkazy jsou navržené pro zajištění optimálního výkonu. Pomocí AzCopy můžete kopírovat data mezi systémem souborů a účtem úložiště nebo mezi účty úložiště. AzCopy je možné použít ke kopírování místních dat (v místním prostředí) do účtu úložiště.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu úložiště 
> * Použití AzCopy k nahrání veškerých dat
> * Úprava dat pro účely testování
> * Vytvoření plánované úlohy nebo úlohy Cron pro identifikaci nových souborů k nahrání

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento kurz, stáhněte si nejnovější verzi AzCopy. Viz [Začínáme s AzCopy](storage-use-azcopy-v10.md).

Pokud používáte Windows, budete potřebovat nástroj [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx), který se v tomto kurzu používá k naplánování úlohy. Uživatelé Linuxu místo toho použijí příkaz crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Prvním krokem je vytvoření kontejneru, protože objekty blob se musí vždy nahrávat do kontejneru. Kontejnery slouží jako metoda uspořádání skupin objektů blob podobně jako složky slouží k uspořádání souborů na počítači.

Podle těchto pokynů vytvořte kontejner:

1. Na hlavní stránce vyberte tlačítko **Účty úložiště** a pak vyberte účet úložiště, který jste vytvořili.
2. V části **Služby** vyberte **Objekty blob** a pak vyberte **Kontejner**.

   ![Vytvoření kontejneru](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Názvy kontejnerů musí začínat písmenem nebo číslicí. Můžou obsahovat pouze písmena, číslice a znak spojovníku (-). Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>Stažení AzCopy

Stáhněte si spustitelný soubor AzCopy V10.

- [Okna](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (dehet)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Umístěte soubor AzCopy kamkoli v počítači. Přidejte umístění souboru do proměnné systémové cesty, abyste mohli odkazovat na tento spustitelný soubor z libovolné složky v počítači.

## <a name="authenticate-with-azure-ad"></a>Ověřování pomocí Azure AD

Nejprve přiřaďte roli [přispěvatele dat objektů blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) k vaší identitě. Viz [Udělení přístupu k datům objektů blob Azure a fronty s RBAC na webu Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Potom otevřete příkazový řádek, zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login
```

Tento příkaz vrátí ověřovací kód a adresu URL webu. Otevřete web, zadejte kód a pak zvolte tlačítko **Další.**

![Vytvoření kontejneru](media/storage-use-azcopy-v10/azcopy-login.png)

Zobrazí se přihlašovací okno. V tomto okně se přihlaste ke svému účtu Azure pomocí přihlašovacích údajů účtu Azure. Po úspěšném přihlášení můžete zavřít okno prohlížeče a začít používat AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Nahrání obsahu složky do úložiště objektů blob

Pomocí AzCopy můžete nahrát všechny soubory ve složce do úložiště objektů blob ve [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) nebo v [Linuxu](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux). Pokud chcete nahrát všechny objekty blob ve složce, zadejte následující příkaz AzCopy:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Nahraďte `<local-folder-path>` zástupný symbol cestou ke složce, `C:\myFolder` `/mnt/myFolder`která obsahuje soubory (například: nebo ).

* Nahraďte `<storage-account-name>` zástupný symbol názvem svého účtu úložiště.

* Nahraďte `<container-name>` zástupný symbol názvem kontejneru, který jste vytvořili.

Chcete-li obsah zadaného adresáře nahrát rekurzivně `--recursive` do úložiště objektů Blob, zadejte možnost. Když spustíte AzCopy s touto volbou, všechny podsložky a jejich soubory jsou nahrány také.

## <a name="upload-modified-files-to-blob-storage"></a>Nahrání upravených souborů do úložiště objektů blob

Pomocí AzCopy můžete nahrávat soubory na základě času jejich poslední úpravy. 

Pokud to chcete vyzkoušet, upravte nebo vytvořte nové soubory ve zdrojovém adresáři pro účely testování. Potom použijte příkaz AzCopy. `sync`

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Nahraďte `<local-folder-path>` zástupný symbol cestou ke složce, `C:\myFolder` `/mnt/myFolder`která obsahuje soubory (například: nebo .

* Nahraďte `<storage-account-name>` zástupný symbol názvem svého účtu úložiště.

* Nahraďte `<container-name>` zástupný symbol názvem kontejneru, který jste vytvořili.

Další informace o `sync` příkazu naleznete v [tématu Synchronizace souborů](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Vytvoření plánované úlohy

Můžete vytvořit plánovanou úlohu nebo úlohu Cron, která spouští skript příkazů AzCopy. Skript v zadaném časovém intervalu identifikuje nová místní data a nahrává je do cloudového úložiště.

Zkopírujte následující příkaz AzCopy do textového editoru. Aktualizujte hodnoty parametrů příkazu AzCopy na odpovídající hodnoty. Uložte soubor jako `script.sh` (Linux) nebo `script.bat` (Windows) pro AzCopy. 

Tyto příklady předpokládají, že `myFolder`složka je pojmenována , název účtu úložiště je `mystorageaccount` a název kontejneru je `mycontainer`.

> [!NOTE]
> Příklad Linuxu připojí token SAS. Budete muset poskytnout jeden ve vašem velení. Aktuální verze AzCopy V10 nepodporuje autorizaci Azure AD v cron úlohy.

# <a name="linux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true

# <a name="windows"></a>[Windows](#tab/windows)

    azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

---

V tomto kurzu se k vytvoření plánované úlohy ve Windows používá [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx). Příkaz [Crontab](http://crontab.org/) slouží k vytvoření úlohy Cron v Linuxu.

 **Schtasks** umožňuje správci vytvářet, odstraňovat, dotazovat, měnit, spouštět a ukončovat plánované úlohy na místním nebo vzdáleném počítači. **Cron** umožňuje uživatelům Linuxu a Unixu spouštět příkazy nebo skripty v zadaném datu a čase pomocí [výrazů Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linux"></a>[Linux](#tab/linux)

Pokud chcete vytvořit úlohu Cron v Linuxu, zadejte v terminálu následující příkaz:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Zadání výrazu Cron `*/5 * * * *` v příkazu značí, že se má skript prostředí `script.sh` spouštět každých pět minut. Spouštění skriptu můžete naplánovat na určitý čas každý den, měsíc nebo rok. Další informace o nastavení data a času provádění úlohy najdete v článku o [výrazech Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windows"></a>[Windows](#tab/windows)

Pokud chcete vytvořit plánovanou úlohu ve Windows, zadejte na příkazovém řádku nebo v PowerShellu následující příkaz:

Tento příklad předpokládá, že skript je umístěn v kořenové jednotce počítače, ale skript může být kdekoli, které chcete.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

Příkaz používá:
- Parametr `/SC` k určení minutového plánu.
- Parametr `/MO` k určení pětiminutového intervalu.
- Parametr `/TN` k určení názvu úlohy.
- Parametr `/TR` k určení cesty k souboru `script.bat`.

Další informace o vytváření plánovaných úloh ve Windows najdete v tématu [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---

Pokud chcete ověřit správné spouštění plánované úlohy nebo úlohy Cron, vytvořte v adresáři `myFolder` nové soubory. Počkejte pět minut a ověřte, že se nové soubory nahrály do účtu úložiště. Přejděte do svého adresáře protokolů a zobrazte protokoly výstupu plánované úlohy nebo úlohy Cron.

## <a name="next-steps"></a>Další kroky

Další informace o způsobech přesunu místních dat do služby Azure Storage a naopak najdete na následujícím odkazu:

* [Přesuňte data do a z Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

Další informace o AzCopy naleznete v některém z těchto článků:

* [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)

* [Přenos dat pomocí úložiště Objektů blob AzCopy a objektů blob](storage-use-azcopy-blobs.md)

* [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)

* [Přenos dat pomocí kbelíků AzCopy a Amazon S3](storage-use-azcopy-s3.md)
 
* [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)
