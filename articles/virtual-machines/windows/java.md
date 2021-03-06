---
title: Vytvoření a správa virtuálního počítače Azure pomocí Javy
description: Pomocí Java a Azure Resource Manager nasadit virtuální počítač a všechny jeho podpůrné prostředky.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 35d5569cb36cb538585b9d2c85a392b668e9fc34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944486"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Vytváření a správa virtuálních aplikací Windows v Azure pomocí Javy

Virtuální [počítač Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) potřebuje několik podpůrných prostředků Azure. Tento článek popisuje vytváření, správu a odstranění prostředků virtuálního jazyka pomocí Javy. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření projektu Maven
> * Přidání závislostí
> * Vytvoření přihlašovacích údajů
> * Vytvoření prostředků
> * Provádění úloh správy
> * Odstranění prostředků
> * Spuštění aplikace

Trvá asi 20 minut, než provedete tyto kroky.

## <a name="create-a-maven-project"></a>Vytvoření projektu Maven

1. Pokud jste tak ještě neučinili, nainstalujte [jazyk Java](https://aka.ms/azure-jdks).
2. Nainstalujte [Maven](https://maven.apache.org/download.cgi).
3. Vytvořte novou složku a projekt:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Přidání závislostí

1. Pod `testAzureApp` složkou otevřete `pom.xml` soubor a přidejte &lt;&gt; konfiguraci sestavení do projektu, abyste povolili vytváření aplikace:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Přidejte závislosti, které jsou potřeba pro přístup k Azure Java SDK.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency>
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Uložte soubor.

## <a name="create-credentials"></a>Vytvoření přihlašovacích údajů

Před zahájením tohoto kroku se ujistěte, že máte přístup k [objektu active directory](../../active-directory/develop/howto-create-service-principal-portal.md). Měli byste také zaznamenat ID aplikace, ověřovací klíč a ID klienta, které potřebujete v pozdějším kroku.

### <a name="create-the-authorization-file"></a>Vytvoření autorizačního souboru

1. Vytvořte soubor `azureauth.properties` s názvem a přidejte do něj tyto vlastnosti:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    ** &lt;&gt; Nahraďte id předplatného** identifikátorem předplatného, ** &lt;id&gt; aplikace** identifikátorem aplikace služby Active Directory, ** &lt;ověřovacím klíčem&gt; ** s klíčem aplikace a ** &lt;id&gt; klienta** identifikátorem klienta.

2. Uložte soubor.
3. Nastavte proměnnou prostředí s názvem AZURE_AUTH_LOCATION v prostředí s úplnou cestou k ověřovacímu souboru.

### <a name="create-the-management-client"></a>Vytvoření klienta pro správu

1. Otevřete `App.java` soubor `src\main\java\com\fabrikam` pod a ujistěte se, že tento příkaz balíčku je v horní části:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. Pod příkaz balíček přidejte tyto příkazy importu:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. Chcete-li vytvořit pověření služby Active Directory, která potřebujete k vytváření požadavků, přidejte tento kód do hlavní metody třídy App:
   
    ```java
    try {
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Vytvoření prostředků

### <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Všechny prostředky musí být obsaženy ve [skupině prostředků](../../azure-resource-manager/management/overview.md).

Chcete-li zadat hodnoty pro aplikaci a vytvořit skupinu prostředků, přidejte tento kód do bloku try v hlavní metodě:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Vytvoření sady dostupnosti

[Skupiny dostupnosti](tutorial-availability-sets.md) usnadňují údržbu virtuálních počítačů používaných vaší aplikací.

Chcete-li vytvořit sadu dostupnosti, přidejte tento kód do bloku try v hlavní metodě:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Vytvoření veřejné IP adresy

Pro komunikaci s virtuálním počítačem je potřeba [veřejná IP adresa.](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)

Chcete-li vytvořit veřejnou IP adresu pro virtuální počítač, přidejte tento kód do bloku try v hlavní metodě:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Virtuální počítač musí být v podsíti [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

Chcete-li vytvořit podsíť a virtuální síť, přidejte tento kód do bloku try v hlavní metodě:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>Vytvoření síťového rozhraní

Virtuální počítač potřebuje síťové rozhraní pro komunikaci ve virtuální síti.

Chcete-li vytvořit síťové rozhraní, přidejte tento kód do bloku try v hlavní metodě:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Teď, když jste vytvořili všechny podpůrné prostředky, můžete vytvořit virtuální počítač.

Chcete-li vytvořit virtuální počítač, přidejte tento kód do bloku try v hlavní metodě:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> Tento kurz vytvoří virtuální počítač s verzí operačního systému Windows Server. Další informace o výběru dalších bitových kopií najdete [v tématu Navigace a výběr ibi virtuálních zařízení Azure pomocí Windows PowerShellu a Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Pokud chcete místo image marketplace použít existující disk, použijte tento kód: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PremiumLRS)
    .create();

azure.virtualMachines.define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.StandardDS1)
    .create();
```

## <a name="perform-management-tasks"></a>Provádění úloh správy

Během životního cyklu virtuálního počítače možná budete potřebovat provádět úlohy správy, jako jsou spuštění, zastavení nebo odstranění virtuálního počítače. Kromě toho můžete chtít vytvořit kód pro automatizaci opakovaných nebo složitých úloh.

Když potřebujete něco udělat s virtuálním virtuálním mem, musíte získat jeho instanci. Přidejte tento kód do bloku try hlavní metody:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Získání informací o virtuálním virtuálním mísu

Chcete-li získat informace o virtuálním počítači, přidejte tento kód do bloku try v hlavní metodě:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Můžete zastavit virtuální počítač a zachovat všechna jeho nastavení, ale nadále se za něj účtovat, nebo můžete zastavit virtuální počítač a navrátit ho. Když je virtuální počítač nabytá, všechny prostředky s ním spojené jsou také přiděleny a fakturace pro něj končí.

Chcete-li zastavit virtuální počítač bez zrušení přemístění, přidejte tento kód do bloku try v hlavní metodě:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Pokud chcete navrátit virtuální počítač, změňte volání PowerOff na tento kód:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Spuštění virtuálního počítače

Chcete-li spustit virtuální počítač, přidejte tento kód do bloku try v hlavní metodě:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Změna velikosti virtuálního počítače

Mnoho aspektů nasazení by měly být považovány při rozhodování o velikosti pro váš virtuální počítač. Další informace najdete v tématu [velikosti virtuálních počítače](sizes.md).  

Chcete-li změnit velikost virtuálního počítače, přidejte tento kód do bloku try v hlavní metodě:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Přidání datového disku k virtuálnímu počítači

Chcete-li přidat datový disk do virtuálního počítače, který má velikost 2 GB, má logickou jednotku 0 a typ ukládání do mezipaměti readwrite, přidejte tento kód do bloku try v hlavní metodě:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Odstranění prostředků

Vzhledem k tomu, že se vám účtují prostředky používané v Azure, je vždy vhodné odstranit prostředky, které už nejsou potřeba. Pokud chcete odstranit virtuální počítače a všechny podpůrné prostředky, stačí odstranit skupinu prostředků.

1. Chcete-li odstranit skupinu prostředků, přidejte tento kód do bloku try v hlavní metodě:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Uložte soubor App.java.

## <a name="run-the-application"></a>Spuštění aplikace

Mělo by trvat asi pět minut, než bude tato konzolová aplikace spuštěna úplně od začátku do konce.

1. Chcete-li aplikaci spustit, použijte tento příkaz Maven:

    ```
    mvn compile exec:java
    ```

2. Než stisknete **Enter** a začnete spouštět prostředky, může trvat několik minut, než ověříte vytvoření prostředků na webu Azure Portal. Kliknutím na stav nasazení zobrazíte informace o nasazení.


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o používání [knihoven Azure pro Jazyk Java](https://docs.microsoft.com/java/azure/java-sdk-azure-overview).

