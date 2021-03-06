---
title: Příklad návodu k infrastruktuře Azure
description: Přečtěte si o klíčových pokynech pro návrh a implementaci pro nasazení ukázkové infrastruktury v Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 6040bf8862131f5a8a5564cd2f5d845fa0490a95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944907"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Příklad návodu k infrastruktuře Azure pro virtuální počítače s Linuxem
Tento článek vás provede vytvářením ukázkové aplikační infrastruktury. Podrobně popisujeme navrhování infrastruktury pro jednoduché on-line úložiště, které sdružuje všechny pokyny a rozhodnutí týkající se konvencí pojmenování, sad dostupnosti, virtuálních sítí a nástrojek pro vyrovnávání zatížení a skutečného nasazení virtuálních počítačů.

## <a name="example-workload"></a>Příklad pracovního vytížení
Adventure Works Cycles chce v Azure vytvořit on-line aplikaci pro ukládání, která se skládá z:

* Dva servery nginx s front-endem klienta ve webové vrstvě
* Dva servery nginx zpracovávající data a objednávky v aplikační vrstvě
* Dva servery MongoDB jsou součástí rozdělitého clusteru pro ukládání produktových dat a objednávek v databázové vrstvě
* Dva řadiče domény služby Active Directory pro účty zákazníků a dodavatele v ověřovací vrstvě
* Všechny servery jsou umístěny ve dvou podsítích:
  * podsíť front-end pro webové servery 
  * back-endová podsíť pro aplikační servery, cluster MongoDB a řadiče domény

![Diagram různých úrovní pro aplikační infrastrukturu](./media/infrastructure-example/example-tiers.png)

Příchozí zabezpečený webový provoz musí být vyrovnán zatížením mezi webovými servery, protože zákazníci procházejí on-line úložiště. Provoz zpracování objednávek ve formě požadavků HTTP z webových serverů musí být mezi aplikačními servery vyrovnán zatížením. Kromě toho musí být infrastruktura navržena pro vysokou dostupnost.

Výsledný průmyslový vzor musí obsahovat:

* Předplatné azure a účet
* Jedna skupina prostředků
* Spravované disky Azure
* Virtuální síť se dvěma podsítěmi
* Skupiny dostupnosti pro virtuální uživatele s podobnou rolí
* Virtual Machines

Všechny výše uvedené postupujte podle těchto zásad pojmenování:

* Adventure Works Cycles používá jako předponu **[it workload]-[location]-[Azure resource]**
  * V tomto příkladu je "**azos**" (Azure On-line Store) název úlohy IT a "**use**" (Východní USA 2) je umístění
* Virtuální sítě používají AZOS-USE-VN<strong>[číslo]</strong>
* Skupiny dostupnosti používají azos-use-as-**[role]**
* Názvy virtuálních počítačů používají azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Předplatná a účty Azure
Adventure Works Cycles používá své předplatné Enterprise s názvem Adventure Works Enterprise Subscription k poskytování fakturace pro tuto úlohu IT.

## <a name="storage"></a>Úložiště
Adventure Works Cycles určily, že by měly používat spravované disky Azure. Při vytváření virtuálních zařízení se používají obě úrovně úložiště, které jsou k dispozici:

* **Standardní úložiště** pro webové servery, aplikační servery a řadiče domény a jejich datové disky.
* **Úložiště Premium** pro servery s oddíly pevných clusterů MongoDB a jejich datové disky.

## <a name="virtual-network-and-subnets"></a>Virtuální sítě a podsítě
Vzhledem k tomu, že virtuální síť nepotřebuje trvalé připojení k místní síti Adventure Work Cycles, rozhodli se pro virtuální síť pouze pro cloud.

Vytvořili virtuální síť jenom pro cloud s následujícím nastavením pomocí portálu Azure:

* Jméno: AZOS-USE-VN01
* Umístění: Východní USA 2
* Adresní prostor virtuální sítě: 10.0.0.0/8
* První podsíť:
  * Název: FrontEnd
  * Adresní prostor: 10.0.1.0/24
* Druhá podsíť:
  * Název: BackEnd
  * Adresní prostor: 10.0.2.0/24

## <a name="availability-sets"></a>Skupiny dostupnosti
Aby byla zachována vysoká dostupnost všech čtyř úrovní jejich on-line obchodu, společnost Adventure Works Cycles rozhodla o čtyřech sadách dostupnosti:

* **azos-use-as-web** pro webové servery
* **azos-use-as-app** pro aplikační servery
* **Azos-use-as-db** pro servery v názvovém rámci MongoDB
* **azos-use-as-dc** pro řadiče domény

## <a name="virtual-machines"></a>Virtual Machines
Adventure Works Cycles se rozhodly pro své virtuální počítače Azure o následujících názvech:

* **azos-use-vm-web01** pro první webový server
* **azos-use-vm-web02** pro druhý webový server
* **azos-use-vm-app01** pro první aplikační server
* **azos-use-vm-app02** pro druhý aplikační server
* **azos-use-vm-db01** pro první server MongoDB v clusteru
* **azos-use-vm-db02** pro druhý server MongoDB v clusteru
* **azos-use-vm-dc01** pro první řadič domény
* **azos-use-vm-dc02** pro druhý řadič domény

Zde je výsledná konfigurace.

![Konečná aplikační infrastruktura nasazená v Azure](./media/infrastructure-example/example-config.png)

Tato konfigurace zahrnuje:

* Virtuální síť pouze pro cloud se dvěma podsítěmi (FrontEnd a BackEnd)
* Spravované disky Azure pomocí disků Standard i Premium
* Čtyři sady dostupnosti, jedna pro každou úroveň on-line obchodu
* Virtuální počítače pro čtyři úrovně
* Externí sada pro vyrovnávání zatížení pro webový provoz založený na protokolu HTTPS z Internetu na webové servery
* Interní sada vyrovnávání zatížení pro nešifrovaný webový provoz z webových serverů na aplikační servery
* Jedna skupina prostředků
