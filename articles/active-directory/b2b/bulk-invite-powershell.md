---
title: Kurz hromadného pozvání uživatelů spolupráce B2B – Azure Active Directory | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak pomocí PowerShellu a souboru CSV odesílat hromadné pozvánky externím uživatelům spolupráce Azure AD B2B pro spolupráci.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89e24d9ff76184c36aee5c14f15f9713b30f6f1d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172657"
---
# <a name="tutorial-use-powershell-to-bulk-invite-azure-ad-b2b-collaboration-users"></a>Kurz: Použití PowerShellu k hromadnému pozvání uživatelů spolupráce Azure AD B2B

Pokud k práci se svými externími partnery používáte spolupráci B2B služby Azure Active Directory (Azure AD), můžete do organizace pozvat více uživatelů typu host najednou. V tomto kurzu se dozvíte, jak použít PowerShell k hromadnému rozeslání pozvánek externím uživatelům. Konkrétně provedete následující:

> [!div class="checklist"]
> * Připravíte soubor CSV (textový soubor s oddělovači) s informacemi o uživatelích.
> * Spustíte skript PowerShellu pro odeslání pozvánek.
> * Ověříte, že jsou uživatelé v adresáři přidaní.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 

## <a name="prerequisites"></a>Požadavky

### <a name="install-the-latest-azureadpreview-module"></a>Instalace nejnovějšího modulu AzureADPreview

Přesvědčte se, že instalujete nejnovější verzi modulu Azure AD PowerShell pro Graph (AzureADPreview). 

Nejdřív zkontrolujte, které moduly jste nainstalovali. Otevřete Windows PowerShell se zvýšenými uživatelskými oprávněními (Spustit jako správce) a spusťte následující příkaz:

```powershell
Get-Module -ListAvailable AzureAD*
```

Na základě výstupu proveďte jednu z následujících akcí:

- Pokud se nezobrazí žádné výsledky, spusťte k instalaci modulu AzureADPreview následující příkaz:
  
   ```powershell
   Install-Module AzureADPreview
   ```

- Pokud se ve výsledcích zobrazí pouze modul AzureAD, spusťte k instalaci modulu AzureADPreview tyto příkazy: 

   ```powershell
   Uninstall-Module AzureAD
   Install-Module AzureADPreview
   ```

- Pokud se ve výsledcích zobrazí pouze modul AzureADPreview, ale zobrazí se zpráva, že existuje novější verze, aktualizujte ho následujícími příkazy:

   ```powershell
   Uninstall-Module AzureADPreview
   Install-Module AzureADPreview
   ```

Může se zobrazit výzva, že modul instalujete z nedůvěryhodného úložiště. K tomu dochází, pokud jste úložiště PSGallery dříve nenastavili jako důvěryhodné. Stisknutím klávesy **Y** modul nainstalujte.

### <a name="get-test-email-accounts"></a>Získání testovacích e-mailových účtů

Potřebujete alespoň dva testovací e-mailové účty, na které můžete pozvánky odeslat. Účty se nesmí nacházet ve vaší organizaci. Můžete použít libovolný typ účtu, včetně sociálních účtů jako jsou adresy gmail.com nebo outlook.com.

## <a name="prepare-the-csv-file"></a>Příprava souboru CSV

V Microsoft Excelu vytvořte soubor CSV se seznamem jmen pozvaných uživatelů a jejich e-mailových adres. Nezapomeňte nastavit záhlaví sloupce **Name** (Jméno) a **InvitedUserEmailAddress** (E-mailová adresa pozvaného uživatele).

List vytvořte například v následujícím formátu:

![Výstup PowerShellu zobrazující zatím neschválené pozvání uživatele](media/tutorial-bulk-invite/AddUsersExcel.png)

Soubor uložte jako **C:\BulkInvite\Invitations.csv**. 

Pokud nemáte Excel, můžete soubor CSV vytvořit v libovolném textovém editoru, jako je například Poznámkový blok. Každou hodnotu oddělte čárkou a každý řádek novým řádkem. 

## <a name="sign-in-to-your-tenant"></a>Přihlášení k tenantovi

Spuštěním následujícího příkazu se připojte k doméně klienta:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```

Například, `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Po zobrazení výzvy zadejte své přihlašovací údaje.

## <a name="send-bulk-invitations"></a>Hromadné odeslání pozvánek

Pokud chcete pozvánky odeslat, spusťte následující skript PowerShellu (cesta **c:\bulkinvite\invitations.csv** je cestou k souboru CSV):

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv

$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo

$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."

foreach ($email in $invitations)
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.microsoft.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```

Tento skript odešle pozvánku e-mailovým adresám v souboru Invitations.csv. Zobrazený výstup by měl u každého uživatele vypadat přibližně takto:

![Výstup PowerShellu zobrazující zatím neschválené pozvání uživatele](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>Ověření existence uživatelů v adresáři

Pokud chcete ověřit, zda se uživatelé do Azure AD přidali, spusťte následující příkaz:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Měli byste vidět uživatele, které jste pozvali uvedené, s hlavním jménem uživatele\@(UPN) ve formátu *e-mailové adresy*#EXT#*domény*. Například *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, kde contoso.onmicrosoft.com je organizace, ze které jste odeslali pozvánky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už testovací uživatelské účty v adresáři nepotřebujete, můžete je odstranit. Uživatelský účet odstraníte spuštěním následujícího příkazu:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Příklad: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste hromadně odeslali pozvánky uživatelům typu host, kteří nepatří do vaší organizace. V dalším článku se dozvíte, jak funguje uplatnění pozvánky.

> [!div class="nextstepaction"]
> [Další informace o uplatnění pozvánky ve spolupráci B2B služby Azure AD](redemption-experience.md)
