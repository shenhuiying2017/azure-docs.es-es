---
title: "Máquina virtual con varias direcciones IP mediante la CLI de Azure 2.0 | Microsoft Docs"
description: "Aprenda a asignar varias direcciones IP a una máquina virtual con la CLI de Azure 2.0 | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: d52694b2604510f1926142ace89cc7781ca83a1c
ms.openlocfilehash: 8b969b1367b3af752a89c8fea62a0685b514e8b5
ms.lasthandoff: 02/27/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli-20"></a>Asignación de varias direcciones IP a máquinas virtuales con Azure CLI 2.0

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

En este artículo se describe cómo crear una máquina virtual con el modelo de implementación de Azure Resource Manager mediante la CLI de Azure 2.0. No se pueden asignar varias direcciones IP a los recursos creados mediante el modelo de implementación clásica. Para información acerca de los modelos de implementación de Azure, lea el artículo [Understand deployment models](../resource-manager-deployment-model.md) (Descripción de los modelos de implementación).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Creación de una máquina virtual con varias direcciones IP

Puede completar esta tarea mediante la CLI de Azure 2.0 (en este artículo) o la [CLI de Azure 1.0](virtual-network-multiple-ip-addresses-cli-nodejs.md). Modifique los valores del modo adecuado para su entorno. En los pasos siguientes se explica cómo crear una VM de ejemplo con varias direcciones IP, tal como se describe en el escenario. Cambie los valores de variable entre "" y los tipos de direcciones IP según sea necesario para la implementación. 

