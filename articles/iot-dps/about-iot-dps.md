---
title: Přehled služby Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Popisuje zřizování zařízení v Azure pomocí služby Device Provisioning Service (DPS) a Služby IoT Hub.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: e564660b502a950021ba4e4754ff5c210dfd477f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241175"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Zřizování zařízení pomocí služby Azure IoT Hub Device Provisioning Service
Microsoft Azure poskytuje bohatou sadu integrovaných veřejných cloudových služeb pro všechny potřeby vašeho řešení IoT. Služba DPS (Device Provisioning Service) služby IoT Hub je pomocná služba pro Službu IoT Hub, která umožňuje zřizování s nulovým dotykem a just-in-time do správného centra IoT hub bez nutnosti lidského zásahu. DPS umožňuje zřizování milionů zařízení bezpečným a škálovatelným způsobem.

## <a name="when-to-use-device-provisioning-service"></a>Kdy použít službu Device Provisioning
Existuje mnoho scénářů zřizování, ve kterých DPS je vynikající volbou pro připojení zařízení a nakonfigurované pro Službu IoT Hub, jako jsou:

* Plně automatizované zřizování do jednoho řešení IoT bez nutnosti pevně kódovat informace o připojení ke službě IoT Hub v továrně (počáteční nastavení)
* Zařízení pro vyrovnávání zatížení ve více rozbočovačích
* Připojování zařízení k řešení IoT jejich vlastníka na základě dat o prodejních transakcích (víceklientská architektura)
* Připojování zařízení ke konkrétnímu řešení IoT v závislosti na případu použití (izolace řešení)
* Připojování zařízení k centru IoT s nejnižší latencí (geografické horizontální dělení)
* Opětovné zřizování na základě změny v zařízení
* Obměna klíčů, které zařízení používá k připojení ke službě IoT Hub (pokud k připojení nepoužívá certifikáty X.509)

## <a name="behind-the-scenes"></a>Informace pro pokročilé uživatele
Všechny scénáře uvedené v předchozí části lze provést pomocí DPS pro zero-touch zřizování se stejným tokem. Mnoho ručních kroků, které se tradičně podílejí na zřizování, je automatizováno pomocí DPS, aby se zkrátila doba nasazení zařízení IoT a snížilo se riziko ruční chyby. Následující část popisuje, co se při zřizování zařízení děje na pozadí. První krok se provádí ručně, ale všechny následující kroky jsou automatizované.

![Základní postup zřizování](./media/about-iot-dps/dps-provisioning-flow.png)

1. Výrobce zařízení přidá informace o registraci zařízení do seznamu registrací na webu Azure Portal.
2. Zařízení kontaktuje koncový bod DPS nastavený ve výrobě. Zařízení předá identifikační informace DPS, aby prokázalo svou identitu.
3. DPS ověří identitu zařízení ověřením ID registrace a klíče proti vstupu do seznamu zápisu pomocí výzvy nonce[(Trusted Platform Module)](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)nebo standardního ověření X.509 (X.509).
4. DPS zaregistruje zařízení pomocí centra IoT hub a naplní [požadovaný stav dvojčete](../iot-hub/iot-hub-devguide-device-twins.md)zařízení .
5. Centrum IoT vrátí informace o ID zařízení do DPS.
6. DPS vrátí informace o připojení centra IoT do zařízení. Zařízení teď může začít odesílat data přímo do centra IoT.
7. Zařízení se připojí k centru IoT.
8. Zařízení získá požadovaný stav ze svého dvojčete zařízení v centru IoT.

## <a name="provisioning-process"></a>Proces zřizování
Existují dva odlišné kroky v procesu nasazení zařízení, ve kterém DPS se účastní, které lze provést nezávisle:

* **Krok výroby**, při kterém se zařízení vytvoří a připraví v továrně.
* **Krok nastavení cloudu**, při kterém se ve službě Device Provisioning nakonfiguruje automatizované zřizování.

Oba tyto kroky se bezproblémově integrují do stávajících výrobních procesů a procesů nasazení. DPS dokonce zjednodušuje některé procesy nasazení, které zahrnují ruční práci pro získání informací o připojení do zařízení.

### <a name="manufacturing-step"></a>Krok výroby
Tento krok zahrnuje vše, co se děje na výrobní lince. Mezi role zapojené do tohoto kroku patří návrhář čipu, výrobce čipu, integrátor nebo koncový výrobce zařízení. Tento krok se týká samotného vytvoření hardwaru.

DPS nezavádí nový krok ve výrobním procesu; spíše se váže k existujícímu kroku, který nainstaluje počáteční software a (v ideálním případě) hsm na zařízení. Místo vytváření ID zařízení se v tomto kroku v zařízení naprogramují informace o službě zřizování, což zařízení umožní po zapnutí zavolat službu zřizování a získat informace o připojení nebo přiřazení řešení IoT.

V tomto kroku také výrobce zařízení poskytne nasazovači nebo operátorovi zařízení klíčové identifikující informace. Poskytnutí těchto informací může být jednoduché a spočívat pouze v potvrzení, že všechna zařízení mají certifikát X.509 vygenerovaný z podpisového certifikátu od nasazovače nebo operátora zařízení. Nebo může být složité a spočívat v extrahování veřejné části ověřovacího klíče TPM ze všech zařízení TPM. Tyto služby v současné době poskytuje celá řada výrobců čipů.

### <a name="cloud-setup-step"></a>Krok nastavení cloudu
Tento krok se týká konfigurace cloudu pro správné automatické zřizování. Do kroku nastavení cloudu se obvykle zapojují dva typy uživatelů: někdo, kdo ví, jaké má být počáteční nastavení zařízení (operátor zařízení), a někdo jiný, kdo ví, jak se mají zařízení rozdělit mezi centra IoT (operátor řešení).

