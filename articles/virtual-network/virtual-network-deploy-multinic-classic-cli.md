---
title: "Creación de una máquina virtual (clásica) con varias NIC: CLI de Azure 1.0 | Microsoft Docs"
description: "Aprenda a crear una máquina virtual (clásica) con varias NIC mediante la interfaz de la línea de comandos (CLI) de Azure 1.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b62421b7289650818748d0016dccfdf42ef0a768
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>Creación de una máquina virtual (clásica) con varias NIC mediante la CLI de Azure 1.0

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Puede crear máquinas virtuales (VM) en Azure y asociar varias interfaces de red (NIC) a cada una de las máquinas virtuales. Varias NIC permiten la separación de tipos de tráfico a través de las NIC. Por ejemplo, una NIC podría comunicarse con Internet, mientras que la otra solo se comunica con los recursos internos no conectados a Internet. La capacidad de separar el tráfico de red a través de varias NIC es necesaria para muchos dispositivos virtuales de red, como la entrega de aplicaciones y soluciones para la optimización de WAN.

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md). Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Obtenga información sobre cómo realizar estos pasos con el [modelo de implementación de Resource Manager](virtual-network-deploy-multinic-arm-cli.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

En los pasos siguientes se usa un grupo de recursos denominado *IaaSStory* para los servidores web y un grupo de recursos denominado *IaaSStory-BackEnd* para los servidores de base de datos.

## <a name="prerequisites"></a>Requisitos previos
Antes de crear los servidores de base de datos, necesita crear el grupo de recursos *IaaSStory* con todos los recursos necesarios para este escenario. Para crear estos recursos, complete los pasos siguientes. Cree una red virtual siguiendo los pasos del artículo [Creación de una red virtual](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Implementación de las máquinas virtuales de back-end
Las máquinas virtuales de back-end dependen de la creación de los siguientes recursos:

* **Cuenta de almacenamiento en discos de datos**. Para mejorar el rendimiento, los discos de datos en los servidores de base de datos usarán la tecnología de unidad de estado sólido (SSD), que requiere una cuenta de almacenamiento Premium. Asegúrese de que la ubicación de Azure que implementa admita el almacenamiento Premium.
* **NIC**. Cada VM tendrá dos NIC, una para el acceso de la base de datos y otra para la administración.
* **Conjunto de disponibilidad**. Todos los servidores de base de datos se agregarán al conjunto de disponibilidad único para asegurarse de que al menos una de las máquinas virtuales está activa y ejecutándose durante el mantenimiento.

### <a name="step-1---start-your-script"></a>Paso 1: inicio del script
Puede descargar el script de Bash completo que haya usado [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Complete los pasos siguientes para cambiar el script de forma que funcione en su entorno:

1. Cambie los valores de las variables siguientes en función de su grupo de recursos existente implementado anteriormente en [Requisitos previos](#Prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Cambie los valores de las variables siguientes según los valores que desee usar para la implementación back-end.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Paso 2: creación de los recursos necesarios para las máquinas virtuales
1. Cree un nuevo servicio en la nube para todas las máquinas virtuales de back-end. Observe cómo se usa la variable `$backendCSName` para el nombre del grupo de recursos, y `$location` para la región de Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Cree una cuenta de almacenamiento Premium para los discos de datos y sistemas operativos que usarán sus máquinas virtuales.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Paso 3: crear máquinas virtuales con varias NIC
1. Inicie un bucle para crear varias máquinas virtuales, según las variables `numberOfVMs` .

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Para cada máquina virtual, especifique el nombre y la dirección IP de cada una de las dos NIC.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Cree la máquina virtual. Tenga en cuenta que deberá usar el parámetro `--nic-config` , el cual contiene una lista de todas las NIC con nombre, subred y dirección IP.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Cree dos discos de datos por cada máquina virtual.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Paso 4: ejecución del script
Ahora que descargó y cambió el script según sus necesidades, ejecute el script para crear las máquinas virtuales de la base de datos back-end con varias NIC.

1. Guarde el script y ejecútelo desde su terminal de **Bash** . Verá el resultado inicial, tal como se muestra a continuación.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Después de unos minutos, la ejecución finalizará y verá el resto del resultado como se muestra a continuación.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
