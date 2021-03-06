---
title: Příprava na změnu adresy IP Protokolu SSL
description: Pokud se vaše IP adresa SSL změní, přečtěte si, co dělat, aby vaše aplikace po změně fungovala i nadále.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 34f35eb67cada6066e35227fcd6a0eaf425ac007
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672395"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Jak se připravit na změnu IP adresy SSL

Pokud jste obdrželi oznámení, že se mění IP adresa SSL vaší aplikace Azure App Service, podle pokynů v tomto článku uvolněte existující IP adresu SSL a přiřaďte novou.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Uvolnění ADRES SSL a přiřazení nových adres

1.  Otevřete [portál Azure](https://portal.azure.com).

2.  V levé navigační nabídce vyberte **Možnost Služby aplikací**.

3.  Ze seznamu vyberte aplikaci App Service.

4.  V záhlaví **Nastavení** klikněte v levém navigačním panelu na **nastavení SSL.**

1. V části SSL vyberte záznam názvu hostitele. V editoru, který se otevře, zvolte **ssl ssl** v rozevírací nabídce **SSL Type** a klepněte na tlačítko **Přidat vazbu**. Když se zobrazí zpráva o úspěchu operace, byla uvolněna existující adresa IP.

6.  V části **SSL vazby** znovu vyberte stejný název hostitele s certifikátem. V editoru, který se otevře, tentokrát zvolte **protokol SSL založený na protokolu IP** v rozevírací nabídce Typ **SSL** a klepněte na tlačítko **Přidat vazbu**. Když se zobrazí zpráva o úspěchu operace, jste získali novou IP adresu.

7.  Pokud je na portálu registrace domény (třetí strana DNS Provider nebo Azure DNS) nakonfigurován záznam DNS (záznam DNS směřující přímo na vaši IP adresu), nahraďte existující IP adresu nově vygenerovanou adresou. Novou IP adresu najdete podle pokynů v další části.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Vyhledání nové IP adresy SSL na webu Azure Portal

1.  Počkejte několik minut a pak otevřete [portál Azure](https://portal.azure.com).

2.  V levé navigační nabídce vyberte **Možnost Služby aplikací**.

3.  Ze seznamu vyberte aplikaci App Service.

4.  V záhlaví **Nastavení** klikněte v levé navigaci na **Vlastnosti** a vyhledejte oddíl s názvem **Virtuální IP adresa**.

5. Zkopírujte adresu IP a překonfigurujte záznam domény nebo mechanismus IP adresy.

## <a name="next-steps"></a>Další kroky

Tento článek vysvětluje, jak se připravit na změnu IP adresy, která byla iniciována Azure. Další informace o IP adresách ve službě Azure App Service najdete v tématu [ADRESY SSL a SSL IP ve službě Azure App Service](overview-inbound-outbound-ips.md).
