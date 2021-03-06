---
title: Microsoft Teams na Virtuální desktopu Windows – Azure
description: Jak používat Microsoft Teams na virtuální ploše Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 15a4c9b16b102310fd02f8db3a4fb93cff84882b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314074"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Použití Microsoft Teams na virtuální ploše Windows

> Platí pro: Windows 10 a Windows 10 IoT Enterprise

Virtualizovaná prostředí představují jedinečnou sadu výzev pro aplikace pro spolupráci, jako je Microsoft Teams, včetně zvýšené latence, vysokého využití hostitelského procesoru a nízkého celkového výkonu zvuku a videa. Další informace o používání Microsoft Teams v prostředích VDI najdete v [teams for Virtualized Desktop Infrastructure](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Požadavky

Než budete moci používat Microsoft Teams na Virtuální ploše Windows, budete muset udělat tyto věci:

- Nainstalujte [klienta Windows Desktop](connect-windows-7-and-10.md) na zařízení s Windows 10, které splňuje [hardwarové požadavky](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)Microsoft Teams .
- Připojte se k virtuálnímu počítači s Windows 10 s více relacemi nebo s Windows 10 Enterprise.
- [Připravte síť](https://docs.microsoft.com/microsoftteams/prepare-network) pro Microsoft Teams.

## <a name="use-unoptimized-microsoft-teams"></a>Použití neoptimalizovaných microsoftových teams

Pomocí neoptimalizovaných microsoftových teams v prostředívirtuální chodnících Windows Virtual Desktop můžete využít funkce úplného chatu a spolupráce Microsoft Teams i zvukové hovory. Kvalita zvuku při hovorech se bude lišit v závislosti na konfiguraci hostitele, protože neoptimalizovaná volání využívají více hostitelského procesoru.

### <a name="prepare-your-image-for-teams"></a>Příprava image pro týmy

Chcete-li povolit teams per-machine instalace, nastavte následující klíč registru na hostiteli:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Instalace Microsoft Teams

Desktopovou aplikaci Teams můžete nasadit pomocí instalace pro celé počítače. Instalace Microsoft Teams do prostředí Windows Virtual Desktop:

1. Stáhněte si [balíček Teams MSI,](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) který odpovídá vašemu prostředí. Doporučujeme používat 64bitový instalační program v 64bitovém operačním systému.
2. Spusťte tento příkaz a nainstalujte MSI do hostitelského virtuálního soudu.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Tím nainstalujete Teams do programových souborů nebo programových souborů (x86). Při příštím přihlášení a spuštění teams vás aplikace požádá o vaše přihlašovací údaje.

      > [!NOTE]
      > Uživatelé a správci nemohou zakázat automatické spuštění teams během přihlášení v tuto chvíli.

      Chcete-li odinstalovat MSI z hostitelského virtuálního aplikace, spusťte tento příkaz:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Pokud nainstalujete Teams s nastavením MSI ALLUSER=1, automatické aktualizace budou zakázány. Doporučujeme, abyste týmy aktualizovali alespoň jednou měsíčně.
      
### <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Přizpůsobení vlastností protokolu Vzdálené plochy pro fond hostitelů
Přizpůsobení vlastností protokolu RDP (RdP) hostitelského fondu, jako je například možnosti více monitorů, povolení přesměrování mikrofonu a zvuku, umožňuje uživatelům poskytovat optimální prostředí na základě jejich potřeb. Vlastnosti protokolu RDP ve virtuální ploše systému Windows můžete přizpůsobit pomocí parametru **-CustomRdpProperty** v rutině **Set-RdsHostPool.**
Úplný seznam podporovaných vlastností a jejich výchozích hodnot naleznete v [části Podporovaná nastavení souborů PROTOKOLU RDP.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)
