---
title: Nákup rezervace Azure
description: Seznamte se s důležitými body, které vám pomůžou s nákupem rezervace Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: banders
ms.openlocfilehash: 1f44b0a0e2278bfcfbbf7cde1a3605fe334eaa3f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886513"
---
# <a name="buy-a-reservation"></a>Nákup rezervace

Rezervace Azure umožňují šetřit peníze tím, že potvrzují závazek využívání řady prostředků Azure v rámci plánů na jeden nebo tři roky. Než se rozhodnete pro koupi rezervace, nezapomeňte si projít následující oddíly a připravit se na tento nákup.

## <a name="who-can-buy-a-reservation"></a>Kdo si může koupit rezervaci

Pokud si chcete koupit plán, musíte mít roli vlastníka předplatného v předplatném se smlouvou Enterprise (MS-AZR-0017P nebo MS-AZR-0148P), předplatném s průběžnými platbami (MS-AZR-0003P nebo MS-AZR-0023P) nebo předplatném se smlouvou se zákazníkem Microsoftu. Poskytovatelé cloudových řešení můžou nakupovat rezervace Azure prostřednictvím webu Azure Portal nebo  [Partnerského centra](/partner-center/azure-reservations) .

Zákazníci se smlouvou Enterprise (EA) můžou omezit nákupy na správce EA tak, že na portálu EA Portal zakážou možnost **Přidat rezervované instance**. Aby správce EA mohl zakoupit rezervaci, musí být vlastníkem alespoň jednoho předplatného EA. Tato možnost je užitečná pro podniky, které chtějí, aby rezervace pro různá nákladová centra pořizoval centralizovaný tým. Po zakoupení můžou centralizované týmy přidat k rezervacím vlastníky nákladových center. Vlastníci potom vymezí rozsah rezervace na svoje předplatná. Centrální tým nemusí umožnit přístup vlastníka předplatného tam, kde se rezervace pořizuje.

Sleva za rezervaci se vztahuje jenom na prostředky přidružené k předplatným zakoupeným prostřednictvím plánů Enterprise, Cloud Solution Provider (CSP), plánů se smlouvou se zákazníkem Microsoftu a individuálních plánů s průběžnými platbami.

## <a name="scope-reservations"></a>Vymezení rozsahů rezervací

Rozsah rezervace můžete vymezit na předplatné nebo na skupiny prostředků. Nastavením rozsahu rezervace se určí, kde se úspory získané rezervací uplatní. Pokud vymezíte rozsah rezervace na skupinu prostředků, uplatní se slevy za rezervaci jenom u této skupiny prostředků, ne u celého předplatného.

### <a name="reservation-scoping-options"></a>Možnosti vymezení rozsahů rezervací

Při vymezování rozsahu rezervace máte v závislosti na vašich potřebách k dispozici tři možnosti:

- Rozsah **Jedna skupina prostředků**: Sleva za rezervaci se uplatní jenom u odpovídajících prostředků ve vybrané skupině prostředků.
- Rozsah **Jedno předplatné**: Sleva za rezervaci se uplatní u odpovídajících prostředků ve vybraném předplatném.
- Rozsah **Sdílený**: Sleva za rezervaci se uplatní u odpovídajících prostředků v oprávněných předplatných v kontextu fakturace. U zákazníků se smlouvou Enterprise je kontextem fakturace příslušná registrace. U zákazníků se smlouvou se zákazníkem Microsoftu je rozsahem fakturace příslušný fakturační profil. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.

Při uplatňování slev za používání Azure zpracovává rezervace v tomto pořadí:

1. Rezervace s rozsahem na jednu skupinu prostředků
2. Rezervace s rozsahem na jedno předplatné
3. Rezervace se sdíleným rozsahem

Po zakoupení rezervace máte možnost rozsah aktualizovat. Provedete to tak, že přejdete k rezervaci, kliknete na **Konfigurace** a změníte rozsah této rezervace. Změna rozsahu rezervace není komerční transakcí. Období vaší rezervace se nezmění. Další informace o aktualizaci rozsahu najdete v tématu [Aktualizace rozsahu po zakoupení rezervace](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Příklad znázorňující změnu rozsahu rezervace](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>Typy zlevněných předplatných a nabídek

Slevy za rezervace platí pro následující oprávněné typy předplatných a nabídek.

- Smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P)
- Předplatná se smlouvou se zákazníkem Microsoftu
- Individuální plány s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P)
- Předplatná CSP

