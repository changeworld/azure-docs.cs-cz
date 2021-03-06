---
title: Federace více Azure AD s jedním AD FS - Azure
description: V tomto dokumentu se naučíte vytvořit federaci několik služeb Azure AD s jednou službou AD FS.
keywords: vytvoření federace, ADFS, AD FS, více klientů, jedna služba AD FS, jedna služba ADFS, federace s více klienty, ADFS s více doménovými strukturami, připojení AAD, federace, federace mezi klienty
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9122e3a7af2230dc0f68e72b28891d488b01a80a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65137836"
---
# <a name="federate-multiple-instances-of-azure-ad-with-single-instance-of-ad-fs"></a>Vytvoření federace několika instancí Azure AD s jednou instancí AD FS

Jedna farma AD FS s vysokou dostupností může federovat několik doménových struktur, pokud mezi nimi existuje obousměrný vztah důvěryhodnosti. Těchto několik doménových struktur může, ale nemusí odpovídat téže službě Azure Active Directory. Tento článek obsahuje pokyny pro konfiguraci federace mezi jedním nasazením služby AD FS a více než jednou doménovou strukturou, přičemž doménové struktury se synchronizují do různých služeb Azure AD.

![Federace více klientů s jednou službou AD FS](./media/how-to-connect-fed-single-adfs-multitenant-federation/concept.png)
 
> [!NOTE]
> Zpětný zápis zařízení a automatické připojení zařízení nejsou v tomto scénáři podporovány.

> [!NOTE]
> V tomto scénáři nelze ke konfigurování federace použít Azure AD Connect, protože Azure AD Connect může konfigurovat federaci pro domény v jedné službě Azure AD.

## <a name="steps-for-federating-ad-fs-with-multiple-azure-ad"></a>Kroky pro vytvoření federace AD FS s více službami Azure AD

Předpokládejme, že doména contoso.com ve službě Azure Active Directory contoso.onmicrosoft.com je již federovaná s místní službou AD FS nainstalovanou v místním prostředí služby Active Directory contoso.com. Fabrikam.com je doména ve službě Azure Active Directory fabrikam.onmicrosoft.com.

## <a name="step-1-establish-a-two-way-trust"></a>Krok 1: Vytvoření obousměrného vztahu důvěryhodnosti
 
Aby služba AD FS v doméně contoso.com mohla ověřovat uživatele v doméně fabrikam.com, je potřebný obousměrný vztah důvěryhodnosti mezi doménami contoso.com a fabrikam.com. Při vytváření obousměrného vztahu důvěryhodnosti postupujte podle pokynů v tomto [článku](https://technet.microsoft.com/library/cc816590.aspx).
 
## <a name="step-2-modify-contosocom-federation-settings"></a>Krok 2: Úprava nastavení federace contoso.com 
 
Výchozí vystavitel nastavený pro jednu doménu federován do služby AD FS je například "http\: `http://fs.contoso.com/adfs/services/trust`//ADFSServiceFQDN/adfs/services/trust". Azure Active Directory vyžaduje jedinečného vystavitele pro každou federovanou doménu. Vzhledem k tomu, že stejná služba AD FS bude federovat dvě domény, hodnota vystavitele musí být upravena, aby byla jedinečná pro každou doménu, kterou služba AD FS federuje s Azure Active Directory. 
 
Na serveru služby AD FS otevřete prostředí Azure AD PowerShell (ujistěte se, že je nainstalovaný modul MSOnline) a proveďte následující kroky:
 
Připojte se ke službě Azure Active Directory obsahující doménu contoso.com: Connect-MsolService Aktualizujte nastavení federace pro doménu contoso.com: Update-MsolFederatedDomain -DomainName contoso.com –SupportMultipleDomain
 
Vystavitel v nastavení federation domain se\:změní na "http //contoso.com/adfs/services/trust" a pravidlo deklarace vystavování bude přidáno pro azure ad předávající strany trust vydat správnou hodnotu issuerId na základě přípony UPN.
 
## <a name="step-3-federate-fabrikamcom-with-ad-fs"></a>Krok 3: Vytvoření federace domény fabrikam.com se službou AD FS
 
V relaci prostředí Azure AD PowerShell proveďte následující kroky: Připojte se ke službě Azure Active Directory, která obsahuje doménu fabrikam.com.

    Connect-MsolService
Převeďte spravovanou doménu fabrikam.com na federovanou:

    Convert-MsolDomainToFederated -DomainName fabrikam.com -Verbose -SupportMultipleDomain
 
Uvedená operace vytvoří federaci domény fabrikam.com se stejnou službou AD FS. Nastavení domény můžete ověřit pomocí příkazu Get-MsolDomainFederationSettings pro obě domény.

## <a name="next-steps"></a>Další kroky
[Připojení služby Active Directory ke službě Azure Active Directory](whatis-hybrid-identity.md)
