---
title: zahrnout soubor
description: zahrnout soubor
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/03/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: dc08dcded6418208751edbffcb5d263db059ec01
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657479"
---
### <a name="general-requirements"></a>Obecné požadavky

* Virtuální síť musí být ve stejném předplatném a stejné oblasti jako účet Batch, který použijete k vytvoření fondu.

* Fond využívající virtuální síť může mít maximálně 4 096 uzlů.

* Podsíť zadaná pro fond musí obsahovat dostatek nepřiřazených IP adres pro všechny virtuální počítače, na které fond cílí, jejichž počet je součtem vlastností `targetDedicatedNodes` a `targetLowPriorityNodes` fondu. Pokud podsíť nemá dostatek nepřiřazených IP adres, fond částečně přidělí výpočetní uzly a dojde k chybě změny velikosti.

* Všechny vlastní servery DNS obsluhující virtuální síť musí být schopné přeložit váš koncový bod služby Azure Storage. Konkrétně musí být možné přeložit adresy URL ve formátu `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` a `<account>.blob.core.windows.net`.

Další požadavky na virtuální síť se liší podle toho, jestli je fond Batch v konfiguraci virtuálního počítače nebo konfiguraci služby Cloud Services. Pro nová nasazení fondů do virtuální sítě se doporučuje konfigurace virtuálního počítače.

### <a name="pools-in-the-virtual-machine-configuration"></a>Fondy v konfiguraci virtuálního počítače

**Podporované virtuální sítě** – Pouze virtuální sítě založené na Azure Resource Manageru

**ID podsítě** – Při zadávání podsítě pomocí rozhraní API služby Batch použijte *identifikátor prostředku* podsítě. Identifikátor podsítě má tento formát:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Oprávnění** – Zkontrolujte, jestli vaše zásady zabezpečení nebo zámky na předplatném nebo skupině prostředků virtuální sítě neomezují oprávnění uživatele spravovat tuto virtuální síť.

**Další síťové prostředky** – Batch automaticky přiděluje další síťové prostředky ve skupině prostředků obsahující virtuální síť.

> [!IMPORTANT]
>Pro každých 50 vyhrazených uzlů (nebo každých 20 uzlů s nízkou prioritou) batch přiděluje: jednu skupinu zabezpečení sítě (NSG), jednu veřejnou IP adresu a jeden systém vyrovnávání zatížení. Pro tyto prostředky platí omezení [kvót prostředků](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) předplatného. U velkých fondů může být nutné požádat o zvýšení kvóty pro jeden nebo více těchto prostředků.

#### <a name="network-security-groups-batch-default"></a>Skupiny zabezpečení sítě: Výchozí dávka

Podsíť musí umožňovat příchozí komunikaci ze služby Batch, aby bylo možné plánovat úlohy na výpočetních uzlech a odchozí komunikaci pro komunikaci s Azure Storage nebo jinými prostředky podle potřeby vašeho pracovního vytížení. Pro fondy v konfiguraci virtuálního počítače Batch přidá skupiny zabezpečení sítě na úrovni síťových rozhraní (NIC) připojené k výpočetním uzlům. Tyto sítě nsg jsou konfigurovány s následujícími dalšími pravidly:

* Příchozí přenosy Protokolu TCP na portech 29876 a 29877 `BatchNodeManagement` z adres IP služby Batch, které odpovídají výrobnímu číslu.
* Příchozí provoz přes protokol TCP na portu 22 (uzly s Linuxem) nebo portu 3389 (uzly s Windows) umožňující vzdálený přístup. Pro určité typy úloh s více instancemi v Linuxu (například MPI) budete muset také povolit přenosy Port 22 SSH pro IP adresy v podsíti obsahující výpočetní uzly Batch. Tato pravidla mohou být blokována podle pravidel skupiny sítě nsg na úrovni podsítě (viz níže).
* Odchozí provoz do virtuální sítě na jakémkoli portu. To může být změněno podle pravidel skupiny nsg na úrovni podsítě (viz níže).
* Odchozí provoz na libovolném portu do Internetu. To může být změněno podle pravidel skupiny nsg na úrovni podsítě (viz níže).

> [!IMPORTANT]
> Pokud potřebujete upravit nebo přidat pravidla příchozích nebo odchozích přenosů ve skupinách zabezpečení sítě nakonfigurovaných službou Batch, postupujte obezřetně. Pokud je komunikace s výpočetními uzly v zadané podsíti zakázaná skupinou NSG, nastaví služba Batch stav výpočetních uzlů na **nepoužitelné**. Kromě toho žádné uzamčení prostředků by měla být použita na všechny prostředky vytvořené Batch, jinak to může mít za následek zabránění vyčištění prostředků v důsledku akce iniciované uživatelem, jako je například odstranění fondu.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Skupiny zabezpečení sítě: Určení pravidel na úrovni podsítě

