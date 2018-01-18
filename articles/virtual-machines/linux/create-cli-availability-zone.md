---
title: "Creación de una máquina virtual Linux con zonas mediante la CLI de Azure | Microsoft Docs"
description: "Creación de una máquina virtual Linux en una zona de disponibilidad con la CLI de Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: 5e742187295d0bd6dbc0767ee164335fc0cf9f02
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Creación de una máquina virtual Linux en una zona de disponibilidad con la CLI de Azure

En este artículo se describe el uso de la CLI de Azure para crear una máquina virtual Linux en una zona de disponibilidad de Azure. Una [zona de disponibilidad](../../availability-zones/az-overview.md) es una zona separada físicamente en una región de Azure. Use zonas de disponibilidad para proteger sus datos y aplicaciones de la improbable pérdida o error de todo un centro de datos.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]

Asegúrese de que ha instalado la versión más reciente de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) y que ha iniciado sesión en una cuenta de Azure con [az login](/cli/azure/#login).


## <a name="check-vm-sku-availability"></a>Comprobación de la disponibilidad del SKU de la máquina virtual
La disponibilidad de tamaños de máquinas virtuales o SKU puede variar según la región y la zona. Para ayudarle a planear el uso de las Zonas de disponibilidad, puede enumerar las SKU de la máquina virtual disponibles por región de Azure y zona. Con esta funcionalidad se asegura de elegir un tamaño apropiado de máquina virtual y obtener la resistencia deseada en las distintas zonas. Para más información sobre los diferentes tipos y tamaños de máquina virtual, consulte [Introducción a los tamaños de máquina virtual](sizes.md).

Puede ver las SKU de máquina virtual disponibles con el comando [az vm list-skus](/cli/azure/vm#az_vm_list_skus). En el ejemplo siguiente se enumeran las SKU de máquina virtual disponibles en la región *eastus2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

La salida es similar al siguiente ejemplo reducido, en el que se muestran las Zonas de disponibilidad en las que está disponible cada tamaño de máquina virtual:

```azurecli
ResourceType      Locations  Name               Tier       Size     Zones
----------------  ---------  -----------------  ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2    Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2    Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s       Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s       Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3    Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3    Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3     Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3     Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#az_group_create).  

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Se debe crear un grupo de recursos antes de una máquina virtual. En este ejemplo, se crea un grupo de recursos denominado *myResourceGroupVM* en la región *eastus2*. La región Este de EE. UU. 2 es una de las regiones de Azure que admite zonas de disponibilidad en versión preliminar.

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus2
```

El grupo de recursos se especifica al crear o modificar una máquina virtual, lo que se puede a lo largo de este artículo.

## <a name="create-virtual-machine"></a>Create virtual machine

Cree la máquina virtual con el comando [az vm create](/cli/azure/vm#az_vm_create). 

Al crear una máquina virtual, están disponibles varias opciones, como la imagen de sistema operativo, tamaño de disco y credenciales administrativas. En este ejemplo, se crea una máquina virtual llamada *myVM* que se ejecuta en Ubuntu. La máquina virtual se crea en la zona de disponibilidad *1*. De forma predeterminada, la máquina virtual se crea con el tamaño *Standard_DS1_v2*. Este tamaño se admite en la versión preliminar de las zonas de disponibilidad.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys --zone 1
```

La creación de la máquina virtual puede tardar unos minutos. Una vez creada la máquina virtual, la CLI de Azure ofrece como salida información sobre la máquina virtual. Tome nota del valor de `zones`, que indica la zona de disponibilidad en la que se ejecuta la máquina virtual. 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="zone-for-ip-address-and-managed-disk"></a>Zona de dirección IP y disco administrado

Cuando la máquina virtual se implementa en una zona de disponibilidad, la dirección IP y los recursos del disco administrado se implementan en la misma zona de disponibilidad. En los ejemplos siguientes se obtiene información acerca de estos recursos.

Use primero el comando [az vm list-ip-addresses](/cli/azure/vm#az_vm_list_ip_addresses) para devolver el nombre del recurso de dirección IP pública en *myVM*. En este ejemplo, el nombre se almacena en una variable que se usa en un paso posterior.

```azurecli-interactive
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Ahora puede obtener información sobre la dirección IP:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

El resultado muestra que la dirección IP está en la misma zona de disponibilidad que la máquina virtual:

```azurecli-interactive
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

De igual forma, compruebe que el disco administrado de la máquina virtual se encuentra en la zona de disponibilidad. Use el comando [az vm show](/cli/azure/vm#az_vm_show) para devolver el identificador de disco. En este ejemplo, el identificador del disco se almacena en una variable que se usa en un paso posterior. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Ahora puede obtener información sobre el disco administrado:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

La salida muestra que el disco administrado está en la misma zona de disponibilidad que la máquina virtual:

```azurecli-interactive
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/Subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2017-09-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```
 


## <a name="next-steps"></a>pasos siguientes

En este artículo, ha aprendido a crear una máquina virtual en una zona de disponibilidad. Aprenda más sobre las [regiones y la disponibilidad](regions-and-availability.md) de las máquinas virtuales de Azure.




