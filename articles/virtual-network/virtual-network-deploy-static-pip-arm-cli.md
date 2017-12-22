---
title: "Crear una VM con una dirección IP pública estática: CLI de Azure | Microsoft Docs"
description: "Aprenda a crear una VM con una dirección IP pública estática mediante la interfaz de la línea de comandos (CLI) de Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c50f685745a645b5fbe383a5fe4726faa0e36345
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli"></a>Crear una VM con una dirección IP pública estática mediante la CLI de Azure

> [!div class="op_single_selector"]
> * [Portal de Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [CLI de Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Plantilla](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (clásico)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del modelo de implementación clásica.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>Creación de la máquina virtual

Los valores entre "" para las variables de los pasos siguientes crean recursos con la configuración del escenario. Modifique los valores del modo adecuado para su entorno.

1. Instale la [CLI de Azure 2.0](/cli/azure/install-az-cli2), si aún no la tiene instalada.
2. Cree un par de claves pública y privada SSH para máquinas virtuales Linux siguiendo los pasos de [Creación de un par de claves SSH pública y privada para máquinas virtuales Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. En un shell de comandos, inicie sesión con el comando `az login`.
4. Cree la máquina virtual mediante la ejecución del siguiente script en un equipo Linux o Mac. La dirección IP pública de Azure, la red virtual, la interfaz de red y los recursos de máquina virtual deben existir todos en la misma ubicación. Aunque los recursos no tienen por qué existir todos en el mismo grupo de recursos, en el siguiente script sí lo hacen.

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

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

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

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
--ssh-key-value $SshKeyValue
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

Además de crear una máquina virtual, el script crea:
- Un único disco administrado premium de forma predeterminada, pero tiene otras opciones para el tipo de disco que puede crear. Consulte el artículo [Creación de una máquina virtual Linux con la CLI de Azure 2.0 ](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para más información.
- Una red virtual, una subred, una NIC y recursos de dirección IP pública. Como alternativa, puede usar una red virtual, una subred, una NIC o una dirección IP pública *existentes*. Para aprender a utilizar los recursos de red existentes, en lugar de crear recursos adicionales, escriba `az vm create -h`.

## <a name = "validate"></a>Validación de la creación de máquinas virtuales y de dirección IP pública

1. Escriba el comando `az resource list --resouce-group IaaSStory --output table` para ver una lista de los recursos creados por el script. Debería haber cinco recursos en la salida devuelta: una interfaz de red, un disco, una dirección IP pública, una red virtual y una máquina virtual.
2. Escriba el comando `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. En la salida devuelta, observe que el valor de **IpAddress** y el de **PublicIpAllocationMethod** es *Static*.
3. Antes de ejecutar el siguiente comando, quite los <>, reemplace *Username* por el nombre utilizado para la variable **Username** del script y reemplace *ipAddress* por el valor de **ipAddress** del paso anterior. Ejecute el siguiente comando para conectarse a la máquina virtual: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Eliminación de la máquina virtual y los recursos asociados

Se recomienda eliminar los recursos creados en este ejercicio si no va a utilizarlos en producción. Los recursos de máquina virtual, dirección IP pública y disco incurren en gastos, cuando se están aprovisionando. Para quitar los recursos creados durante este ejercicio, complete los pasos siguientes:

1. Ejecute el comando `az resource list --resource-group IaaSStory` para ver los recursos del grupo de recursos.
2. Confirme que no haya otros recursos en el grupo de recursos que no sean los creados por el script de este artículo, antes de eliminar el grupo de recursos. 
3. Para eliminar todos los recursos creados en este ejercicio, ejecute el comando `az group delete -n IaaSStory`. Este comando elimina el grupo de recursos y todos los recursos que contiene.

## <a name="next-steps"></a>Pasos siguientes

Cualquier tráfico de red puede fluir hacia la máquina virtual creada en este artículo y desde ella. Puede definir reglas entrantes y salientes en un grupo de seguridad de red que limite el tráfico que puede fluir hacia la interfaz de red, la subred o ambas y desde ellas. Para más información acerca de los grupos de seguridad de red, consulte el artículo de [información general sobre los grupos de seguridad de red](virtual-networks-nsg.md).