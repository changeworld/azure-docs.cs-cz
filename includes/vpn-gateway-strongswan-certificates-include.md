---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1c2525b352c25f470814ce909a8d10ff821d9e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70961594"
---
Vygenerujte certifikát certifikační autority.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

Tisk certifikátu certifikační autority ve formátu base64 Toto je formát, který je podporován Azure. Tento certifikát nahrajete do Azure jako součást [kroků konfigurace P2S](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md).

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

Vygenerujte uživatelský certifikát.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

Vygenerujte balíček p12 obsahující uživatelský certifikát. Tento balíček bude použit v dalších krocích při práci s konfiguračními soubory klienta.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```