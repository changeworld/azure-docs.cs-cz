---
title: Kurz – zřízení zařízení pomocí služby Azure IoT Hub Device Provisioning Service
description: Tento kurz ukazuje, jak můžete zřídit zařízení do jednoho centra IoT pomocí služby Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 3fe2fa8b094830e2d15c1cebce782381b4ca7bc7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74975036"
---
# <a name="tutorial-provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Kurz: Zřízení zařízení do centra IoT pomocí služby Azure IoT Hub Device Provisioning Service

V předchozím kurzu jste se naučili nastavit zařízení pro připojení ke službě Device Provisioning. V tomto kurzu zjistíte, jak pomocí této služby zřídit zařízení pro jedno centrum IoT s využitím automatického zřizování a **_seznamů registrací_**. V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Registrace zařízení
> * Spuštění zařízení
> * Ověření registrace zařízení

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, nezapomeňte své zařízení nakonfigurovat, jak je popsáno v kurzu [Nastavení zařízení pro zřízení pomocí služby Azure IoT Hub Device Provisioning](./tutorial-set-up-device.md).

Pokud neznáte proces automatického zřizování, nezapomeňte si přečíst o [konceptech automatického zřizování](concepts-auto-provisioning.md), než budete pokračovat.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Registrace zařízení

Tento krok zahrnuje přidání jedinečných artefaktů zabezpečení zařízení do služby Device Provisioning. Tyto artefakty zabezpečení jsou založené na [mechanismu ověřování](concepts-device.md#attestation-mechanism) zařízení následujícím způsobem:

- Pro zařízení založená na TPM potřebujete:
    - *Ověřovací klíč*, který je jedinečný pro každou simulaci nebo čip TPM a který získáte od výrobce čipu TPM.  Další informace najdete v tématu [Vysvětlení ověřovacího klíče TPM](https://technet.microsoft.com/library/cc770443.aspx).
    - *ID registrace*, které slouží k jednoznačné identifikaci zařízení v oboru názvů nebo oboru. Toto ID může, ale nemusí být stejné jako ID zařízení. ID je povinné pro každé zařízení. U zařízení založených na TPM můžete ID registrace získat přímo z TMP, například jako hodnotu hash SHA-256 ověřovacího klíče TPM.

      [![Informace o registraci čipu TPM na portálu](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- Pro zařízení založená na X.509 potřebujete:
    - [Certifikát vydaný pro simulaci nebo čip X.509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) ve formě souboru *.pem* nebo *.cer*. Pro individuální zápis je třeba použít *certifikát podepsaný* pro jednotlivé zařízení pro systém X.509, zatímco pro skupiny zápisů je třeba použít *kořenový certifikát*. 

      [![Přidání individuální registrace pro atestaci X.509 na portálu](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

Zařízení můžete do služby Device Provisioning zaregistrovat dvěma způsoby:

- **Skupiny registrací** představují skupinu zařízení, která sdílí konkrétní mechanismus ověřování. Skupinu registrací doporučujeme použít pro velké množství zařízení, která sdílí požadovanou počáteční konfiguraci, nebo pro zařízení, která budou patřit do stejného tenanta. Další informace o ověření identity u skupin registrací najdete v tématu o [zabezpečení](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

    [![Přidání registrace skupiny pro atestaci X.509 na portálu](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **Jednotlivé registrace** představují záznamy jednotlivých zařízení, která se můžou zaregistrovat do služby Device Provisioning. Jednotlivé registrace můžou jako mechanismus ověřování využívat certifikáty X.509 nebo tokeny SAS (na skutečném nebo virtuálním zařízení TPM). Jednotlivé registrace doporučujeme použít pro zařízení, která vyžadují jedinečnou počáteční konfiguraci, a zařízení, která jako mechanismus ověřování můžou využívat pouze tokeny SAS prostřednictvím skutečného nebo virtuálního zařízení TPM. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

Teď zařízení zaregistrujete do své instance služby Device Provisioning s použitím požadovaných artefaktů zabezpečení na základě mechanismu ověřování zařízení: 

1. Přihlaste se k webu Azure Portal, v nabídce vlevo klikněte na tlačítko **Všechny prostředky** a otevřete svou službu Device Provisioning.

2. V okně s přehledem služby Device Provisioning vyberte **Správa registrací**. V závislosti na nastavení svého zařízení vyberte kartu **Jednotlivé registrace** nebo **Skupiny registrací**. Klikněte na tlačítko **Přidat** v horní části. Jako *Mechanismus* ověření identity vyberte **TPM** nebo **X.509** a zadejte odpovídající artefakty zabezpečení, jak je popsáno výše. Můžete zadat nové **ID zařízení služby IoT Hub**. Jakmile budete hotovi, klikněte na tlačítko **Uložit**. 

3. Po úspěšné registraci by se zařízení mělo zobrazit na portálu, jak je znázorněno níže:

    ![Úspěšná registrace TPM na portálu](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

Po registraci zřizovací služba počká na budoucí spuštění a připojení zařízení. Při prvním spuštění zařízení klientská knihovna SDK ve spolupráci s čipem extrahuje ze zařízení artefakty zabezpečení a ověří registraci ve službě Device Provisioning. 

## <a name="start-the-iot-device"></a>Spuštění zařízení IoT

Vaše zařízení IoT může být skutečným nebo simulovaným zařízením. Vzhledem k tomu, že jste už zařízení IoT zaregistrovali pomocí instance služby Device Provisioning Service, můžete nyní zařízení spustit a volat zřizovací službu, aby zařízení pomocí mechanismu ověřování rozpoznala. Jakmile zřizovací služba zařízení rozpozná, přiřadí ho do IoT Hubu. 

Příklady simulovaných zařízení používající ověření TPM i X.509 existují pro jazyky C, Java, C#, Node.js a Python. Například u simulovaného zařízení, které používá ověření TPM a sadu [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c), byste postupovali podle procesu popsaném v části [Simulace první spouštěcí sekvence pro zařízení](quick-create-simulated-device.md#simulate-first-boot-sequence-for-the-device). U stejného zařízení, které používá ověření certifikátem X.509, byste postupovali podle této [Simulace první spouštěcí sekvence pro zařízení](quick-create-simulated-device-x509.md#simulate-first-boot-sequence-for-the-device).

Příklad se skutečným zařízením najdete v článku s [postupem pro použití MXChip IoT DevKit](how-to-connect-mxchip-iot-devkit.md).

Spusťte zařízení, aby klientská aplikace mohla zahájit registraci do vaší služby Device Provisioning.  

## <a name="verify-the-device-is-registered"></a>Ověření registrace zařízení

Po spuštění zařízení by mělo proběhnout následující:

1. Zařízení odešle žádost o registraci do vaší služby Device Provisioning.
2. Pro zařízení TPM služba Device Provisioning odešle zpět výzvu k registraci, na kterou zařízení zareaguje. 
3. Po úspěšné registraci služba Device Provisioning odešle zpět do zařízení identifikátor URI centra IoT, ID zařízení a zašifrovaný klíč. 
4. Klientská aplikace IoT Hub na zařízení se pak připojí k vašemu centru. 
5. Po úspěšném připojení k IoT Hubu by se zařízení mělo zobrazit v průzkumníku **Zařízení IoT**. 

    ![Úspěšné připojení k centru na portálu](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

Další informace naleznete v ukázce klienta zřizovacího [zařízení, prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c). Ukázka ukazuje zřizování simulované zařízení pomocí TPM, X.509 certifikáty a symetrické klíče. Podrobné pokyny k použití vzorku naleznete zpět na rychlých startech atestace kláves [TPM](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device), [X.509](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509)a [Symetrického](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-symm-key) klíče.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Registrace zařízení
> * Spuštění zařízení
> * Ověření registrace zařízení

V dalším kurzu se dozvíte, jak zřídit několik zařízení napříč centry s vyrovnáváním zatížení. 

> [!div class="nextstepaction"]
> [Zřízení zařízení v několika centrech IoT s vyrovnáváním zatížení](./tutorial-provision-multiple-hubs.md)
