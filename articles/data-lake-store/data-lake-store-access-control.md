---
title: Přehled řízení přístupu v úložišti datových jezer Gen1 | Dokumenty společnosti Microsoft
description: Zjistěte, jak funguje řízení přístupu v Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 276e691351d852d6dcb0075d47bf33af6767fc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260329"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Řízení přístupu ve službě Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementuje model řízení přístupu, který je odvozen od HDFS, který zase pochází z modelu řízení přístupu POSIX. Tento článek shrnuje základy modelu řízení přístupu pro data Lake Storage Gen1. 

## <a name="access-control-lists-on-files-and-folders"></a>Seznamy řízení přístupu k souborům a složkám

Existují dva druhy seznamů řízení přístupu (ACL) – **přístupové seznamy ACL** a **výchozí seznamy ACL**.

* **Přístupové seznamy ACL:** Řídí přístup k objektu. Přístupové seznamy ACL jsou definovány pro soubory i složky.

* **Výchozí seznamy ACL:** „Šablona“ seznamů ACL přidružených ke složce, které určují přístupové seznamy ACL pro všechny podřízené položky vytvořené v rámci příslušné složky. Výchozí seznamy ACL nejsou definovány pro soubory.


Přístupové seznamy ACL i výchozí seznamy ACL mají stejnou strukturu.



> [!NOTE]
> Změna výchozího seznamu ACL u nadřazené položky nemá vliv na přístupový seznam ACL ani na výchozí seznam ACL u podřízených položek, které již existují.
>
>

## <a name="permissions"></a>Oprávnění

Pro objekt systému souborů jsou definována oprávnění **Číst**, **Zapisovat** a **Provést** a pro soubory a složky je lze používat podle následující tabulky:

|            |    File     |   Složka |
|------------|-------------|----------|
| **Číst (R)** | Může číst obsah souboru | Pro vypsání obsahu složky jsou vyžadována oprávnění **Číst** a **Provést**.|
| **Zapisovat (W)** | Může zapisovat do souboru nebo k němu připojovat data | Pro vytváření podřízených položek ve složce jsou vyžadována oprávnění **Zapisovat** a **Provést**. |
| **Provést (X)** | Neznamená nic v kontextu Data Lake Storage Gen1 | Je vyžadováno k procházení podřízenými položkami složky. |

### <a name="short-forms-for-permissions"></a>Zkrácené verze oprávnění

Zápis **RWX** se používá k označení **Číst + Zapisovat + Provést**. Používá se i zhuštěná číselná verze, která využívá nahrazení **Číst = 4**, **Zapisovat = 2** a **Provést = 1**, přičemž oprávnění je vyjádřeno součtem. Dále je uvedeno několik příkladů.

| Číselný tvar | Krátký tvar |      Význam     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Číst + Zapisovat + Provést |
| 5            | `R-X`        | Číst + Provést         |
| 4            | `R--`        | Čtení                   |
| 0            | `---`        | Žádná oprávnění         |


### <a name="permissions-do-not-inherit"></a>Oprávnění se nedědí

V modelu stylu POSIX, který používá Data Lake Storage Gen1, jsou oprávnění pro položku uložena na samotné položce. Jinými slovy: oprávnění pro položku nelze zdědit z nadřazených položek.

## <a name="common-scenarios-related-to-permissions"></a>Běžné scénáře týkající se oprávnění

Níže jsou uvedeny některé běžné scénáře, které vám pomohou pochopit, která oprávnění jsou potřeba k provedení určitých operací na účtu Data Lake Storage Gen1.

| Operace | Objekt              |    /      | Praha/   | Portland/   | Soubor Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| Čtení      | Soubor Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Připojit k | Soubor Data.txt            |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Odstranění    | Soubor Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Vytvořit    | Soubor Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Seznam      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Seznam      | /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Seznam      | /Seattle/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> Oprávnění k zápisu pro soubor nejsou vyžadována při jeho odstraňování, pokud jsou splněny obě uvedené podmínky.
>
>


## <a name="users-and-identities"></a>Uživatelé a identity

Každý soubor a složka má samostatná oprávnění pro tyto identity:

* Vlastnící uživatel
* Vlastnící skupina
* Pojmenovaní uživatelé
* Pojmenované skupiny
* Všichni ostatní uživatelé

Identity uživatelů a skupin jsou identity Azure Active Directory (Azure AD). Takže pokud není uvedeno jinak, "uživatel", v kontextu Data Lake Storage Gen1, může znamenat uživatele Azure AD nebo skupiny zabezpečení Azure AD.

### <a name="the-super-user"></a>Superuživatel

