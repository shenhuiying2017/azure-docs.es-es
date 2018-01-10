---
title: "Creación de una máquina virtual (clásica) con varias NIC (Azure PowerShell) | Microsoft Docs"
description: "Obtenga información sobre cómo crear una máquina virtual (clásica) con varias NIC mediante PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 824af3f0f5f6d42341f6f1dc94104f6009b568b8
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Crear una VM (clásica) con varias NIC mediante PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Puede crear máquinas virtuales (VM) en Azure y asociar varias interfaces de red (NIC) a cada una de las máquinas virtuales. Varias NIC permiten la separación de tipos de tráfico a través de las NIC. Por ejemplo, una NIC podría comunicarse con Internet, mientras que la otra solo se comunica con los recursos internos no conectados a Internet. La capacidad de separar el tráfico de red a través de varias NIC es necesaria para muchos dispositivos virtuales de red, como la entrega de aplicaciones y soluciones para la optimización de WAN.

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md). Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager. Obtenga información sobre cómo realizar estos pasos con el [modelo de implementación de Resource Manager](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

En los pasos siguientes se usa un grupo de recursos denominado *IaaSStory* para los servidores web y un grupo de recursos denominado *IaaSStory-BackEnd* para los servidores de base de datos.

## <a name="prerequisites"></a>requisitos previos

Antes de crear los servidores de base de datos, necesita crear el grupo de recursos *IaaSStory* con todos los recursos necesarios para este escenario. Para crear estos recursos, complete los pasos siguientes. Cree una red virtual siguiendo los pasos del artículo [Create a virtual network (Crear una red virtual)](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Crear las máquinas virtuales back-end
Las máquinas virtuales back-end dependen de la creación de los siguientes recursos:

* **Subred de back-end**. Los servidores de bases de datos formarán parte de una subred independiente para separar el tráfico. El siguiente script debe tener su subred en una red virtual denominada *WTestVnet*.
* **Cuenta de almacenamiento en discos de datos**. Para mejorar el rendimiento, los discos de datos en los servidores de base de datos usarán la tecnología de unidad de estado sólido (SSD), que requiere una cuenta de almacenamiento Premium. Asegúrese de que la ubicación de Azure que implementa admita el almacenamiento Premium.
* **Conjunto de disponibilidad**. Todos los servidores de base de datos se agregarán al conjunto de disponibilidad único para asegurarse de que al menos una de las máquinas virtuales está activa y ejecutándose durante el mantenimiento.

### <a name="step-1---start-your-script"></a>Paso 1: inicio del script
Puede descargar el script de PowerShell completo que ha usado [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Siga los pasos siguientes para cambiar el script para que funcione en su entorno.

1. Cambie los valores de las variables siguientes en función de su grupo de recursos existente implementado anteriormente en [Requisitos previos](#Prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Cambie los valores de las variables siguientes según los valores que desee usar para la implementación back-end.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Paso 2: creación de los recursos necesarios para las máquinas virtuales
Necesita crear un servicio en la nube y una cuenta de almacenamiento para los discos de datos de todas las VM. Asimismo, también debe especificar una imagen y una cuenta de administrador local para las máquinas virtuales. Para crear estos recursos, complete los siguientes pasos:

1. Cree un nuevo servicio en la nube.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Cree una cuenta de Almacenamiento premium.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Establezca la cuenta de almacenamiento creada anteriormente como la cuenta de almacenamiento actual para la suscripción.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Seleccione una imagen para la máquina virtual.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Establezca las credenciales de la cuenta de administrador local

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Paso 3: crear máquinas virtuales
Debe usar un bucle para crear tantas máquinas virtuales como desee y así poder crear las NIC y las máquinas virtuales dentro del bucle. Para crear las NIC y las máquinas virtuales, realice los siguientes pasos.

1. Inicie un bucle `for` para repetir los comandos que se usarán para crear una máquina virtual y dos NIC tantas veces como sea necesario, según el valor de la variable `$numberOfVMs`.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Cree un objeto `VMConfig` especificando la imagen, el tamaño y el conjunto de disponibilidad para la máquina virtual.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Aprovisione la máquina virtual como una máquina virtual de Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Establezca la NIC predeterminada y asígnela a una dirección IP estática.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Agregue una segunda NIC para cada máquina virtual.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Cree dos discos de datos para cada máquina virtual.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Cree cada máquina virtual y finalice el bucle.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Paso 4: ejecución del script
Ahora que ha descargado y cambiado el script según sus necesidades, ejecute el script para crear las máquinas virtuales de la base de datos de back-end con varias NIC.

1. Guarde el script y ejecútelo desde el símbolo del sistema **PowerShell** o desde **PowerShell ISE**. Verá el resultado inicial, tal como se muestra a continuación.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Rellene la información necesaria cuando le soliciten las credenciales y haga clic en **Aceptar**. Se devuelve el siguiente resultado.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Paso 5: configuración del enrutamiento en el sistema operativo de la máquina virtual

Azure DHCP asigna una puerta de enlace predeterminada a la primera interfaz de red (principal) conectada a la máquina virtual. Azure no asigna una puerta de enlace predeterminada a las interfaces de red adicionales (secundarias) conectadas a una máquina virtual. Por lo tanto, de manera predeterminada, no es posible comunicarse con recursos externos a la subred en la que se encuentra una interfaz de red secundaria. Sin embargo, las interfaces de red secundarias pueden comunicarse con recursos que están fuera de su subred. Para configurar el enrutamiento para las interfaces de red secundarias, consulte [Routing within a virtual machine operating system with multiple network interfaces](virtual-network-network-interface-vm.md) (Enrutamiento dentro de un sistema operativo de la máquina virtual con varias interfaces de red).