Není nutné specifikovat skupiny zabezpečení sítě na úrovni podsítě virtuální sítě, protože batch konfiguruje vlastní skupiny zabezpečení sítě (viz výše). Pokud máte skupinu zabezpečení sítě přidruženou k podsíti, kde jsou nasazeny dávkové výpočetní uzly nebo chcete použít vlastní pravidla skupiny zabezpečení sítě k přepsání použitých výchozích hodnot, je nutné nakonfigurovat tento soubor zabezpečení sítě s alespoň příchozími a odchozími pravidly zabezpečení, jak je znázorněno v následujících tabulkách.

Nakonfigurujte příchozí provoz na portu 3389 (Windows) nebo 22 (Linux) pouze v případě, že potřebujete povolit vzdálený přístup k výpočetním uzlům z vnějších zdrojů. Možná budete muset povolit port 22 pravidla na Linuxu, pokud potřebujete podporu pro úlohy více instancí s určitými runtimes MPI. Povolení provozu na těchto portech není nezbytně nutné pro výpočetní uzly fondu, které mají být použitelné.

**Příchozí pravidla zabezpečení**

| Zdrojové IP adresy | Zdrojová značka servisu | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- | --- |
| Není dostupné. | `BatchNodeManagement`[Výrobní číslo](../articles/virtual-network/security-overview.md#service-tags) (pokud používáte místní variantu ve stejné oblasti jako váš účet Batch) | * | Všechny | 29876–29877 | TCP | Povolit |
| Ip adresy uživatelského zdroje pro vzdálený přístup k výpočetním uzlům nebo podsíti výpočetních uzlů pro úlohy linuxových více instancí, pokud je to nutné. | Není dostupné. | * | Všechny | 3389 (Windows), 22 (Linux) | TCP | Povolit |

> [!WARNING]
> Ip adresy dávkové služby se mohou v průběhu času měnit. Proto se důrazně doporučuje využít `BatchNodeManagement` servisní značku (nebo regionální variantu) pro pravidla skupiny nsg. Pravidla skupiny nsg se nedoporučuje naplnit přímo adresami IP služby Batch.

**Odchozí pravidla zabezpečení**

| Zdroj | Zdrojové porty | Cíl | Značka cílové služby | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- | --- |
| Všechny | * | [Značka služby](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(pokud používáte regionální variantu, ve stejné oblasti jako váš účet Batch) | 443 | TCP | Povolit |

### <a name="pools-in-the-cloud-services-configuration"></a>Fondy v konfigurace služby Cloud Services

**Podporované virtuální sítě** – Pouze virtuální sítě Classic

**ID podsítě** – Při zadávání podsítě pomocí rozhraní API služby Batch použijte *identifikátor prostředku* podsítě. Identifikátor podsítě má tento formát:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Oprávnění** – Instanční objekt `Microsoft Azure Batch` musí mít pro zadanou virtuální síť roli řízení přístupu na základě role `Classic Virtual Machine Contributor`.

#### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Podsíť musí umožňovat příchozí komunikaci ze služby Batch, aby mohla plánovat úlohy na výpočetních uzlech, a odchozí komunikaci kvůli komunikaci se službou Azure Storage nebo jinými prostředky.

Není potřeba zadávat skupinu zabezpečení sítě, protože Batch konfiguruje příchozí komunikaci do uzlů fondu pouze z IP adres služby Batch. Pokud má však zadaná podsíť přiřazené skupiny zabezpečení sítě nebo bránu firewall, nakonfigurujte příchozí a odchozí pravidla zabezpečení podle následujících tabulek. Pokud je komunikace s výpočetními uzly v zadané podsíti zakázaná skupinou NSG, nastaví služba Batch stav výpočetních uzlů na **nepoužitelné**.

Nakonfigurujte příchozí provoz na portu 3389 pro systém Windows, pokud potřebujete povolit přístup protokolu RDP k uzlům fondu. Uzly fondu budou použitelné i bez této konfigurace.

**Příchozí pravidla zabezpečení**

| Zdrojové IP adresy | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- |
Všechny <br /><br />Přestože to efektivně vyžaduje „povolit vše“, služba Batch použije pravidlo seznamu ACL na úrovni každého uzlu, které filtruje všechny IP adresy, které nejsou služby Batch. | * | Všechny | 10100, 20100, 30100 | TCP | Povolit |
| Volitelné, chcete-li povolit přístup k výpočetním uzlům. | * | Všechny | 3389 | TCP | Povolit |

**Odchozí pravidla zabezpečení**

| Zdroj | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- |
| Všechny | * | Všechny | 443  | Všechny | Povolit |
