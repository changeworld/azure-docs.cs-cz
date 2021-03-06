---
title: Zálohování sdílené složky Azure
description: Přečtěte si, jak zálohovat sdílené složky Azure v trezoru služby Recovery Services
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: fd5bb51b2c7b5c09e9d859b69c3094eb50c205b5
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396285"
---
# <a name="about-azure-file-share-backup"></a>Zálohování sdílené složky Azure

Zálohování sdílení souborů Azure je nativní cloudové řešení zálohování, které chrání vaše data v cloudu a eliminuje další nároky na údržbu, které se účastní místních řešení zálohování. Služba Azure Backup se hladce integruje se synchronizací souborů Azure a umožňuje centralizovat data sdílení souborů i zálohy. Toto jednoduché, spolehlivé a zabezpečené řešení umožňuje nakonfigurovat ochranu sdílených složek podniku v několika jednoduchých krocích s jistotou, že v případě jakéhokoli scénáře katastrofy můžete obnovit data.

## <a name="key-benefits-of-azure-file-share-backup"></a>Klíčové výhody zálohování sdílení souborů Azure

* Nulová infrastruktura: Ke konfiguraci ochrany sdílených složek není potřeba žádné nasazení.
* Integrované možnosti správy: Můžete naplánovat zálohování a určit požadovanou dobu uchování bez další režie vyřezávání dat.
* Okamžité obnovení: Zálohování sdílené složky Azure používá snímky sdílené složky, takže můžete vybrat jenom soubory, které chcete okamžitě obnovit.
* Upozorňování a vytváření sestav: Můžete nakonfigurovat výstrahy pro selhání zálohování a obnovení a pomocí řešení pro vytváření sestav poskytovaného službou Azure Backup získat přehled o zálohách ve sdílených složkách.

## <a name="architecture"></a>Architektura

![Architektura zálohování sdílení souborů Azure](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Jak funguje proces zálohování

1. Prvním krokem při konfiguraci zálohování pro sdílené složky Azure file je vytvoření trezoru služeb pro obnovení. Úschovna poskytuje konsolidované zobrazení záloh nakonfigurovaných v různých úlohách.

2. Po vytvoření trezoru služba Azure Backup zjišťuje účty úložiště, které lze zaregistrovat v trezoru. Můžete vybrat účet úložiště, který je hostitelem sdílených složek, které chcete chránit.

3. Po výběru účtu úložiště služba Azure Backup zobrazí sadu sdílených složek, které se nacházejí v účtu úložiště, a uloží jejich názvy do katalogu vrstvy správy.

4. Potom nakonfigurujte zásady zálohování (plán a uchovávání informací) podle vašich požadavků a vyberte sdílené složky, které chcete zálohovat. Služba Azure Backup registruje plány v rovině ovládacího prvku k plánování zálohování.

5. Na základě zadané zásady plánovač e-zálohování Azure aktivuje zálohování v naplánovaný čas. Jako součást této úlohy je snímek sdílené složky vytvořen pomocí rozhraní API sdílené složky. V úložišti metadat je uložena pouze adresa URL snímku.

    >[!NOTE]
    >Data sdílení souborů se nepřenášejí do služby Zálohování, protože služba Backup vytváří a spravuje snímky, které jsou součástí vašeho účtu úložiště.

6. Obsah sdílené složky Azure (jednotlivé soubory nebo úplnou sdílenou složku) můžete obnovit ze snímků dostupných ve zdrojové sdílené složce. Jakmile je operace spuštěna, adresa URL snímku se načte z úložiště metadat a data jsou uvedena a přenesena ze zdrojového snímku do cílovésdílené složky podle vašeho výběru.

7. Data monitorování úloh zálohování a obnovení se zasouvá do služby Azure Backup Monitoring. To vám umožní sledovat cloudové zálohy pro sdílené složky souborů na jednom řídicím panelu. Kromě toho můžete také nakonfigurovat výstrahy nebo e-mailová oznámení, pokud je ovlivněn stav zálohy. E-maily se posílají prostřednictvím e-mailové služby Azure.

## <a name="backup-costs"></a>Náklady na zálohování

Zálohování sdílené složky Azure je řešení založené na snímku a poplatky za úložiště, které se účtují za snímky, se účtují spolu s využitím souborů Azure podle podrobností o cenách uvedených [zde](https://azure.microsoft.com/pricing/details/storage/files/).

Poplatek za chráněnou instanci za využití řešení zálohování je však podle cenového modelu popsaného v části [Zálohování pro soubory Azure.](https://azure.microsoft.com/pricing/details/backup/) V současné době byla skutečná cena aktualizována pouze pro západní střední USA. V jiných regionech budou přesné ceny brzy aktualizovány s některými regionálními variantami, ale za použití stejného cenového modelu.

>[!NOTE]
>Během náhledu neexistuje žádný "poplatek za chráněnou instanci" a bude vám účtován pouze za snímky podle [zde](https://azure.microsoft.com/pricing/details/storage/files/)uvedených cen .

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [zálohovat sdílené složky Azure](backup-afs.md)
* Najděte odpovědi na [otázky týkající se zálohování souborů Azure](backup-azure-files-faq.md)
