---
title: 'Kurz: Vytvoření záznamu aliasu Azure DNS, který bude odkazovat na veřejnou IP adresu Azure'
description: V tomto kurzu se dozvíte, jak nakonfigurovat záznam aliasu Azure DNS tak, aby odkazoval na veřejnou IP adresu Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: d3017d09e94040d16950598dad360fe32930c16b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985435"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Kurz: Konfigurace záznamu aliasu odkazujícího na veřejnou IP adresu Azure 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření síťové infrastruktury
> * Vytvořte virtuální počítač webového serveru s veřejnou IP adresou.
> * Vytvořte záznam aliasu, který odkazuje na veřejnou IP adresu.
> * Test záznamu aliasu


Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky
Musíte mít k dispozici název domény, kterou můžete hostovat v Azure DNS a použít k testování. Musíte mít úplnou kontrolu nad touto doménou. Úplná kontrola zahrnuje možnost nastavit pro doménu záznamy názvového serveru (NS).

Pokyny k hostování domény v Azure DNS najdete v [kurzu hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md).

Ukázková doména použitá v tomto kurzu je contoso.com, ale použijte vlastní název domény.

## <a name="create-the-network-infrastructure"></a>Vytvoření síťové infrastruktury
Nejprve vytvořte virtuální síť a podsíť, do které umístíte webové servery.
1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
2. V levém horním rohu portálu vyberte **Vytvořit prostředek**. Do vyhledávacího pole zadejte *skupina prostředků* a vytvořte skupinu prostředků **RG-DNS-Alias-pip**.
3. Vyberte**Networking** > možnost Vytvořit**síť**ovou síť **prostředků** > .
4. Vytvořte virtuální síť **VNet-Server**. Umístěte ji do skupiny prostředků **RG-DNS-Alias-pip** a podsíť pojmenujte **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Vytvoření virtuálního počítače s webovým serverem
1. Vyberte **Vytvořit prostředek** > **virtuálního připojení windows server 2016**.
2. Jako název zadejte **Web-01** a umístěte virtuální počítač do skupiny prostředků **RG-DNS-Alias-TM**. Zadejte uživatelské jméno a heslo a vyberte **OK**.
3. Jako **Velikost** vyberte skladovou položku s 8 GB paměti RAM.
4. V části **Nastavení** vyberte virtuální síť **VNet-Servers** a podsíť **SN-Web**. U veřejných příchozích portů vyberte **HTTP** > **HTTPS** > **RDP (3389)** a pak vyberte **OK**.
5. Na stránce **Souhrn** vyberte **Vytvořit**.

Dokončení tohoto postupu trvá několik minut. Virtuální počítač bude mít připojenou síťovou síťovou karty, která bude mít základní dynamickou veřejnou IP adresu nazvanou Web-01-ip. Veřejná IP adresa se změní při každém restartování virtuálního počítače.

### <a name="install-iis"></a>Instalace služby IIS

Na virtuální počítač **Web-01** nainstalujte službu IIS.

1. Připojte se k **webu Web-01**a přihlaste se.
2. Na řídicím panelu **Správce serveru** vyberte **Přidat role a funkce**.
3. Třikrát vyberte **Další**. Na stránce **Role serveru** vyberte **Webový server (IIS)**.
4. Vyberte **Přidat funkce** a pak **Další**.
5. Čtyřikrát vyberte **Další** a pak vyberte **Nainstalovat**. Dokončení tohoto postupu trvá několik minut.
6. Po dokončení instalace vyberte **Zavřít**.
7. Otevřete webový prohlížeč. Přejděte na adresu **localhost** a ověřte, že se zobrazí výchozí webová stránka služby IIS.

## <a name="create-an-alias-record"></a>Vytvoření záznamu aliasu

Vytvořte záznam aliasu, který odkazuje na veřejnou IP adresu.

1. Výběrem zóny Azure DNS ji otevřete.
2. Vyberte **Sada záznamů**.
3. V textovém poli **Název** vyberte **web01**.
4. Jako **Typ** ponechte záznam **A**.
5. Vyberte zaškrtávací políčko **Sada záznamů aliasů**.
6. Vyberte **Zvolit službu Azure** a vyberte veřejnou IP adresu **Web-01-ip**.

## <a name="test-the-alias-record"></a>Test záznamu aliasu

1. Ve skupině prostředků **RG-DNS-Alias-pip** vyberte virtuální počítač **Web-01**. Poznamenejte si veřejnou IP adresu.
1. Ve webovém prohlížeči přejděte na plně kvalifikovaný název domény virtuálního počítače Web01-01. Příklad: **web01.contoso.com**. Teď se zobrazí výchozí webová stránka služby IIS.
2. Zavřete webový prohlížeč.
3. Zastavte virtuální počítač **Web-01** a pak ho restartujte.
4. Jakmile se virtuální počítač restartuje, poznamenejte si jeho novou veřejnou IP adresu.
5. Otevřete nový prohlížeč. Znovu přejděte na plně kvalifikovaný název domény virtuálního počítače Web01-01. Příklad: **web01.contoso.com**.

Tento postup proběhne úspěšně, protože jste použili záznam aliasu odkazující na prostředek veřejné IP adresy, a ne standardní záznam A.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky vytvořené pro účely tohoto kurzu nepotřebujete, odstraňte skupinu prostředků **RG-DNS-Alias-pip**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili záznam aliasu odkazující na veřejnou IP adresu Azure. Další informace o Azure DNS a webových aplikacích získáte v kurzu o webových aplikacích.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)
