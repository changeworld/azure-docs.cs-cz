---
title: 'PowerShell: Nahrávání souborů pomocí protokolu FTP'
description: Zjistěte, jak pomocí Azure PowerShellu automatizovat nasazení a správu služby App Service. Tato ukázka ukazuje, jak nahrát soubory do aplikace pomocí protokolu FTP.
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 1dfcbec9530a66570244e8168faa84bfe0759038
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74685092"
---
# <a name="upload-files-to-a-web-app-using-ftp"></a>Nahrání souborů do webové aplikace pomocí FTP

Tento ukázkový skript vytvoří ve službě App Service webovou aplikaci se souvisejícími prostředky a pak pomocí FTP nasadí kód vaší webové aplikace (pomocí metody [WebClient.UploadFile()](https://msdn.microsoft.com/library/ms144229.aspx)).

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "Upload files to a web app using FTP")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, webovou aplikaci a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nový-AzAppServicePlán](/powershell/module/az.websites/new-azappserviceplan) | Vytvoří plán služby App Service. |
| [Nový-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří webovou aplikaci. |
| [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) | Získá profil publikování webové aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