Superuživatel má nejvíce práv ze všech uživatelů v účtu Data Lake Storage Gen1. Superuživatel:

* Má oprávnění RWX ke **všem** souborům a složkám.
* Může měnit oprávnění pro kterýkoli soubor nebo složku.
* Může měnit vlastnícího uživatele nebo vlastnící skupinu pro kterýkoli soubor nebo složku.

Všichni uživatelé, kteří jsou součástí role **Vlastníci** pro účet Data Lake Storage Gen1, jsou automaticky superuživatelem.

### <a name="the-owning-user"></a>Vlastnící uživatel

Uživatel, který položku vytvořil, je automaticky jejím vlastníkem. Vlastnící uživatel může:

* Měnit oprávnění souboru, jehož vlastníkem je.
* Měnit vlastnící skupinu pro vlastněný soubor, pokud je vlastnící uživatel členem cílové skupiny.

> [!NOTE]
> Vlastnící uživatel *nemůže* měnit vlastnícího uživatele souboru nebo složky. Vlastnícího uživatele souboru nebo složky, mohou měnit pouze superuživatelé.
>
>

### <a name="the-owning-group"></a>Vlastnící skupina

**Pozadí**

V seznamech ACL POSIX je ke každému uživateli přiřazena „primární skupina“. Uživatel „alice“ může například patřit do skupiny „finance“. Alice může patřit do více skupin, ale jedna skupina je vždy určena jako její primární skupina. Když Alice vytvoří soubor v rámci specifikace POSIX, bude jako vlastnící skupina tohoto souboru nastavena její primární skupina, což je v tomto případě skupina „finance“. Jinak se vlastnící skupina chová podobně jako přiřazená oprávnění pro jiné uživatele nebo skupiny.

Vzhledem k tomu, že neexistuje žádná "primární skupina" přidružené k uživatelům v Datové jezero Storage Gen1, vlastnící skupina je přiřazena jako níže.

**Přiřazení vlastnící skupiny pro nový soubor nebo složku**

* **Případ 1:** Kořenová složka „/“. Tato složka je vytvořena při vytvoření účtu Data Lake Storage Gen1. V tomto případě je vlastnící skupina nastavena na hodnotu GUID s nulou.  Tato hodnota neumožňuje žádný přístup.  Jedná se o zástupný symbol, dokud není přiřazena skupina.
* **Případ 2** (všechny ostatní případy): Při vytvoření nové položky se vlastnící skupina zkopíruje z nadřazené složky.

**Změna vlastnící skupiny**

Vlastnící skupinu smí změnit:
* Všichni superuživatelé.
* Vlastnící uživatel, pokud je také členem cílové skupiny.

> [!NOTE]
> Vlastnící skupina *nemůže* měnit přístupové seznamy souboru nebo složky.
>
> U účtů vytvořených v září 2018 nebo před ním byla vlastnící skupina nastavena na uživatele, který účet vytvořil v případě kořenové složky pro **případ 1**výše.  Jeden uživatelský účet není platný pro poskytování oprávnění prostřednictvím vlastnící skupiny, proto nejsou tímto výchozím nastavením udělena žádná oprávnění. Toto oprávnění můžete přiřadit platné skupině uživatelů.


## <a name="access-check-algorithm"></a>Algoritmus kontroly přístupu

Následující pseudokód představuje algoritmus kontroly přístupu pro účty Data Lake Storage Gen1.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
      member_count += 1
      perms | =  entry.permissions
  if (member_count>0) :
    return ((desired_perms & perms & mask ) == desired_perms)
 
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>Maska

Jak je znázorněno v algoritmu kontroly přístupu, maska omezuje přístup pro **pojmenované uživatele**, **vlastnící skupinu**a **pojmenované skupiny**.  

> [!NOTE]
> Pro nový účet Data Lake Storage Gen1 je maska pro přístupový acl kořenové složky ("/") výchozí rwx.
>
>

### <a name="the-sticky-bit"></a>Bit sticky

Bit sticky představuje pokročilejší funkci systému souborů POSIX. V kontextu Data Lake Storage Gen1 je nepravděpodobné, že bude potřeba lepkavý bit. Stručně řečeno, pokud je ve složce povolen nepřístupný bit, může být podřízená položka odstraněna nebo přejmenována pouze vlastnícím uživatelem podřízené položky.

Bit sticky se na webu Azure Portal nezobrazuje.

## <a name="default-permissions-on-new-files-and-folders"></a>Výchozí oprávnění pro nové soubory a složky

Při vytvoření nového souboru nebo složky v rámci existující složky se podle výchozího seznamu ACL pro nadřazenou složku určí:

