---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0755c01fe8e13e8e39c0b453198f2b67c51a2bc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175206"
---
#### <a name="to-download-hotfixes"></a>Stažení oprav hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

1. Spusťte aplikaci [http://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Internet Explorer a přejděte na .
2. Pokud na tomto počítači používáte Katalog služby Microsoft Update poprvé, po zobrazení výzvy k instalaci doplňku Katalog služby Microsoft Update klikněte na **Nainstalovat**.

    ![Instalace katalogu](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Do vyhledávacího pole Katalogu služby Microsoft Update zadejte číslo opravy hotfix ve znalostní bázi, kterou chcete stáhnout, například **4011839**, a potom klikněte na **Vyhledat**.
   
    Zobrazí se výpis opravy hotfix, například **Cumulative Software Bundle Update 4.0 for StorSimple 8000 Series** (Kumulativní aktualizace balíčku softwaru 4.0 pro StorSimple 8000 Series).
   
    ![Prohledávání katalogu](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Klepněte na tlačítko **Stáhnout**. Zadejte místní umístění, do kterého chcete aktualizace stáhnout, nebo do něj přejděte pomocí tlačítka **Procházet**. Klikněte na soubory, které chcete stáhnout do zadaného umístění a složky. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.
5. Vyhledejte všechny další opravy hotfix uvedené ve výše uvedené tabulce (**40111841**) a stáhněte odpovídající soubory do konkrétních složek uvedených v předchozí tabulce.

> [!NOTE]
> Opravy hotfix musí být přístupné z obou řadičů, aby bylo možné zjistit případné chybové zprávy z peer řadiče.
>
> Opravy hotfix je nutné zkopírovat do 3 oddělených složek. Například aktualizace softwaru zařízení/cis/mds agenta může být zkopírována ve složce _FirstOrderUpdate,_ všechny ostatní nerušivé aktualizace mohou být zkopírovány ve složce _SecondOrderUpdate_ a aktualizace režimu údržby zkopírovány ve složce _ThirdOrderUpdate._

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Instalace a ověření oprav hotfix běžného režimu

Provedením následujících kroků nainstalujte a ověřte opravy hotfix běžného režimu. Pokud jste je již nainstalovali pomocí portálu Azure Classic, přeskočte k [instalaci a ověření oprav hotfix režimu údržby](#to-install-and-verify-maintenance-mode-hotfixes).

1. Pokud chcete nainstalovat opravy hotfix, v konzole sériového portu zařízení StorSimple spusťte rozhraní Windows PowerShell. Postupujte podle podrobných pokynů v článku [Připojení ke konzole sériového portu pomocí klienta PuTTy](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Na příkazovém řádku stiskněte **Enter**.
2. Vyberte možnost 1, **Přihlaste se s úplným přístupem**. Doporučujeme opravu hotfix nejprve nainstalovat na pasivním kontroleru.
3. Pokud chcete nainstalovat opravu hotfix, na příkazovém řádku zadejte:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    V cestě ke sdílené složce v předchozím příkazu používejte IP adresu místo DNS. Parametr Credential se používá pouze pro přístup ke sdílené složce s nutností ověření.
   
    Parametr Credential doporučujeme používat pro přístup ke sdíleným složkám. I sdílené složky otevřené všem uživatelům obvykle nejsou otevřené pro neověřené uživatele.
   
    Po zobrazení výzvy zadejte heslo.
   
    Ukázkový výstup instalace aktualizací prvního řádu najdete níž. Pro aktualizaci první objednávky je třeba přejděte na konkrétní soubor.
   
        ```
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \FirstOrderUpdate\HcsSoftwareUpdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ```
4. Po zobrazení výzvy k potvrzení instalace opravy hotfix zadejte **Y**.
5. Průběh aktualizace můžete sledovat pomocí rutiny `Get-HcsUpdateStatus`. Aktualizace se nejdříve dokončí na pasivním kontroleru. Jakmile je pasivní kontroler aktualizovaný, proběhne převzetí služeb při selhání a potom se aktualizace nainstaluje i na druhý kontroler. Aktualizace je dokončena, když jsou aktualizované oba kontrolery.
   
    Následující ukázkový výstup ukazuje probíhající aktualizaci. Když aktualizace probíhá, hodnota `RunInprogress` bude `True`.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 02/03/2017 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     Následující ukázkový výstup ukazuje dokončení aktualizace. Když se aktualizace dokončí, hodnota `RunInProgress` bude `False`.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 02/03/2017 9:15:55 AM
    LastUpdateTimestamp : 02/03/2017 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > Rutina občas hlásí `False`, i když aktualizace stále probíhá. Pokud chcete zkontrolovat, že se oprava hotfix dokončila, počkejte několik minut, znovu spusťte tento příkaz a ověřte, že hodnota `RunInProgress` je `False`. Pokud ano, oprava hotfix byla dokončena.

6. Po dokončení aktualizace softwaru zkontrolujte verze systémového softwaru. Zadejte:
   
    `Get-HcsSystem`
   
    Měly by se zobrazit následující verze:
   
   * `FriendlySoftwareVersion: StorSimple 8000 Series Update 4.0`
   * `HcsSoftwareVersion: 6.3.9600.17820`
   
     Pokud se číslo verze po instalaci aktualizace nezměnilo, znamená to, že se instalace opravy hotfix nezdařila. Pokud je to váš případ a potřebujete další pomoc, kontaktujte [podporu Microsoftu](../articles/storsimple/storsimple-contact-microsoft-support.md).
     
     > [!IMPORTANT]
     > Před použitím další aktualizace `Restart-HcsController` je nutné restartovat aktivní ovladač prostřednictvím rutiny.
     
7. Opakujte kroky 3-5 k instalaci agenta Cis/MDS staženého do složky _FirstOrderUpdate._ 
8. Opakujte kroky 3–5 a nainstalujte aktualizace druhého řádu. **Pro aktualizace druhého řádu lze nainstalovat více `Start-HcsHotfix cmdlet` aktualizací pouhým spuštěním a odkazem na složku, kde jsou umístěny aktualizace druhého řádu. Rutina provede všechny aktualizace, které jsou k dispozici ve složce.** Pokud je aktualizace již nainstalovaná, logika aktualizace to pozná a nebude ji instalovat. 

Jakmile budou nainstalované všechny opravy hotfix, použijte rutinu `Get-HcsSystem`. Verze by měly být:

   * `CisAgentVersion:  1.0.9441.0`
   * `MdsAgentVersion: 35.2.2.0`
   * `Lsisas2Version: 2.0.78.00`


#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Instalace a ověření oprav hotfix režimu údržby
Pomocí KB4011837 nainstalujte aktualizace firmwaru disku. Jedná se o narušující aktualizace a jejich dokončení trvá přibližně 30 minut. Můžete se rozhodnout je nainstalovat během naplánovaného časového období údržby pomocí připojení ke konzole sériového portu zařízení.

Poznámka: Pokud je váš firmware disku aktuální, není třeba instalovat tyto aktualizace. Z konzoly sériového portu zařízení spusťte rutinu `Get-HcsUpdateAvailability`, která zkontroluje dostupnost aktualizací a jestli se jedná o narušující (režim údržby) nebo nenarušující (běžný režim) aktualizace.

Pokud chcete nainstalovat aktualizace firmwaru disku, postupujte podle následujících pokynů.

1. Umístíte zařízení do režimu údržby. **Všimněte si, že byste neměli používat vzdálené komunikace prostředí Windows PowerShell při připojování k zařízení v režimu údržby. Místo toho spusťte tuto rutinu na ovladači zařízení při připojení přes sériovou konzolu zařízení.** Zadejte:
   
    `Enter-HcsMaintenanceMode`
   
    Ukázkový výstup najdete níž.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8600
        Name: Update4-8600-mystorsimple
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
   
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    Oba kontrolery se následně restartují do režimu údržby.
2. Pokud chcete nainstalovat aktualizaci firmwaru disku, zadejte:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Ukázkový výstup najdete níž.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\ThirdOrderUpdates\ -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Průběh instalace můžete sledovat pomocí příkazu `Get-HcsUpdateStatus`. Když se `RunInProgress` změní na `False`, aktualizace je dokončena.
4. Po dokončení instalace se kontroler, na který se instalovala oprava hotfix režimu údržby, restartuje. Přihlaste se jako možnost 1, **přihlaste se s úplným přístupem**a ověřte verzi firmwaru disku. Zadejte:
   
   `Get-HcsFirmwareVersion`
   
   Očekávané verze firmwaru disku jsou:
   
   `XMGJ, XGEG, KZ50, F6C2, VR08, N002, 0106`
   
   Ukázkový výstup najdete níž.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8600
       Name: Update4-8600-mystorsimple
       Software Version: 6.3.9600.17820
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
           ActiveBIOS:0.45.0010
              BackupBIOS:0.45.0006
              MainCPLD:17.0.000b
              ActiveBMCRoot:2.0.001F
              BackupBMCRoot:2.0.001F
              BMCBoot:2.0.0002
              LsiFirmware:20.00.04.00
              LsiBios:07.37.00.00
              Battery1Firmware:06.2C
              Battery2Firmware:06.2C
              DomFirmware:X231600
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0x9134777A
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x19
              CanisterVPDCRC:0x142F7DC2
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:1.00|1.05
              PCM1VPDStructure:0x05
              PCM1VPDCRC:0x41BEF99C
              PCM2Firmware:1.00|1.05
              PCM2VPDStructure:0x05
              PCM2VPDCRC:0x41BEF99C

           EbodFirmware
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0xB23150F8
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x14
              CanisterVPDCRC:0xBAA55828
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:3.11
              PCM1VPDStructure:0x03
              PCM1VPDCRC:0x6B58AD13
              PCM2Firmware:3.11
              PCM2VPDStructure:0x03
              PCM2VPDCRC:0x6B58AD13

           DisksFirmware
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
   
    Na druhém kontroleru spusťte příkaz `Get-HcsFirmwareVersion` a ověřte, že došlo k aktualizaci verze softwaru. Potom můžete ukončit režim údržby. Uděláte to tak, že na obou kontrolerech zařízení zadáte následující příkaz:
   
   `Exit-HcsMaintenanceMode`

5. Kontrolery se restartují, jakmile ukončíte režim údržby. Po úspěšné instalaci aktualizací firmwaru disku a ukončení režimu údržby na zařízení se vraťte na portál Azure Classic. Poznámka: Informace o instalaci aktualizací režimu údržby se na portálu může zobrazit až po 24 hodinách.

