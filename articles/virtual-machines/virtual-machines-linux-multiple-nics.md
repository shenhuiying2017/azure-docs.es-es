---
title: "Creación de una máquina virtual de Linux con varias NIC mediante la CLI de Azure 2.0 (versión preliminar)| Microsoft Docs"
description: "Aprenda a crear una máquina virtual Linux con varias NIC conectadas a ella mediante la CLI de Azure 2.0 (versión preliminar) o plantillas de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 368c79b001495e0fb000a4b280023b2299256435
ms.openlocfilehash: a854a15a9119f289344a75638d1042ee6779bb46


---
# <a name="create-a-linux-vm-with-multiple-nics-using-the-azure-cli-20-preview"></a>Creación de una máquina virtual de Linux con varias NIC mediante la CLI de Azure 2.0 (versión preliminar)
Puede crear una máquina virtual (VM) en Azure que tenga asociadas varias interfaces de red virtual (NIC). Un escenario común sería tener distintas subredes para la conectividad front-end y back-end o una red dedicada a una solución de supervisión o copia de seguridad. En este artículo se proporcionan comandos rápidos para crear una máquina virtual que tiene conectadas varias NIC. Para más información, lo que incluye cómo crear varias NIC dentro de sus propios scripts de Bash, lea más sobre la [implementación de máquinas virtuales con varias NIC](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Diferentes [tamaños de máquina virtual](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) admiten un número distinto de NIC, así que ajuste el tamaño de su máquina virtual teniendo esto en cuenta.

> [!WARNING]
> Cuando crea una máquina virtual, debe asociar varias NIC; no es posible agregar NIC a una máquina virtual existente. También puede [crear una máquina virtual en función de los discos virtuales originales](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y crear varias NIC mientras implementa la máquina virtual.


## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](virtual-machines-linux-multiple-nics-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI para los modelos de implementación clásico y de Resource Manager
- [CLI de Azure 2.0 (versión preliminar)](#create-supporting-resources): la CLI de última generación para el modelo de implementación de administración de recursos (este artículo)


## <a name="create-supporting-resources"></a>Creación de recursos de apoyo
Instale la última versión de la [CLI de Azure 2.0 (versión preliminar)](/cli/azure/install-az-cli2) e inicie sesión en una cuenta de Azure con [az login](/cli/azure/#login).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Nombres de parámetros de ejemplo incluidos `myResourceGroup`, `mystorageaccount` y `myVM`.

En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `WestUS`:

```azurecli
az group create --name myResourceGroup --location westus
```

Cree la red virtual con [az network vnet create](/cli/azure/network/vnet#create). En el ejemplo siguiente se crea una red virtual denominada `myVnet` y una subred llamada `mySubnetFrontEnd`:

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnetFrontEnd --subnet-prefix 192.168.1.0/24
```

Cree una subred para el tráfico de back-end con [az network vnet subnet create](/cli/azure/network/vnet/subnet#create). En el ejemplo siguiente se crea una subred denominada `mySubnetBackEnd`:

```azurecli
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVnet \
    --name mySubnetBackEnd --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Creación y configuración de varias NIC
Puede leer información más detallada sobre la [implementación de varias NIC con la CLI de Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), como los scripts del proceso de paso de bucle para crear todas las NIC.

Normalmente también se crea un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) o un [equilibrador de carga](../load-balancer/load-balancer-overview.md) que ayuden a administrar y distribuir el tráfico entre las distintas máquinas virtuales. Cree un grupo de seguridad de red con [az network nsg create](/cli/azure/network/nsg#create). En el ejemplo siguiente, se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup \
  --name myNetworkSecurityGroup
```

Creación de dos NIC con [az network nic create](/cli/azure/network/nic#create). En el ejemplo siguiente se crean dos NIC, denominadas `myNic1` y `myNic2`, conectadas al grupo de seguridad de red, con una NIC conectada con cada subred:

```azurecli
az network nic create --resource-group myResourceGroup --name myNic1 \
  --vnet-name myVnet --subnet mySubnetFrontEnd \
  --network-security-group myNetworkSecurityGroup
az network nic create --resource-group myResourceGroup --name myNic2 \
  --vnet-name myVnet --subnet mySubnetBackEnd \
  --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Creación de una máquina virtual y conexión de las NIC
Cuando cree la máquina virtual, especifique las NIC creadas con `--nics`. También debe tener cuidado al seleccionar el tamaño de la máquina virtual. Existen límites para el número total de NIC que se pueden agregar a una máquina virtual. Más información sobre los [tamaños de máquina virtual Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Cree la máquina virtual con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual denominada `myVM` mediante [Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic1 myNic2
```

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Creación de varias NIC con plantillas de Resource Manager
Las plantillas de Azure Resource Manager emplean archivos JSON declarativos para definir el entorno. Puede leer la [introducción a Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Las plantillas de Resource Manager ofrecen una manera de crear varias instancias de un recurso durante la implementación; por ejemplo, se pueden crear varias NIC. Utilizará el comando *copy* para especificar el número de instancias que se crearán:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Más información sobre la [creación de varias instancias mediante *copia*](../azure-resource-manager/resource-group-create-multiple.md). 

También puede utilizar `copyIndex()` para anexar un número a un nombre de recurso, lo que le permite crear `myNic1`, `myNic2`, etc. A continuación se muestra un ejemplo de cómo anexar el valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Puede leer un ejemplo completo de [cómo crear varias NIC con plantillas de Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Pasos siguientes
Asegúrese de revisar los [tamaños de máquina virtual Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) al intentar crear una máquina virtual con varias NIC. Preste atención al número máximo de NIC que admite cada tamaño de máquina virtual. 

Recuerde que no se pueden agregar NIC adicionales a una máquina virtual existente; debe crear todas las NIC al implementar la máquina virtual. Tenga cuidado al planear las implementaciones para asegurarse de que dispone de toda la conectividad de red necesaria desde el principio.




<!--HONumber=Feb17_HO2-->