- Výchozí seznam ACL a přístupový seznam ACL podřízené složky.
- Přístupový seznam ACL podřízeného souboru (pro soubory není definován výchozí seznam ACL).

### <a name="umask"></a>Vlastnost umask

Při vytváření souboru nebo složky se umask používá k úpravě nastavení výchozích seznamů ACL pro podřízenou položku. umask je 9bitová hodnota v nadřazených složkách, která obsahuje hodnotu RWX pro **vlastnící uživatele**, **vlastnící skupinu**a **další**.

Umask pro Azure Data Lake Storage Gen1 je konstantní hodnota nastavená na 007. Tato hodnota se promítá do

| komponenta umask     | Číselný tvar | Krátký tvar | Význam |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Pro vlastnící uživatele zkopírujte výchozí přístupový kód nadřazeného uživatele do přístupového přístupu dítěte | 
| umask.owning_group  |    0         |   `---`      | Pro vlastnící skupinu zkopírujte výchozí přístupový soubor ACL nadřazeného dítěte do přístupového přístupu dítěte. | 
| umask.other         |    7         |   `RWX`      | Pro ostatní odebrat všechna oprávnění na přístupový přístup dítěte ACL |

Hodnota umask používaná Azure Data Lake Storage Gen1 efektivně znamená, že hodnota pro ostatní se nikdy nepřenáší ve výchozím nastavení na nové podřízené položky – bez ohledu na to, co označuje výchozí přístupový bod. 

Následující pseudokód ukazuje, jak je maska použita při vytváření seznamů ACPro pro podřízenou položku.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Časté otázky týkající se aklů v úložišti datových jezer Gen1

### <a name="do-i-have-to-enable-support-for-acls"></a>Je třeba povolit podporu pro seznamy ACL?

Ne. Řízení přístupu přes ACLs je vždy zapnuto pro účet Data Lake Storage Gen1.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Jaká oprávnění jsou vyžadována pro rekurzivní odstranění složky a jejího obsahu?

* Pro nadřazenou složku musí být nastavena oprávnění **Zapisovat + Provést**.
* Pro složku, která se má odstranit, a všechny složky, které obsahuje, se vyžadují oprávnění **Číst + Zapisovat + Provést**.

> [!NOTE]
> K odstranění souborů ve složkách nepotřebujete oprávnění Zapisovat. Kořenovou složku „/“ navíc nelze **nijak** odstranit.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Kdo je vlastníkem souboru nebo složky?

Vlastníkem souboru nebo složky se stane příslušný tvůrce.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Jaká skupina je nastavena jako vlastnící skupina souboru nebo složky při vytvoření?

Vlastnící skupina se zkopíruje z vlastnící skupiny nadřazené složky, v rámci které se nový soubor nebo složka vytvoří.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jsem vlastnícím uživatelem souboru, ale nemám potřebné oprávnění RWX. Co mám udělat?

Vlastnící uživatel může změnit oprávnění k souboru a sám si udělit veškerá potřebná oprávnění RWX.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Když se podívám na seznamy ACL na webu Azure Portal, zobrazí se uživatelská jména, ale přes rozhraní API se zobrazí identifikátory GUID. Proč?

Položky v seznamech ACL se ukládají jako identifikátory GUID odpovídající uživatelům ve službě Azure AD. Rozhraní API vrací identifikátory GUID tak, jak jsou. Azure Portal se snaží zjednodušit práci se seznamy ACL tím, že překládá identifikátory GUID na popisné názvy, pokud je to možné.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Proč se někdy na webu Azure Portal v seznamech ACL zobrazují identifikátory GUID?

Identifikátor GUID se zobrazí v případě, že daný uživatel již ve službě Azure AD neexistuje. K tomu obvykle dochází, když uživatel opustí společnost nebo když je jeho účet odstraněn ve službě Azure AD.

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Podporuje data Lake Storage Gen1 dědičnost aklů?

Ne, ale výchozí seznamy ACL je možné použít k nastavení seznamů ACL pro podřízené soubory a složku nově vytvořené v nadřazené složce.  

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Kde najdu další informace o modelu řízení přístupu POSIX?

* [POSIX Access Control Lists on Linux (Seznamy řízení přístupu v rámci specifikace POSIX v Linuxu)](https://www.linux.com/news/posix-acls-linux)
* [Průvodce oprávněními HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Nejčastější dotazy týkající se specifikace POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL na Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL: Using Access Control Lists on Linux (Seznamy ACL: Používání seznamů řízení přístupu v Linuxu)](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Viz také

* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
