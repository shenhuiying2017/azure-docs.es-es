---
title: 'Restricción del acceso a la red a los recursos de PaaS: CLI de Azure | Microsoft Docs'
description: Aprenda a limitar o restringir el acceso a la red a los recursos de Azure, como Azure Storage y Azure SQL Database, con puntos de conexión de servicio de red virtual mediante la CLI de Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 5c0c6a802c931b71f5be8b01c610cf0810b0b4d1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Restricción del acceso a la red a los recursos de PaaS con puntos de conexión de servicio de red virtual mediante la CLI de Azure

Los puntos de conexión de servicio de red virtual permiten limitar el acceso a la red en algunos recursos de servicio de Azure a una subred de red virtual. También puede quitar el acceso a Internet a los recursos. Los puntos de conexión de servicio proporcionan conexión directa a los servicios de Azure compatibles desde la red virtual, de modo que puede usar el espacio de direcciones privadas de la red virtual para acceder a los servicios de Azure. El tráfico destinado a los recursos de Azure a través de puntos de conexión de servicio siempre se mantiene en la red troncal de Microsoft Azure. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual con una subred
> * Agregar una subred y habilitar un punto de conexión de servicio
> * Crear un recurso de Azure y permitir que tenga acceso a la red solamente desde una subred
> * Implementar una máquina virtual (VM) en cada subred
> * Confirmar el acceso a un recurso desde una subred
> * Confirmar que se ha denegado el acceso a un recurso desde una subred e Internet

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI en un entorno local, para esta guía de inicio rápido es preciso que ejecute la versión 2.0.28 de la CLI de Azure o una versión posterior. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Crear una red virtual