Zřizování se musí zpočátku jednorázově nastavit, což obvykle provádí operátor řešení. Po nakonfigurování služby zřizování není potřeba ji upravovat, dokud se nezmění případ použití.

Po nakonfigurování služby pro automatické zřizování se služba musí připravit na registrování zařízení. Tento krok provádí operátor zařízení, který zná požadovanou konfiguraci zařízení a má na starosti zajištění, aby služba zřizování mohla při hledání odpovídajícího centra IoT správně ověřit identitu zařízení. Operátor zařízení vezme klíčové identifikující informace od výrobce a přidá je do seznamu registrací. Seznam registrací se následně může aktualizovat s ohledem na přidávání nových položek nebo aktualizaci nejnovějších informací o zařízeních ve stávajících položkách.

## <a name="registration-and-provisioning"></a>Registrace a zřizování
Termín *zřizování* znamená různé věci v závislosti na odvětví, ve kterém se použije. V kontextu zřizování zařízení IoT v cloudovém řešení je zřizování proces skládající se ze dvou částí:

1. První část představuje navázání počátečního připojení mezi zařízením a řešením IoT prostřednictvím registrace zařízení.
2. Druhá část představuje použití správné konfigurace pro zařízení na základě konkrétních požadavků řešení, do kterého se zaregistrovalo.

Po dokončení obou těchto kroků můžeme říct, že je zařízení plně zřízené. Některé cloudové služby zajišťují pouze první krok procesu zřizování, tedy registraci zařízení do koncového bodu řešení IoT, ale už ne počáteční konfiguraci. DPS automatizuje oba kroky a poskytuje bezproblémové zřizování zařízení.

## <a name="features-of-the-device-provisioning-service"></a>Funkce služby Device Provisioning
DPS má mnoho funkcí, takže je ideální pro zřizování zařízení.

* Podpora **zabezpečeného osvědčení** pro identity založené na X.509 i TPM.
* **Seznam registrací** obsahující úplné záznamy o zařízeních a skupinách zařízení, která se někdy můžou registrovat. Jakmile se zařízení zaregistruje, bude seznam registrací obsahovat informace o požadované konfiguraci zařízení a může se kdykoli aktualizovat.
* **Více zásad přidělení** řídit, jak DPS přiřazuje zařízení k ioT rozbočovačům na podporu vašich scénářů: Nejnižší latence, rovnoměrně vážené rozdělení (výchozí) a statické konfigurace prostřednictvím seznamu zápisu. Latence je určena stejnou metodou jako [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#performance).
* **Monitorování a protokolování diagnostiky** pro zajištění, že vše funguje správně.
* **Podpora více rozbočovačů** umožňuje DPS přiřadit zařízení k více než jednomu centru IoT hub. DPS může mluvit s rozbočovači napříč několika předplatnými Azure.
* **Podpora mezi oblastmi** umožňuje DPS přiřadit zařízení k centrům IoT hubů v jiných oblastech.
* **Šifrování dat v klidovém stavu** umožňuje šifrovat a dešifrovat data v DPS transparentně pomocí 256bitového šifrování AES, což je jedna z nejsilnějších dostupných blokových šifer a je kompatibilní s FIPS 140-2.


Další informace o konceptech a funkcích souvisejících se zřizováním zařízení najdete v [konceptech zařízení](concepts-device.md), [konceptech služby](concepts-service.md) a [konceptech zabezpečení](concepts-security.md).

## <a name="cross-platform-support"></a>Podpora různých platforem
Stejně jako všechny služby Azure IoT funguje DPS napříč platformami s různými operačními systémy. Azure nabízí open source sady SDK v různých [jazycích](https://github.com/Azure/azure-iot-sdks), které usnadňují připojení zařízení a správu služby. DPS podporuje následující protokoly pro připojení zařízení:

* HTTPS
* AMQP
* AMQP přes webové sokety
* MQTT
* MQTT přes webové sokety

DPS podporuje pouze připojení HTTPS pro servisní operace.

## <a name="regions"></a>Oblasti
DPS je k dispozici v mnoha regionech. Aktualizovaný seznam stávajících a nově ohlášených oblastí pro všechny služby najdete na stránce [Oblasti Azure](https://azure.microsoft.com/regions/). Dostupnost služby Device Provisioning můžete zkontrolovat na stránce [Stav Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> DPS je globální a není vázánna na místo. Je však nutné zadat oblast, ve které budou umístěna metadata přidružená k profilu DPS.

## <a name="availability"></a>Dostupnost
K dispozici je 99,9% smlouva o úrovni služeb pro DPS a můžete [si přečíst smlouvu SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/). Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/) vysvětluje garantovanou dostupnost Azure jako celku.

## <a name="quotas"></a>Kvóty
Pro každé předplatné Azure platí výchozí omezení kvót, která můžou ovlivnit dosah vašeho řešení IoT. Aktuální omezení je 10 služeb Device Provisioning pro každé předplatné.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Další podrobnosti o limitech kvót:
* [Omezení služeb v předplatném Azure](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Související komponenty Azure
DPS automatizuje zřizování zařízení pomocí Azure IoT Hub. Další informace o službě [IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>Další kroky
Teď máte přehled o zřizování zařízení IoT v Azure. Dalším krokem je vyzkoušet si kompletní scénář IoT.
> [!div class="nextstepaction"]
> [Nastavení služby zřizování zařízení služby IoT Hub pomocí portálu](quick-setup-auto-provision.md)
> Azure[Vytvoření a zřízení simulovanézařízení](quick-create-simulated-device.md)
> [Nastavení zařízení pro zřizování](tutorial-set-up-device.md)
