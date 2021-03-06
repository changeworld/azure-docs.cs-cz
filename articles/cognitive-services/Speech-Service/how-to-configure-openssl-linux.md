---
title: Jak nakonfigurovat OpenSSL pro Linux
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nakonfigurovat OpenSSL pro Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331140"
---
# <a name="configure-openssl-for-linux"></a>Konfigurace OpenSSL pro Linux

Při použití libovolné verze sady Speech SDK před 1.9.0 je [OpenSSL](https://www.openssl.org) dynamicky konfigurován na verzi hostitelského systému. V novějších verzích sady Speech SDK je OpenSSL (verze [1.1.1b)](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)staticky propojen s základní knihovnou sady Speech SDK.

Chcete-li zajistit připojení, ověřte, zda byly ve vašem systému nainstalovány certifikáty OpenSSL. Spuštění příkazu:
```bash
openssl version -d
```

Výstup na ubuntu / debianí systémy by měly být:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Zkontrolujte, `certs` zda je podadresář v openssldir. Ve výše uvedeném příkladu by `/usr/lib/ssl/certs`bylo .

* Pokud existuje `/usr/lib/ssl/certs` a obsahuje mnoho jednotlivých `.crt` `.pem` souborů certifikátů (s nebo rozšíření), není třeba další akce.

* Pokud openssldir je `/usr/lib/ssl` něco jiného než a/ nebo je jeden soubor svazku certifikátu namísto více jednotlivých souborů, je třeba nastavit odpovídající proměnnou prostředí SSL k označení, kde lze certifikáty nalézt.

## <a name="examples"></a>Příklady

- OPENSSLDIR `/opt/ssl`je . Existuje `certs` podadresář s `.crt` `.pem` mnoha nebo soubory.
Nastavte proměnnou `SSL_CERT_DIR` prostředí `/opt/ssl/certs` tak, aby ukazovala na před spuštěním programu, který používá sadu Speech SDK. Například:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR `/etc/pki/tls` je (jako na systémech založených na RHEL/CentOS). Existuje `certs` podadresář se souborem sady `ca-bundle.crt`certifikátů, například .
Před spuštěním programu, který používá sadu Speech SDK, nastavte proměnnou `SSL_CERT_FILE` prostředí tak, aby ukazovala na tento soubor. Například:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o sadě Speech SDK](speech-sdk.md)