Prostředky, které se spouští v rámci předplatného s jinými typy nabídek, neobdrží slevu za rezervace.

## <a name="purchase-reservations"></a>Nákup rezervací

Rezervace si můžete koupit prostřednictvím webu Azure Portal, rozhraní API, PowerShellu nebo CLI. Přečtěte si následující články, ve kterých se dozvíte, kdy jste připravení koupit rezervace:

- [App Service](prepay-app-service-isolated-stamp.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Databáze Cosmos](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](../../data-explorer/pricing-reserved-capacity.md)
- [Disk Storage](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Softwarové plány](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [SQL Data Warehouse](prepay-sql-data-warehouse-charges.md)
- [Virtual Machines](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>Nákup rezervací s měsíčními platbami

Za rezervace můžete platit měsíčními platbami. Na rozdíl od nákupu předem, kdy zaplatíte celou částku najednou, se při měsíčních platbách rozdělí celkové náklady na rezervaci rovnoměrně na jednotlivé měsíce určeného období. Celkové náklady na rezervaci placenou předem a rezervaci s měsíčními platbami jsou stejné. Pokud tedy zvolíte měsíční platby, neplatíte žádné další poplatky.

Pokud se rezervace nakupuje prostřednictvím smlouvy se zákazníkem Microsoftu (MCA), částka měsíční platby se může měnit v závislosti na směnném kurzu vaší místní měny v aktuálním měsíci.

Měsíční platby nejsou k dispozici pro: Databricks, rezervace pro SUSE Linux, plány Red Hatu a Azure Red Hat OpenShift ve verzi Compute.

### <a name="view-payments-made"></a>Zobrazení provedených plateb

Provedené platby můžete zobrazit pomocí rozhraní API, prostřednictvím dat o využití a v analýze nákladů. U měsíčně placených rezervací se hodnota četnosti zobrazuje v datech o využití a v rozhraní API poplatků za rezervaci jako **opakovaná**. U rezervací placených předem je tato hodnota zobrazená jako **jednorázová**.

Analýza nákladů zobrazuje měsíční nákupy ve výchozím zobrazení. Pokud chcete zobrazit všechny nákupy, nastavte filtr **Typ poplatku** na **nákup** a **Četnost** na **opakovaná**. Pokud chcete zobrazit jenom rezervace, nastavte filtr na **Rezervace**.

![Příklad ukazující náklady na nákup rezervací v analýze nákladů](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>Výměna a refundace

Podobně jako jiné rezervace můžete i rezervace zakoupené s měsíční fakturací vracet nebo vyměňovat. 

Když vyměňujete měsíčně placenou rezervaci, celkové náklady na nový nákup by měly být větší než zbývající platby, které se pro vracenou rezervaci zruší. Pro výměny neexistují žádná další omezení ani poplatky. Předem placenou rezervaci si můžete vyměnit za nákup nové rezervace fakturované měsíčně. Celková hodnota nové rezervace by ale měla být větší než poměrná hodnota vracené rezervace.

Pokud zrušíte rezervaci, která se platí měsíčně, zrušené budoucí platby se načítají do limitu refundace 50 000 USD.

Další informace o výměně a refundacích najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](exchange-and-refund-azure-reservations.md).

## <a name="reservation-notifications"></a>Oznámení o rezervacích

V závislosti na tom, jak za předplatné Azure platíte, odesíláme e-mailová oznámení o rezervacích následujícím uživatelům ve vaší organizaci. Oznámení se odesílají při různých událostech, včetně těchto: 

- Koupit
- Nadcházející vypršení platnosti rezervace
- Vypršení platnosti
- Prodloužení
- Zrušení
- Změna rozsahu rezervace

U zákazníků s předplatnými EA:

- Oznámení se odesílají jenom kontaktům pro oznámení EA.
- Uživatelé přidaní k rezervaci pomocí oprávnění RBAC (IAM) nedostávají žádná e-mailová oznámení.

U zákazníků s individuálními předplatnými:

- Nákupčí obdrží oznámení o zakoupení.
- V okamžiku nákupu obdrží vlastník fakturačního účtu pro dané předplatné oznámení o nákupu.
- Vlastník účtu obdrží všechna další oznámení.

## <a name="next-steps"></a>Další kroky

- [Správa rezervací prostředků Azure](manage-reserved-vm-instance.md)
