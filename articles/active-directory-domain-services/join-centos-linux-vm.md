---
title: Připojení k virtuálnímu počítači CentOS ke službám Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat a připojit virtuální počítač CentOS Linux ke spravované doméně služby Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: d08552dcae51c897f2419d94e5e61e857247f09a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655241"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Připojení virtuálního počítače CentOS linux ke spravované doméně služby Azure AD Domain Services

Pokud chcete uživatelům uvolit přihlášení k virtuálním počítačům (VM) v Azure pomocí jediné sady přihlašovacích údajů, můžete virtuální počítače připojit ke spravované doméně služby Azure Active Directory Domain Services (AD DS). Když připojíte virtuální počítač ke spravované doméně Azure AD DS, uživatelské účty a přihlašovací údaje z domény se dají použít k přihlášení a správě serverů. Členství ve skupinách ze spravované domény Azure AD DS se také používají, abyste měli řídit přístup k souborům nebo službám na virtuálním počítači.

Tento článek ukazuje, jak se připojit k virtuálnímu počítači CentOS Linux do spravované domény Azure AD DS.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby první kurz [vytvoří a nakonfiguruje instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény Azure AD DS.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>Vytvoření virtuálního počítače SCenOS s Linuxem a připojení k němu

Pokud máte existující centos linuxový virtuální počítač v Azure, připojte se k němu pomocí SSH, pak pokračujte dalším krokem a [začněte konfigurovat virtuální počítač](#configure-the-hosts-file).

Pokud potřebujete vytvořit virtuální počítač CentOS Linux nebo chcete vytvořit testovací virtuální počítač pro použití s tímto článkem, můžete použít jednu z následujících metod:

* [portál Azure](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Při vytváření virtuálního počítače věnujte pozornost nastavení virtuální sítě a ujistěte se, že virtuální počítač může komunikovat se spravovanou doménou Azure AD DS:

* Nasaďte virtuální počítač do stejné nebo partnerské virtuální sítě, ve které jste povolili služby Azure AD Domain Services.
* Nasazení virtuálního počítače do jiné podsítě, než je vaše instance služby Azure AD Domain Services.

Po nasazení virtuálního virtuálního zařízení postupujte podle pokynů pro připojení k virtuálnímu virtuálnímu zařízení pomocí SSH.

## <a name="configure-the-hosts-file"></a>Konfigurace souboru hosts

Chcete-li se ujistit, že je název hostitele virtuálního počítače správně nakonfigurován pro spravovanou doménu, upravte soubor */etc/hosts* a nastavte název hostitele:

```console
sudo vi /etc/hosts
```

V souboru *hosts* aktualizujte adresu *localhost.* V následujícím příkladu:

* *aaddscontoso.com* je název domény DNS spravované domény Azure AD DS.
* *centos* je název hostitele vašeho virtuálního počítače CentOS, ke kterému se připojujete ke spravované doméně.

Aktualizujte tyto názvy vlastními hodnotami:

```console
127.0.0.1 centos.aaddscontoso.com centos
```

Po dokončení uložte a ukončete soubor *hosts* pomocí `:wq` příkazu editoru.

## <a name="install-required-packages"></a>Instalace požadovaných balíčků

Virtuální počítač potřebuje některé další balíčky pro připojení virtuálního počítače ke spravované doméně Azure AD DS. Chcete-li nainstalovat a nakonfigurovat tyto balíčky, `yum`aktualizujte a nainstalujte nástroje pro připojení k doméně pomocí :

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Připojení virtuálního virtuálního montovaka ke spravované doméně

Teď, když se požadované balíčky nainstalované na virtuálním počítači, připojte virtuální počítač ke spravované doméně Azure AD DS.

1. Pomocí `realm discover` příkazu zjišťujte spravovanou doménu Azure AD DS. Následující příklad zjišťuje *AADDSCONTOSO.COM*sféry . Zadejte svůj vlastní název spravované domény Azure AD DS ve všech velkých písmen:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Pokud `realm discover` příkaz nemůže najít vaši spravovanou doménu Azure AD DS, přečtěte si následující kroky řešení potíží:

    * Ujistěte se, že doména je dosažitelná z virtuálního virtuálního soudu. Pokuste `ping aaddscontoso.com` se zjistit, zda je vrácena kladná odpověď.
    * Zkontrolujte, že virtuální počítač je nasazený do stejné nebo partnerské virtuální sítě, ve které je dostupná spravovaná doména Azure AD DS.
    * Zkontrolujte, zda bylo nastavení serveru DNS pro virtuální síť aktualizováno tak, aby ukazovalo na řadiče domény spravované spravované doménou Azure AD DS.

1. Nyní inicializovat Kerberos pomocí příkazu. `kinit` Zadejte uživatele, který je součástí spravované domény Azure AD DS. V případě potřeby [přidejte uživatelský účet do skupiny ve službě Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Znovu název spravované domény Azure AD DS musí být zadán ve všech velkých písmen. V následujícím příkladu se `contosoadmin@aaddscontoso.com` účet s názvem používá k inicializaci protokolu Kerberos. Zadejte svůj vlastní uživatelský účet, který je součástí spravované domény Azure AD DS:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Nakonec připojte počítač ke spravované doméně Azure AD DS pomocí příkazu. `realm join` Použijte stejný uživatelský účet, který je součástí spravované domény Služby Azure AD DS, kterou jste zadali v předchozím `kinit` příkazu, například `contosoadmin@AADDSCONTOSO.COM`:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Připojení virtuálního počítače ke spravované doméně Azure AD DS trvá několik okamžiků. Následující příklad výstupu ukazuje, že virtuální počítač se úspěšně připojil ke spravované doméně Azure AD DS:

```output
Successfully enrolled machine in realm
```

Pokud váš virtuální počítač nemůže úspěšně dokončit proces připojení k doméně, ujistěte se, že skupina zabezpečení sítě virtuálního počítače umožňuje odchozí přenosprotokolu Kerberos na portu TCP + UDP 464 do podsítě virtuální sítě pro spravovanou doménu Azure AD DS.

## <a name="allow-password-authentication-for-ssh"></a>Povolit ověřování hesla pro SSH

Ve výchozím nastavení se uživatelé můžou k virtuálnímu počítačůmu přihlašovat jenom pomocí ověřování pomocí veřejného klíče SSH. Ověřování pomocí hesla se nezdaří. Když připojíte virtuální počítač ke spravované doméně Azure AD DS, musí tyto účty domény používat ověřování založené na heslech. Aktualizujte konfiguraci SSH takto, aby bylo možné ověřování pomocí hesel.

1. Otevřete *soubor sshd_conf* pomocí editoru:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Aktualizujte řádek pro *passwordauthentication* na *ano*:

    ```console
    PasswordAuthentication yes
    ```

    Po dokončení uložte a ukončete *soubor sshd_conf* pomocí `:wq` příkazu editoru.

1. Chcete-li použít změny a uvolit uživatelům přihlášení pomocí hesla, restartujte službu SSH:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Udělení oprávnění skupiny sudo správců řadiče AAD DC

Chcete-li udělit členům *skupiny Správců Řadiče a dministrátorů AAD* oprávnění správce na vos VM CentOS, přidejte položku */etc/sudoers*. Po přidání mohou členové *skupiny AAD DC Administrators* použít `sudo` příkaz na virtuálním počítači CentOS.

1. Otevřete soubor *sudoers* pro úpravy:

    ```console
    sudo visudo
    ```

1. Přidejte následující položku na konec *souboru /etc/sudoers.* Skupina *Správci řadiče domény AAD* obsahuje v názvu mezery, takže do názvu skupiny zahrňte řídicí znak zpětného lomítka. Přidejte svůj vlastní název domény, *například aaddscontoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Po dokončení uložte a ukončete editor pomocí `:wq` příkazu editoru.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Přihlášení k virtuálnímu virtuálnímu účtu pomocí účtu domény

Chcete-li ověřit, že virtuální počítač byl úspěšně připojen ke spravované doméně Azure AD DS, spusťte nové připojení SSH pomocí uživatelského účtu domény. Zkontrolujte, zda byl vytvořen domovský adresář a zda je použito členství ve skupině z domény.

1. Vytvořte nové připojení SSH z konzole. Pomocí `ssh -l` příkazu použijte účet domény, který patří `contosoadmin@aaddscontoso.com` do spravované domény, například adresu virtuálního počítače, například *centos.aaddscontoso.com*. Pokud používáte Azure Cloud Shell, použijte veřejnou IP adresu virtuálního počítače, nikoli interní název DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
    ```

1. Po úspěšném připojení k virtuálnímu virtuálnímu serveru ověřte, že domácí adresář byl inicializován správně:

    ```console
    pwd
    ```

    Měli byste být v adresáři */home* s vlastním adresářem, který odpovídá uživatelskému účtu.

1. Nyní zkontrolujte, zda jsou členství ve skupinách správně vyřešena:

    ```console
    id
    ```

    Měli byste vidět vaše členství ve skupinách ze spravované domény Azure AD DS.

1. Pokud jste se k virtuálnímu virtuálnímu soudu přihlásili jako člen *skupiny Správci řadiče domény AAD,* zkontrolujte, zda můžete `sudo` příkaz správně použít:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s připojením virtuálního počítače ke spravované doméně Azure AD DS nebo při přihlášení pomocí účtu domény, [přečtěte si článek Poradce při potížích s připojením domény](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
