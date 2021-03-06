---
title: 'Ověřování mezi službami: Python s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak dosáhnout ověřování mezi službami pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí Pythonu
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 009aff2703829e6d30f93b3c8e3696724594f29b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260290"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Ověřování mezi službami pomocí Azure Data Lake Storage Gen1 pomocí Pythonu
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

V tomto článku se dozvíte, jak pomocí sady Python SDK provést ověřování mezi službami pomocí Azure Data Lake Storage Gen1. Ověření u koncových uživatelů pomocí modulu Gen1 úložiště datového jezera pomocí Pythonu najdete [v tématu Ověřování koncových uživatelů pomocí data lake storage gen1 pomocí Pythonu](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte "webovou" aplikaci služby Azure Active Directory**. Kroky ověřování mezi službami musíte provést [pomocí služby Data Lake Storage Gen1 pomocí služby Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalace modulů

Chcete-li pracovat s Datovým lakem Storage Gen1 pomocí Pythonu, musíte nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* Modul, `azure-mgmt-datalake-store` který zahrnuje operace správy účtu Data Lake Storage Gen1. Další informace o tomto modulu najdete v [tématu Azure Data Lake Storage Gen1 Management odkaz na modul](/python/api/azure-mgmt-datalake-store/).
* Modul, `azure-datalake-store` který zahrnuje operace souborového systému Data Lake Storage Gen1. Další informace o tomto modulu naleznete v [tématu azure-datalake-store Filesystem reference](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Pomocí následujících příkazů tyto moduly nainstalujte.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

1. Pomocí integrovaného vývojového prostředí (IDE) podle vašeho výběru vytvořte novou aplikaci v Pythonu, například **mysample.py**.

2. Přidáním následujícího fragmentu kódu importujte požadované moduly.

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Uložte změny v souboru mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Ověřování služba-služba s tajným klíčem klienta pro správu účtu

Tento fragment použijte k ověření pomocí Služby Azure AD pro operace správy účtů na webu Data Lake Storage Gen1, jako je vytvoření účtu Data Lake Storage Gen1, odstranění účtu Data Lake Storage Gen1 atd. Následující fragment fragmentu lze použít k ověření aplikace neinteraktivně pomocí tajného klíče klienta pro instanční objekt aplikace nebo služby existující aplikace Azure AD "Web App".

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Ověřování služba-služba s tajným klíčem klienta pro operace se systémem souborů

Pomocí následujícího fragmentu příkazu k ověření pomocí Služby Azure AD pro operace souborového systému na webu Data Lake Storage Gen1, jako je vytvoření složky, nahrávání souborů atd. Následující fragment fragmentu lze použít k ověření aplikace neinteraktivně pomocí tajného klíče klienta pro instanční objekt aplikace / služby. Použijte tento fragment kódu se stávající aplikací Azure AD Webová aplikace.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak používat ověřování služby ke službě k ověření pomocí Data Lake Storage Gen1 pomocí Pythonu. Nyní se můžete podívat na následující články, které hovoří o tom, jak používat Python pro práci s Datovým lakem Storage Gen1.

* [Operace správy účtů v zařízení Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-get-started-python.md)
* [Datové operace na Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-data-operations-python.md)