1. Instale la [CLI de Azure 2.0](/cli/azure/install-az-cli2), si aún no la tiene instalada.
2. Cree un par de claves pública y privada SSH para máquinas virtuales Linux siguiendo los pasos de [Creación de un par de claves SSH pública y privada para máquinas virtuales Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Desde un shell de comandos, inicie sesión con el comando `az login` y seleccione la suscripción que está usando.
4. <a name="register"></a>Ejecute los siguientes comandos de PowerShell (no se puede registrar con la CLI) para registrarse para la versión preliminar:

    ```powershell
    Register-AzureRmProviderFeature  -FeatureName AllowMultipleIpConfigurationsPerNic    -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature  -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

    No trate de completar los pasos restantes hasta que vea el siguiente resultado cuando ejecute el comando ```Get-AzureRmProviderFeature```:
    
    ```powershell
    FeatureName                            ProviderName      RegistrationState
    -----------                            ------------      -----------------
    AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
    AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
    ```
        
    >[!NOTE] 
    >El registro puede tardar unos minutos.
5. Cree la máquina virtual mediante la ejecución del siguiente script en un equipo Linux o Mac. El script crea un grupo de recursos, una red virtual (VNet), una NIC con tres configuraciones IP y una máquina virtual con las dos NIC conectadas a ella. La NIC, la dirección IP pública, la red virtual y los recursos de máquina virtual deben existir en la misma ubicación y suscripción. Aunque los recursos no tienen por qué existir todos en el mismo grupo de recursos, en el siguiente script sí lo hacen.

    ```azurecli
    #!/bin/sh

    RgName="myResourceGroup"
    Location="westcentralus"
    az group create --name $RgName --location $Location

    # Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
    # do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
    # of IP adresses unique to each Azure region. Download and view the file from
    # https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

    PipName="myPublicIP"

    # This name must be unique within an Azure location.
    DnsName="myDNSName"

    az network public-ip create \
    --name $PipName \
    --resource-group $RgName \
    --location $Location \
    --dns-name $DnsName\
    --allocation-method Static

    # Create a virtual network with one subnet

    VnetName="myVnet"
    VnetPrefix="10.0.0.0/16"
    VnetSubnetName="mySubnet"
    VnetSubnetPrefix="10.0.0.0/24"

    az network vnet create \
    --name $VnetName \
    --resource-group $RgName \
    --location $Location \
    --address-prefix $VnetPrefix \
    --subnet-name $VnetSubnetName \
    --subnet-prefix $VnetSubnetPrefix

    # Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
    # first IP configuration with a dynamic private IP address and will associate the public IP address resource to it.

    NicName="MyNic1"

    az network nic create \
    --name $NicName \
    --resource-group $RgName \
    --location $Location \
    --subnet $VnetSubnet1Name \
    --vnet-name $VnetName \
    --public-ip-address $PipName

    # Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
    # static public IP address and a static private IP address.

    az network public-ip create --resource-group $RgName --location $Location --name myPublicIP2 --dns-name mypublicdns2 --allocation-method Static
    az network nic ip-config create --resource-group $RgName --nic-name $NicName --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2

    # Create a third IP configuration, and associate it to the NIC. This configuration has a dynamic private IP address and
    # no public IP address.

    azure network nic ip-config create --resource-group $RgName --nic-name $NicName --name IPConfig-3

    # Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
    # to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
    # article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

    # Create a VM and attach the NIC.

    VmName="myVm"

    # Replace the value for the following **VmSize** variable with a value from the
    # https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
    # is not supported in the location you select. Run the `azure vm sizes --location westcentralus` command to get a full list
    # of VMs in US West Central, for example.

    VmSize="Standard_DS1"

    # Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
    # `az vm image list` command.

    OsImage="credativ:Debian:8:latest"

    Username="adminuser"

    # Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
    # line and you'll be prompted for the password you want to configure for the VM.

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
    ```

    Además de crear una máquina virtual con una NIC con tres configuraciones de IP, el script crea un único disco administrado premium de forma predeterminada, pero tiene otras opciones para el tipo de disco que puede crear. Consulte el artículo [Creación de una máquina virtual Linux con la CLI de Azure 2.0 ](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para más información.
        - Una red virtual con una subred y dos direcciones IP públicas. Como alternativa, puede usar una red virtual, una subred, una NIC o una dirección IP pública *existentes*. Para aprender a utilizar los recursos de red existentes, en lugar de crear recursos adicionales, escriba `az vm create -h`.

    > [!NOTE]
    > Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios de las direcciones IP, lea la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Existe un límite para el número de direcciones IP públicas que pueden usarse dentro de una suscripción. Para más información sobre los límites, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md#networking-limits).

7. Después de crear la máquina virtual, escriba el comando `az network nic show --name MyNic1 --resource-group myResourceGroup` para ver la configuración de la NIC. Escriba el `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` para ver una lista de las configuraciones de IP asociadas a la NIC.

8. Agregue al sistema operativo de la máquina virtual la dirección IP privada siguiendo las instrucciones de la sección [Incorporación de direcciones IP a un sistema operativo de la VM](#os-config) de este artículo.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Incorporación de direcciones IP a una VM

Puede agregar más direcciones IP públicas y privadas a una NIC existente completando los pasos siguientes. Los ejemplos se crean en el [escenario](#Scenario) descrito en este artículo.

1. Abra un shell de comandos y complete los pasos restantes de esta sección dentro de una sola sesión. Si todavía no tiene la CLI de Azure instalada y configurada, complete los pasos del artículo [Instalación de la CLI de Azure 2.0](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) e inicie sesión en la cuenta de Azure con el comando `az-login`.

2. Regístrese para la versión preliminar pública siguiendo el [paso 4](#register) de la sección **Creación de una máquina virtual con varias direcciones IP** de este artículo.

3. Complete los pasos de una de las secciones siguientes, según sus requisitos:

    **Incorporación de una dirección IP privada**
    
    Para agregar una dirección IP privada a una NIC, debe crear una configuración de IP mediante el comando siguiente. Si desea agregar una dirección IP privada dinámica, quite `-PrivateIpAddress 10.0.0.7` antes de escribir el comando. Al especificar una dirección IP estática, debe ser una dirección no utilizada para la subred.

    ```azurecli
    az network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    
    Cree tantas configuraciones como sea necesario mediante nombres de configuración únicos y direcciones IP privadas (para las configuraciones con direcciones IP estáticas).

    **Incorporación de una dirección IP pública**
    
    Se agrega una dirección IP pública mediante la asociación a una nueva configuración de IP o una configuración de IP existente. Complete los pasos de una de las secciones siguientes, según sea preciso.

    > [!NOTE]
    > Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios de las direcciones IP, lea la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Existe un límite para el número de direcciones IP públicas que pueden usarse dentro de una suscripción. Para más información sobre los límites, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **Asociación del recurso a una nueva configuración de IP**
    
    Siempre que agregue una dirección IP pública en una nueva configuración de IP, también debe agregar una dirección IP privada, porque todas las configuraciones de IP deben tener una dirección IP privada. Puede agregar un recurso de dirección IP pública existente o crear uno nuevo. Para crear uno nuevo, escriba el comando siguiente:
    
    ```azurecli
      az network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --dns-name mypublicdns3
    ```

     Para crear una nueva configuración de IP con una dirección IP privada dinámica y el recurso de dirección IP pública *myPublicIP3*, escriba el comando siguiente:

    ```azurecli
    az network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-5 --public-ip-address myPublicIP3
    ```

    **Asocie el recurso a una configuración de IP existente**
   . Solo se puede asociar un recurso de dirección IP pública a una configuración de IP que ya no tiene asociado uno. Puede determinar si una configuración de IP tiene una dirección IP pública asociada escribiendo el comando siguiente:

    ```azurecli
    az network nic ip-config list --resource-group myResourceGroup --nic-name myNic1 --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Salida que se devuelve:

    ```azurecli
    Name        PublicIpAddressId
    --------    ------------------------------------------------------------------------------------------------------------
    ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
    IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
    IPConfig-3  
    ```

    Puesto que la columna **PublicIpAddressId** para *IpConfig-3* está en blanco en la salida, ningún recurso de dirección IP pública está asociado actualmente a ella. Puede agregar un recurso de dirección IP pública existente a IpConfig-3 o escribir el siguiente comando para crear uno:

    ```azurecli
    az network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --dns-name mypublicdns3 --allocation-method Static
    ```
    
    Escriba el siguiente comando para asociar el recurso de dirección IP pública a la configuración de IP existente llamada *IPConfig-3*:
    
    ```azurecli
    az network nic ip-config update --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip myPublicIP3
    ```

4. Vea las direcciones IP privadas y los identificadores de los recursos de dirección IP pública asignados a la NIC escribiendo el siguiente comando:

    ```azurecli
    az network nic ip-config list --resource-group myResourceGroup --nic-name myNic1 --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```
    Salida que se devuelve: 
    
    ```azurecli
    Name        PrivateIpAddress    PrivateIpAllocationMethod    PublicIpAddressId
    --------    ------------------  ---------------------------  ------------------------------------------------------------------------------------------------------------
    ipconfig1   10.0.0.4            Dynamic                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
    IPConfig-2  10.0.0.5            Static                       /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
    IPConfig-3  10.0.0.6            Dynamic                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    ```

5. Agregue al sistema operativo de la VM las direcciones IP privadas que agregó a la NIC siguiendo las instrucciones de la sección [Incorporación de direcciones IP a un sistema operativo de la VM](#os-config) de este artículo. No agregue las direcciones IP públicas al sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