Antes de crear una red virtual, cree un grupo de recursos para ella y los demás recursos que se crearon en este artículo. Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Cree una red virtual con una subred con [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Habilitación de un punto de conexión de servicio 

Puede habilitar puntos de conexión de servicio únicamente para los servicios que admiten estos puntos de conexión. Vea los servicios habilitados para puntos de conexión de servicio disponibles en una ubicación de Azure con [az network vnet list-endpoint-services](/cli/azure/network/vnet#az_network_vnet_list_endpoint_services). En el ejemplo siguiente se devuelve una lista de servicios habilitados para puntos de conexión de servicio disponibles en la región *eastus*. La lista de servicios devuelta crecerá con el tiempo a medida que más servicios de Azure pasan a estar habilitados para puntos de conexión de servicio.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Cree una subred adicional en la red virtual con [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). En este ejemplo, se crea un punto de conexión de servicio para *Microsoft.Storage* para la subred: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-to-and-from-subnet"></a>Restricción del acceso entre una red y una subred

Cree un grupo de seguridad de red con [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). En el ejemplo siguiente se crea un grupo de seguridad de red denominado *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Asocie el grupo de seguridad de red a la subred *Private* con [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). En el ejemplo siguiente se asocia el grupo de seguridad de red *myNsgPrivate* a la subred *Private*:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Cree reglas de seguridad con [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). La regla siguiente permite el acceso de salida a las direcciones IP públicas asignadas al servicio Azure Storage: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"

Each network security group contains several [default security rules](security-overview.md#default-security-rules). The rule that follows overrides a default security rule that allows outbound access to all public IP addresses. The `destination-address-prefix "Internet"` option denies outbound access to all public IP addresses. The previous rule overrides this rule, due to its higher priority, which allows access to the public IP addresses of Azure Storage.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"

The following rule allows SSH traffic inbound to the subnet from anywhere. The rule overrides a default security rule that denies all inbound traffic from the internet. SSH is allowed to the subnet so that connectivity can be tested in a later step.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Restricción del acceso a la red a un recurso

Los pasos que deben seguirse para restringir el acceso a la red a los recursos creados a través de servicios de Azure habilitados para puntos de conexión de servicio varían en función de los servicios. Consulte en la documentación de cada servicio concreto los pasos necesarios para dicho servicio. Como ejemplo, de aquí en adelante se explican los pasos necesarios para restringir el acceso a la red en una cuenta de Azure Storage.

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Cree una cuenta de almacenamiento de Azure con [az storage account create](/cli/azure/storage/account#az_storage_account_create). Remplace `<replace-with-your-unique-storage-account-name>` por un nombre que sea único en todas las ubicaciones de Azure, que tenga entre 3 y 24 caracteres de longitud y que esté compuesto exclusivamente de números y letras en minúscula.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Después de crear la cuenta de almacenamiento, recupere la cadena de conexión para dicha cuenta en una variable con [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string). La cadena de conexión se utiliza para crear un recurso compartido de archivos en un paso posterior.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Vea el contenido de la variable y anote el valor de **AccountKey** devuelto en la salida, porque se utilizará en un paso posterior.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Creación de un recurso compartido de archivos en la cuenta de almacenamiento

Cree un recurso compartido de archivos en la cuenta de almacenamiento con [az storage share create](/cli/azure/storage/share#az_storage_share_create). En un paso posterior, este recurso compartido de archivos está montado para confirmar el acceso de red a él.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Denegación de todo el acceso a la red a una cuenta de almacenamiento

De forma predeterminada, las cuentas de almacenamiento aceptan conexiones de red procedentes de clientes de cualquier red. Para limitar el acceso a redes seleccionadas, cambie la acción predeterminada a *Deny* con [az storage account update](/cli/azure/storage/account#az_storage_account_update). Una vez que se deniega el acceso a la red, no se puede acceder a la cuenta de almacenamiento desde ninguna red.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Habilitación del acceso a la red desde una subred

Permita el acceso a la red a la cuenta de almacenamiento desde la subred *Private* con [az storage account network-rule add](/cli/azure/storage/account/network-rule#az_storage_account_network_rule_add).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Para probar el acceso a la red para una cuenta de almacenamiento, implemente una máquina virtual en cada subred.

### <a name="create-the-first-virtual-machine"></a>Creación de la primera máquina virtual

Cree una máquina virtual en la subred *Public* con [az vm create](/cli/azure/vm#az_vm_create). Si las claves SSH no existen en la ubicación de claves predeterminada, el comando las crea. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

La máquina virtual tarda en crearse unos minutos. Después de crear la máquina virtual, la CLI de Azure muestra información similar a la del ejemplo siguiente: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Tome nota de **publicIpAddress** en la salida devuelta. En un paso posterior, usaremos esta dirección para obtener acceso a la máquina virtual desde Internet.

### <a name="create-the-second-virtual-machine"></a>Creación de la segunda máquina virtual

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

La máquina virtual tarda en crearse unos minutos. Después de la creación, tome nota de **publicIpAddress** en la salida devuelta. En un paso posterior, usaremos esta dirección para obtener acceso a la máquina virtual desde Internet.

## <a name="confirm-access-to-storage-account"></a>Confirmación del acceso a la cuenta de almacenamiento

SSH en la máquina virtual *myVmPrivate*. Reemplace *<publicIpAddress>* por la dirección IP pública de la máquina virtual *myVmPrivate*.

```bash 
ssh <publicIpAddress>
```

Cree una carpeta para un punto de montaje:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Monte el recurso compartido de archivos de Azure en el directorio que ha creado. Antes de ejecutar el siguiente comando, reemplace `<storage-account-name>` con el nombre de cuenta y `<storage-account-key>` con la clave que recuperada en [Crear una cuenta de almacenamiento](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Recibirá el símbolo del sistema `user@myVmPrivate:~$`. El recurso compartido de archivos de Azure se montó correctamente en */mnt/MyAzureFileShare*.

Asegúrese de que la máquina virtual no tiene conectividad de salida con otras direcciones IP públicas:

```bash
ping bing.com -c 4
```

Dado que el grupo de seguridad de red asociado a la subred *Private* no permite el acceso de salida a direcciones IP públicas que no sean las direcciones asignadas al servicio Azure Storage, no recibirá ninguna respuesta.

Salga de la sesión de SSH en la máquina virtual *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmación de la denegación del acceso a la cuenta de almacenamiento

Use el siguiente comando para crear una sesión de SSH con la máquina virtual *myVmPublic*. Reemplace `<publicIpAddress>` por la dirección IP pública de la máquina virtual *myVmPublic*: 

```bash 
ssh <publicIpAddress>
```

Cree un directorio para un punto de montaje:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Intente montar el recurso compartido de archivos de Azure en el directorio que ha creado. En este tutorial se asume que ha implementado la versión más reciente de Ubuntu. Si está utilizando versiones anteriores de Ubuntu, consulte [Montaje en Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para instrucciones adicionales sobre el montaje de recursos compartidos de archivos. Antes de ejecutar el siguiente comando, reemplace `<storage-account-name>` con el nombre de cuenta y `<storage-account-key>` con la clave que recuperada en [Crear una cuenta de almacenamiento](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

El acceso se deniega y recibe un error `mount error(13): Permission denied` porque la máquina virtual *myVmPublic* está implementada dentro la subred *Public*. La subred *Public* no tiene ningún punto de conexión de servicio habilitado para Azure Storage y la cuenta de almacenamiento solo permite el acceso de red desde la subred *Private*, no desde la subred *Public*.

Salga de la sesión de SSH en la máquina virtual *myVmPublic*.

Desde su equipo, intente ver los recursos compartidos en la cuenta de almacenamiento con [az storage share list](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Reemplace `<account-name>` y `<account-key>` con el nombre de la cuenta de almacenamiento y la clave de [Crear una cuenta de almacenamiento](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

El acceso se deniega y recibe el error *This request is not authorized to perform this operation* (Esta solicitud no está autorizada para realizar esta operación) porque el equipo no está en la subred *Private* de la red virtual *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, use [az group delete](/cli/azure#az_group_delete) para quitar el grupo de recursos y todos los recursos que contenga.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha habilitado un punto de conexión de servicio para una subred de red virtual. Ha aprendido que los puntos de conexión de servicio pueden habilitarse para los recursos implementados con varios servicios de Azure. Ha creado una cuenta de Azure Storage y ha limitado el acceso de red en la cuenta de almacenamiento solo a los recursos que se encuentran en una subred de red virtual. Antes de crear puntos de conexión de servicio en las redes virtuales de producción, es recomendable que se familiarice a fondo con los [puntos de conexión de servicio](virtual-network-service-endpoints-overview.md).

Si tiene varias redes virtuales en la cuenta, es posible que desee conectar dos de ellas para que los recursos que están dentro de cada red virtual puedan comunicarse entre sí. Continúe con el siguiente tutorial para aprender a conectar redes virtuales.

> [!div class="nextstepaction"]
> [Conexión de redes virtuales](./tutorial-connect-virtual-networks-cli.md)
