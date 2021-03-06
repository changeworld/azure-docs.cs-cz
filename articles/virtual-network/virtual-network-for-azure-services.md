---
title: Virtuální síť pro služby Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si o výhodách nasazení prostředků do virtuální sítě. Prostředky ve virtuálních sítích mohou komunikovat mezi sebou a místní mi se kontumačně, aniž by se přecprocházel provoz po Internetu.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 70266a1280b90b4573073d633a918f701f9ee8c2
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878268"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Nasazení vyhrazených služeb Azure do virtuálních sítí

Když nasadíte vyhrazené služby Azure ve [virtuální síti](virtual-networks-overview.md), můžete komunikovat s prostředky služby soukromě, prostřednictvím privátních IP adres.

![Služby nasazené ve virtuální síti](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Nasazení služeb v rámci virtuální sítě poskytuje následující možnosti:

- Prostředky v rámci virtuální sítě mohou komunikovat mezi sebou soukromě, prostřednictvím privátních IP adres. Například přímý přenos dat mezi HDInsight a SQL Server běží na virtuálním počítači, ve virtuální síti.
- Místní prostředky mohou přistupovat k prostředkům ve virtuální síti pomocí privátních IP adres přes [síť VPN (brána VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) nebo [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuální sítě lze [peered](virtual-network-peering-overview.md) povolit prostředky ve virtuálních sítích komunikovat mezi sebou, pomocí privátní IP adresy.
- Instance služeb ve virtuální síti jsou obvykle plně spravované službou Azure. To zahrnuje sledování stavu prostředků a škálování s zatížením.
- Instance služby se nasazují do podsítě ve virtuální síti. Příchozí a odchozí přístup k síti pro podsíť musí být otevřen prostřednictvím [skupin zabezpečení sítě](security-overview.md#network-security-groups)podle pokynů poskytovaných službou.
- Některé služby také ukládají omezení pro podsíť, ve které jsou nasazeny, a omezují použití zásad, tras nebo kombinování virtuálních počítačů a prostředků služeb v rámci stejné podsítě. Obraťte se na každou službu na konkrétní omezení, jak se mohou měnit v průběhu času. Příklady takových služeb jsou Soubory Azure NetApp, Vyhrazený modul hardwarového zabezpečení, Instance kontejnerů Azure, Služba aplikací. 
- Volitelně mohou služby vyžadovat [delegovanou podsíť](virtual-network-manage-subnet.md#add-a-subnet) jako explicitní identifikátor, který může podsíť hostovat určitou službu. Delegováním získají služby explicitní oprávnění k vytvoření prostředků specifických pro službu v delegované podsíti.
- Podívejte se na příklad odpovědi rozhraní REST API ve [virtuální síti s delegovanou podsítí](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Úplný seznam služeb, které používají model delegované podsítě lze získat prostřednictvím rozhraní API [k dispozici delegování.](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list)

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Služby, které je možné nasadit do virtuální sítě

|Kategorie|Služba| Vyhrazeno<sup>1</sup>sup>1</sup> podsíť
|-|-|-|
| Služba Compute | Virtuální počítače: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Škálovací sady virtuálních strojů](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloudová služba](https://msdn.microsoft.com/library/azure/jj156091): Pouze virtuální síť (klasická)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Ne <br/> Ne <br/> Ne <br/> Č.<sup>2</sup>sup>2</sup>
| Síť | [Aplikační brána - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Brána Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Síťová virtuální zařízení](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Ano <br/> Ano <br/> Ano <br/> Ne
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ano <br/> Ano <br/> 
|Analýza | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Č.<sup>2</sup>sup>2</su<sup>2</sup>> <br/> <sup>2.</sup> <br/> 
| Identita | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Ne <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instance (ACI)](https://www.aka.ms/acivnet)<br/>[Modul plug-in Azure Container Service Engine](https://github.com/Azure/acs-engine) s [modulem plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI virtuální sítě Azure<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |Č.<sup>2</sup>sup>2</sup><br/> Ano <br/><br/> Ne <br/> Ano
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Webové aplikace](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ano <br/> Ano <br/> Ano <br/> Ano
| Hostil | [Rezervované HSM Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ano <br/> Ano <br/>
| | |

<sup>1</sup> "Vyhrazené" znamená, že v této podsíti lze nasadit pouze prostředky specifické pro služby a nelze je kombinovat se zákazníky v oblasti virtuálních mís/virtuálních měn. <br/> 
<sup>2</sup> Doporučuje se mít tyto služby ve vyhrazené podsíti jako osvědčený postup, nikoli však povinný požadavek uložený službou.
