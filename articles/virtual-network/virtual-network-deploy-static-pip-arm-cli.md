---
title: "Creación de una máquina virtual con una dirección IP pública estática: CLI de Azure 2.0 | Microsoft Docs"
description: "Aprenda a crear una máquina virtual con una dirección IP pública estática mediante la interfaz de la línea de comandos (CLI) de Azure 2.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: e7874e7d86f75846c452d9863d5604982e9ce50b
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli-20"></a>Creación de una máquina virtual con una dirección IP pública estática mediante la CLI de Azure 2.0

> [!div class="op_single_selector"]
- [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
- [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
- [CLI de Azure 2.0](virtual-network-deploy-static-pip-arm-cli.md)
- [CLI de Azure 1.0](virtual-network-deploy-static-pip-cli-nodejs.md)
- [Plantilla](virtual-network-deploy-static-pip-arm-template.md)
- [PowerShell (clásico)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del modelo de implementación clásica.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="a-name--createacreate-the-vm"></a><a name = "create"></a>Creación de la máquina virtual

Puede completar esta tarea mediante la CLI de Azure 2.0 (en este artículo) o la [CLI de Azure 1.0](virtual-network-deploy-static-pip-cli-nodejs.md). Los valores entre "" para las variables de los pasos siguientes crean recursos con la configuración del escenario. Modifique los valores del modo adecuado para su entorno.

1. Instale la [CLI de Azure 2.0](/cli/azure/install-az-cli2), si aún no la tiene instalada.
2. Cree un par de claves pública y privada SSH para máquinas virtuales Linux siguiendo los pasos de [Creación de un par de claves SSH pública y privada para máquinas virtuales Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. En un shell de comandos, inicie sesión con el comando `az login`.
4. Cree la máquina virtual mediante la ejecución del siguiente script en un equipo Linux o Mac. La dirección IP pública de Azure, la red virtual, la interfaz de red y los recursos de máquina virtual deben existir todos en la misma ubicación. Aunque los recursos no tienen por qué existir todos en el mismo grupo de recursos, en el siguiente script sí lo hacen.

    ```azurecli
    #!/bin/sh

    RgName="IaaSStory"
    Location="westus"
    az group create --name $RgName --location $Location

    # Create a public IP address resource with a static IP address
    PipName="PIPWEB1"
    # Note: The value below must be unique within the azure location it's created in.
    DnsName="iaasstoryws1"

    az network public-ip create \
    --name $PipName \
    --resource-group $RgName \
    --location $Location \

    # The following option allocates a static public IP address to the resource. If you do not specify it, the address is
    # allocated dynamically. The address is assigigned to the resource from a pool of IP adresses unique to each Azure regions.
    # Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 to see the ranges for each region.
    --allocation-method Static \

    --dns-name $DnsName \

    # Create a virtual network with one subnet

    VnetName="TestVNet"
    VnetPrefix="192.168.0.0/16"
    SubnetName="FrontEnd"
    SubnetPrefix="192.168.1.0/24"

    az network vnet create \
    --name $VnetName \
    --resource-group $RgName \
    --location $Location \
    --address-prefix $VnetPrefix \
    --subnet-name $SubnetName \
    --subnet-prefix $SubnetPrefix

    # Create a network interface connected to the VNet with a static private IP address and associate the public IP address
    # resource to the NIC.
    NicName="NICWEB1"
    PrivateIpAddress="192.168.1.101"

    az network nic create \
    --name $NicName \
    --resource-group $RgName \
    --location $Location \
    --subnet $SubnetName \
    --vnet-name $VnetName \
    --private-ip-address $PrivateIpAddress \
    --public-ip-address $PipName

    # Create a new VM with the NIC
    VmName="WEB1"
    
    # Replace the value for the VmSize variable with a value from the
    # https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
    VmSize="Standard_DS1"

    # Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
    # `az vm image list` command. 
    OsImage="credativ:Debian:8:latest"
    
    Username='adminuser'
    
    # Replace the following value with the path to your public key file.
    SshKeyValue="~/.ssh/id_rsa.pub"

    az vm create \
    --name $VmName \
    --resource-group $RgName \
    --image $OsImage \
    --location $Location \
    --size $VmSize \
    --nics $NicName \
    --admin-username $Username \

    # If creating a Windows VM, remove the next line and you'll be prompted for the password you want to configure for the VM.
    --ssh-key-value $SshKeyValue
    ```

    Además de crear una máquina virtual, el script crea:
    - Un único disco administrado premium de forma predeterminada, pero tiene otras opciones para el tipo de disco que puede crear. Consulte el artículo [Creación de una máquina virtual Linux con la CLI de Azure 2.0 ](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para más información.
    - Una red virtual, una subred, una NIC y recursos de dirección IP pública. Como alternativa, puede usar una red virtual, una subred, una NIC o una dirección IP pública *existentes*. Para aprender a utilizar los recursos de red existentes, en lugar de crear recursos adicionales, escriba `az vm create -h`.

## <a name="a-name--validateavalidate-vm-creation-and-public-ip-address"></a><a name = "validate"></a>Validación de la creación de máquinas virtuales y de dirección IP pública

1. Escriba el comando `az resource list --resouce-group IaaSStory --output table` para ver una lista de los recursos creados por el script. Debería haber cinco recursos en la salida devuelta: una interfaz de red, un disco, una dirección IP pública, una red virtual y una máquina virtual.
2. Escriba el comando `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. En la salida devuelta, observe que el valor de **IpAddress** y el de **PublicIpAllocationMethod** es *Static*.
3. Antes de ejecutar el siguiente comando, quite los <>, reemplace *Username* por el nombre utilizado para la variable **Username** del script y reemplace *ipAddress* por el valor de **ipAddress** del paso anterior. Ejecute el siguiente comando para conectarse a la máquina virtual: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name="a-name-clean-uparemove-the-vm-and-associated-resources"></a><a name= "clean-up"></a>Eliminación de la máquina virtual y los recursos asociados

Si crea un grupo de recursos únicamente con el fin de ejecutar los pasos descritos en este artículo, puede quitar todos los recursos eliminando el grupo de recursos con el comando `az group delete -n IaaSStory`.

>[!WARNING]
>Confirme que no haya más recursos en el grupo de recursos que no sean los recursos creados por el script de este artículo, antes de eliminar el grupo de recursos. Ejecute el comando `az resource list --resouce-group IaaSStory` para ver los recursos del grupo de recursos.

Se recomienda eliminar los recursos si no va a utilizar la máquina virtual en producción. Los recursos de máquina virtual, dirección IP pública y disco incurren en gastos, cuando se están aprovisionando. 

## <a name="next-steps"></a>Pasos siguientes

Cualquier tráfico de red puede fluir hacia la máquina virtual creada en este artículo y desde ella. Puede definir reglas entrantes y salientes en un grupo de seguridad de red que limite el tráfico que puede fluir hacia la interfaz de red, la subred o ambas y desde ellas. Para más información acerca de los grupos de seguridad de red, consulte el artículo de [información general sobre los grupos de seguridad de red](virtual-networks-nsg.md).
