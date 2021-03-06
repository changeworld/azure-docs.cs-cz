---
title: Zabezpečený přístup k datům aplikací
titleSuffix: Azure Storage
description: Použití tokenů SAS, šifrování a HTTPS k zabezpečení dat aplikace v cloudu
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: 13a2a0bcc362a13b0c42650509d356f613527cfc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061328"
---
# <a name="secure-access-to-application-data"></a>Zabezpečený přístup k datům aplikací

Tento kurz je třetí částí série. Zjistíte, jak bezpečně přistupovat k účtu úložiště. 

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Použití tokenů SAS pro přístup k obrázkům miniatur
> * Zapnutí šifrování na straně serveru
> * Povolení přenosu pouze přes protokol HTTP

[Úložiště objektů blob v Azure](../common/storage-introduction.md#blob-storage) představuje robustní službu pro ukládání souborů pro aplikace. Tento kurz rozšiřuje [předchozí téma][previous-tutorial] a ukazuje, jak bezpečně přistupovat k účtu úložiště z webové aplikace. Až budete hotovi, obrázky budou šifrované a webová aplikace bude pro přístup k obrázkům miniatur používat zabezpečené tokeny SAS.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu je nutné dokončit předchozí kurz o službě Storage: [Automatizace změny velikosti nahraných obrázků s využitím služby Event Grid][previous-tutorial].

## <a name="set-container-public-access"></a>Nastavení veřejného přístupu ke kontejneru

V této části série kurzů se pro přístup k miniaturám používají tokeny SAS. V tomto kroku nastavíte veřejný přístup ke kontejneru *thumbnails* na hodnotu `off` (vypnuto).

```azurecli-interactive 
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>Konfigurace tokenů SAS pro miniatury

V první části této série kurzů zobrazovala webová aplikace obrázky z veřejného kontejneru. V této části řady použijete tokeny sdílených přístupových podpisů (SAS) k načtení miniatur. Tokeny SAS umožňují zajistit omezený přístup ke kontejneru nebo objektu blob na základě IP adresy, protokolu, časového intervalu nebo povolených oprávnění. Další informace o SAS najdete [v tématu udělení omezeného přístupu k prostředkům Úložiště Azure pomocí sdílených přístupových podpisů (SAS).](../common/storage-sas-overview.md)

V tomto příkladu používá úložiště zdrojového kódu větev `sasTokens`, která obsahuje aktualizovaný vzorový kód. Odstraňte stávající nasazení z GitHubu pomocí příkazu [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Dále nakonfigurujte nasazení z GitHubu do webové aplikace pomocí příkazu [az webapp deployment source config](/cli/azure/webapp/deployment/source).

V následujícím příkazu je `<web-app>` název vaší webové aplikace.

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

Ve větvi `sasTokens` úložiště se aktualizuje soubor `StorageHelper.cs`. Úlohu `GetThumbNailUrls` nahradí níže uvedeným příkladem kódu. Aktualizovaná úloha načte adresy URL miniatur pomocí [objektu BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) k určení času zahájení, času vypršení platnosti a oprávnění pro token SAS. Webová aplikace teď po nasazení načítá miniatury s použitím adresy URL s tokenem SAS. Aktualizovaná úloha je znázorněná v následujícím příkladu:

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

Předchozí úloha využívá následující třídy, vlastnosti a metody:

| Třída | Vlastnosti | Metody |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[Klient blobservice](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[Klient GetBlobContainer](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[Klient blobContainer](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [Uri](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Existuje](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[Objekt blobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [Parametry ToSasQuery](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[Objekt blob](/dotnet/api/azure.storage.blobs.models.blobitem) | [Název](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[Uribuilder](/dotnet/api/system.uribuilder) | [Dotazu](/dotnet/api/system.uribuilder.query) |  |
|[Seznamu](/dotnet/api/system.collections.generic.list-1) | | [Přidat](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="server-side-encryption"></a>Šifrování na straně serveru

[Šifrování služby Azure Storage (SSE)](../common/storage-service-encryption.md) pomáhá chránit a zabezpečit vaše data. SSE šifruje neaktivní uložená data a přitom zpracovává šifrování, dešifrování a správu klíčů. Veškerá data se šifrují pomocí 256bitového [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). To je jedna z nejsilnějších dostupných variant blokového šifrování.

SSE automaticky šifruje data na všech úrovních výkonu (Standard a Premium), ve všech modelech nasazení (Azure Resource Manager a Classic) a ve všech službách Azure Storage (Blob, Queue, Table a File). 

## <a name="enable-https-only"></a>Povolení pouze HTTPS

Abyste zajistili zabezpečení požadavků na data přicházejících do a z účtu úložiště, můžete požadavky omezit pouze na HTTPS. Aktualizujte požadovaný protokol pro účet úložiště pomocí příkazu [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Otestujte připojení pomocí příkazu `curl` s použitím protokolu `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Vzhledem k tomu, že se teď vyžaduje zabezpečený přenos, zobrazí se následující zpráva:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Další kroky

Ve třetí části série jste se dozvěděli, jak zabezpečit přístup k účtu úložiště a naučili jste se například:

> [!div class="checklist"]
> * Použití tokenů SAS pro přístup k obrázkům miniatur
> * Zapnutí šifrování na straně serveru
> * Povolení přenosu pouze přes protokol HTTP

Přejděte ke čtvrté části série, kde se dozvíte, jak monitorovat a řešit potíže s aplikací cloudového úložiště.

> [!div class="nextstepaction"]
> [Monitorování a řešení potíží s aplikací cloudového úložiště](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
