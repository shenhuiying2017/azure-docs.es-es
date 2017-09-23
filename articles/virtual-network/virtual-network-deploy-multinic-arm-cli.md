---
title: "Creación de una máquina virtual con varias NIC: CLI de Azure 2.0 | Microsoft Docs"
description: "Aprenda a crear una máquina virtual con varias NIC mediante la CLI de Azure 2.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8e906a4b-8583-4a97-9416-ee34cfa09a98
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19b1757dd694e756cfd2d0d6cd67e64f43ccab7f
ms.contentlocale: es-es
ms.lasthandoff: 04/03/2017

---
# <a name="create-a-vm-with-multiple-nics-using-the-azure-cli-20"></a>Creación de una máquina virtual con varias NIC mediante la CLI de Azure 2.0

[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md).  En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del [modelo de implementación clásica](virtual-network-deploy-multinic-classic-cli.md).
>

## <a name="create"></a>Creación de la máquina virtual

Puede completar esta tarea mediante la CLI de Azure 2.0 (en este artículo) o la [CLI de Azure 1.0](virtual-network-deploy-multinic-cli-nodejs.md). Los valores entre "" para las variables de los pasos siguientes crean recursos con la configuración del escenario. Modifique los valores del modo adecuado para su entorno.

1. Instale la [CLI de Azure 2.0](/cli/azure/install-az-cli2), si aún no la tiene instalada.
2. Cree un par de claves pública y privada SSH para máquinas virtuales Linux siguiendo los pasos de [Creación de un par de claves SSH pública y privada para máquinas virtuales Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. En un shell de comandos, inicie sesión con el comando `az login`.
4. Cree la máquina virtual mediante la ejecución del siguiente script en un equipo Linux o Mac. El script crea un grupo de recursos, una red virtual (VNet) con dos subredes, dos NIC y una máquina virtual con las dos NIC conectadas a ella. Una de las NIC está conectada a una subred y se le asigna una dirección IP pública y privada estática. La otra NIC está conectada a la otra subred, se le asigna una dirección IP privada estática y no se le asigna ninguna dirección IP pública. La NIC, la dirección IP pública, la red virtual y los recursos de máquina virtual deben existir en la misma ubicación y suscripción. Aunque los recursos no tienen por qué existir todos en el mismo grupo de recursos, en el siguiente script sí lo hacen.

```bash
#!/bin/sh

RgName="Multi-NIC-VM"
Location="westus"

# Create a resource group.
az group create \
--name $RgName \
--location $Location

# The address is assigned to the resource from a pool of IP adresses unique to each Azure region. 
# Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists
# the ranges for each region.

PipName="PIP-WEB"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static

# Create a virtual network with one subnet

VnetName="VNet1"
VnetPrefix="10.0.0.0/16"
VnetSubnet1Name="Front-End"
VnetSubnet1Prefix="10.0.0.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnet1Name \
--subnet-prefix $VnetSubnet1Prefix

# Create a second subnet within the VNet

VnetSubnet2Name="Back-end"
VnetSubnet2Prefix="10.0.1.0/24"
az network vnet subnet create \
--vnet-name $VnetName \
--resource-group $RgName \
--name $VnetSubnet2Name \
--address-prefix $VnetSubnet2Prefix

# Create a network interface connected to one of the subnets. The NIC is assigned a single dynamic private and
# public IP address by default, but you can instead, assign static addresses, or no public IP address at all.
# You can also assign multiple private or public IP addresses to each NIC. To learn more about IP addressing
# options for NICs, enter the az network nic create -h command.

Nic1Name="NIC-FE"
PrivateIpAddress1="10.0.0.5"

az network nic create \
--name $Nic1Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress1 \
--public-ip-address $PipName

# Create a second network interface and connect it to the other subnet. Though multiple NICs attached to the same
# VM can be connected to different subnets, the subnets must all be within the same VNet. Add additional NICs as necessary.

Nic2Name="NIC-BE"
PrivateIpAddress2="10.0.1.5"

az network nic create \
--name $Nic2Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet2Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress2

# Create a VM and attach the two NICs.

VmName="WEB"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. Not all VM sizes support
# more than one NIC, so be sure to select a VM size that supports the number of NICs you want to attach to the VM.
# You must create the VM with at least two NICs if you want to add more after VM creation. If you create a VM with
# only one NIC, you can't add additional NICs to the VM after VM creation, regardless of how many NICs the VM supports.
# The VM size specified in the following variable supports two NICs.

VmSize="Standard_DS2"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# az vm image list command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file.

SshKeyValue="~/.ssh/id_rsa.pub"

# Before executing the following command, add variable names of additional NICs you may have added to the script that
# you want to attach to the VM. If creating a Windows VM, remove the **ssh-key-value** line and you'll be prompted for
# the password you want to configure for the VM.

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $Nic1Name $Nic2Name \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Además de crear una máquina virtual con dos NIC, el script crea:
- Un único disco administrado premium de forma predeterminada, pero tiene otras opciones para el tipo de disco que puede crear. Consulte el artículo [Creación de una máquina virtual Linux con la CLI de Azure 2.0 ](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para más información.
- Una red virtual con dos subredes y una única dirección IP pública. Como alternativa, puede usar una red virtual, una subred, una NIC o una dirección IP pública *existentes*. Para aprender a utilizar los recursos de red existentes, en lugar de crear recursos adicionales, escriba `az vm create -h`.

## <a name = "validate"></a>Validación de la creación de máquinas virtuales y NIC

1. Escriba el comando `az resource list --resouce-group Multi-NIC-VM --output table` para ver una lista de los recursos creados por el script. Debería haber seis recursos en la salida devuelta: dos NIC, un disco, una dirección IP pública, una red virtual y una máquina virtual.
2. Escriba el comando `az network public-ip show --name PIP-WEB --resource-group Multi-NIC-VM --output table`. En la salida devuelta, observe que el valor de **IpAddress** y el de **PublicIpAllocationMethod** es *Static*.
3. Antes de ejecutar el siguiente comando, quite los <>, reemplace *Username* por el nombre utilizado para la variable **Username** del script y reemplace *ipAddress* por el valor de **ipAddress** del paso anterior. Ejecute el siguiente comando para conectarse a la máquina virtual: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 
4. Una vez conectado a la máquina virtual, ejecute el comando `sudo ifconfig` para ver las interfaces *eth0* y *eth1*. Se ha asignado a cada NIC las direcciones IP privadas estáticas especificadas en el script por los servidores DHCP de Azure. Las direcciones IP y MAC asignadas a las NIC no cambian hasta que se elimine la máquina virtual. Se recomienda no cambiar las direcciones IP dentro de un sistema operativo, ya que esto puede deshabilitar la conectividad con el equipo. Las direcciones IP públicas no aparecen dentro del sistema operativo, ya que son direcciones de red traducidas a la dirección IP privada y desde ella por la infraestructura de Azure.

## <a name= "clean-up"></a>Eliminación de la máquina virtual y los recursos asociados

Se recomienda eliminar los recursos creados en este ejercicio si no va a utilizarlos en producción. Los recursos de máquina virtual, dirección IP pública y disco incurren en gastos, cuando se están aprovisionando. Para quitar los recursos creados durante este ejercicio, complete los pasos siguientes:
1. Ejecute el comando `az resource list --resource-group Multi-NIC-VM` para ver los recursos del grupo de recursos.
2. Confirme que no haya otros recursos en el grupo de recursos que no sean los creados por el script de este artículo, antes de eliminar el grupo de recursos. 
3. Para eliminar todos los recursos creados en este ejercicio, ejecute el comando `az group delete --name Multi-NIC-VM`. Este comando elimina el grupo de recursos y todos los recursos que contiene.

## <a name="next-steps"></a>Pasos siguientes

Cualquier tráfico de red puede fluir hacia la máquina virtual creada en este artículo y desde ella. Puede definir reglas entrantes y salientes en un grupo de seguridad de red que limite el tráfico que puede fluir hacia cada interfaz de red, cada subred o ambas y desde ellas. Para más información acerca de los grupos de seguridad de red, consulte el artículo de [información general sobre los grupos de seguridad de red](virtual-networks-nsg.md).
