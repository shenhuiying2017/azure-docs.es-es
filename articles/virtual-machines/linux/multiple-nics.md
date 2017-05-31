---
title: "Creación de una máquina virtual Linux en Azure con varias NIC | Microsoft Docs"
description: "Aprenda a crear una máquina virtual Linux con varias NIC conectadas a ella mediante la CLI de Azure 2.0 o las plantillas de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 8a2931e462079c101c91497d459d7d3126234244
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Cómo crear una máquina virtual Linux en Azure con red varias tarjetas de interfaz de red
Puede crear una máquina virtual (VM) en Azure que tenga asociadas varias interfaces de red virtual (NIC). Un escenario común es tener distintas subredes para la conectividad front-end y back-end o una red dedicada a una solución de supervisión o copia de seguridad. En este artículo describe cómo crear una máquina virtual con varias NIC asociadas a ella y cómo agregar o quitar las NIC de una máquina virtual existente. Para más información, lo que incluye cómo crear varias NIC dentro de sus propios scripts de Bash, lea más sobre la [implementación de máquinas virtuales con varias NIC](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Diferentes [tamaños de máquina virtual](sizes.md) admiten un número distinto de NIC, así que ajuste el tamaño de su máquina virtual teniendo esto en cuenta.

En este artículo se describe cómo crear una máquina virtual con varias NIC con la CLI de Azure 2.0. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](multiple-nics-nodejs.md).


## <a name="create-supporting-resources"></a>Creación de recursos de apoyo
Instale la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) e inicie sesión en una cuenta de Azure con [az login](/cli/azure/#login).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetro de ejemplo incluyen *myResourceGroup*, *mystorageaccount* y *myVM*.

En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Cree la red virtual con [az network vnet create](/cli/azure/network/vnet#create). En el ejemplo siguiente se crea una red virtual denominada *myVnet* y una subred *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Cree una subred para el tráfico de back-end con [az network vnet subnet create](/cli/azure/network/vnet/subnet#create). En el ejemplo siguiente se crea una subred denominada *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Cree un grupo de seguridad de red con [az network nsg create](/cli/azure/network/nsg#create). En el ejemplo siguiente se crea un grupo de seguridad de red denominado *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Creación y configuración de varias NIC
Creación de dos NIC con [az network nic create](/cli/azure/network/nic#create). En el ejemplo siguiente se crean dos NIC, denominadas *myNic1* y *myNic2*, conectadas al grupo de seguridad de red, con una NIC conectada con cada subred:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Creación de una máquina virtual y conexión de las NIC
Cuando cree la máquina virtual, especifique las NIC creadas con `--nics`. También debe tener cuidado al seleccionar el tamaño de la máquina virtual. Existen límites para el número total de NIC que se pueden agregar a una máquina virtual. Más información sobre los [tamaños de máquina virtual Linux](sizes.md). 

Cree la máquina virtual con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual denominada *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

## <a name="add-a-nic-to-a-vm"></a>Adición de una NIC a una máquina virtual
Los pasos anteriores crean una máquina virtual con varias NIC. También puede agregar varias NIC a una máquina virtual existente con la versión 2.0 de la CLI de Azure. 

Cree otra NIC con [az network nic create](/cli/azure/network/nic#create). En el ejemplo siguiente se crea una NIC denominada *myNic3* conectada a la subred de back-end y al grupo de seguridad de red creado en los pasos anteriores:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Para agregar una NIC a una máquina virtual existente, en primer lugar desasigne la máquina virtual con [az vm deallocate](/cli/azure/vm#deallocate). En el ejemplo siguiente se desasigna la máquina virtual denominada *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Agregue la NIC con [az vm nic add](/cli/azure/vm/nic#add). En el ejemplo siguiente se agrega *myNic3* a *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie la máquina virtual con [az vm start](/cli/azure/vm#start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

## <a name="remove-a-nic-from-a-vm"></a>Eliminación de una NIC de una máquina virtual
Para quitar una NIC de una máquina virtual existente, en primer lugar desasigne la máquina virtual con [az vm deallocate](/cli/azure/vm#deallocate). En el ejemplo siguiente se desasigna la máquina virtual denominada *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Quite la NIC con [az vm nic remove](/cli/azure/vm/nic#remove). En el ejemplo siguiente se quita *myNic3* de *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM 
    --nics myNic3
```

Inicie la máquina virtual con [az vm start](/cli/azure/vm#start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Creación de varias NIC con plantillas de Resource Manager
Las plantillas de Azure Resource Manager emplean archivos JSON declarativos para definir el entorno. Puede leer la [introducción a Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Las plantillas de Resource Manager ofrecen una manera de crear varias instancias de un recurso durante la implementación; por ejemplo, se pueden crear varias NIC. Utilizará el comando *copy* para especificar el número de instancias que se crearán:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Más información sobre la [creación de varias instancias mediante *copia*](../../resource-group-create-multiple.md). 

También puede utilizar `copyIndex()` para anexar un número a un nombre de recurso, lo que le permite crear `myNic1`, `myNic2`, etc. A continuación se muestra un ejemplo de cómo anexar el valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Puede leer un ejemplo completo de [cómo crear varias NIC con plantillas de Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Pasos siguientes
Revise los [tamaños de máquina virtual Linux](sizes.md) al intentar crear una máquina virtual con varias NIC. Preste atención al número máximo de NIC que admite cada tamaño de máquina virtual. 
