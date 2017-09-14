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
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: ff3e3121102eedaa1f439e517570d0a97cf07c22
ms.contentlocale: es-es
ms.lasthandoff: 09/02/2017

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

## <a name="configure-guest-os-for-multiple-nics"></a>Configuración del sistema operativo invitado para varias NIC

Al crear varias tarjetas de interfaz de red (NIC) para una máquina virtual basada en un sistema operativo invitado Linux, se requiere crear reglas de enrutamiento adicionales que permitan enviar y recibir el tráfico que pertenezca solo a una NIC específica. En caso contrario, el tráfico perteneciente a eth1 no se puede procesar correctamente, debido a la ruta predeterminada definida.  


### <a name="solution"></a>Solución

Agregue primero dos tablas de enrutamiento al archivo /etc/iproute2/rt_tables

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

Para hacer que el cambio sea persistente y se aplique durante la activación de la pila de red, es necesario modificar los archivos */etc/sysconfig/network-scipts/ifcfg-eth0* y */etc/sysconfig/network-scipts/ifcfg-eth1*.
Cambie la línea *"NM_CONTROLLED = yes"* por *"NM_CONTROLLED = no"*.
Sin este paso, las reglas o el enrutamiento adicionales que vamos a agregar no surtirán ningún efecto.
 
El siguiente paso es extender las tablas de enrutamiento. Para hacer que los pasos siguientes sean más visibles, supondremos que tenemos la siguiente configuración:

*Enrutamiento*

```bash
default via 10.0.1.1 dev eth0 proto static metric 100
10.0.1.0/24 dev eth0 proto kernel scope link src 10.0.1.4 metric 100
10.0.1.0/24 dev eth1 proto kernel scope link src 10.0.1.5 metric 101
168.63.129.16 via 10.0.1.1 dev eth0 proto dhcp metric 100
169.254.169.254 via 10.0.1.1 dev eth0 proto dhcp metric 100
```
    
*Interfaces*

```bash
lo: inet 127.0.0.1/8 scope host lo
eth0: inet 10.0.1.4/24 brd 10.0.1.255 scope global eth0    
eth1: inet 10.0.1.5/24 brd 10.0.1.255 scope global eth1
```
    
    
Con la información anterior, es posible crear los siguientes archivos adicionales como raíz

*   /etc/sysconfig/network-scripts/rule-eth0
*   /etc/sysconfig/network-scripts/route-eth0
*   /etc/sysconfig/network-scripts/rule-eth1
*   /etc/sysconfig/network-scripts/route-eth1

El contenido de cada archivo es el siguiente
```bash
cat /etc/sysconfig/network-scripts/rule-eth0
from 10.0.1.4/32 table eth0-rt
to 10.0.1.4/32 table eth0-rt

cat /etc/sysconfig/network-scripts/route-eth0
10.0.1.0/24 dev eth0 table eth0-rt
default via 10.0.1.1 dev eth0 table eth0-rt

cat /etc/sysconfig/network-scripts/rule-eth1
from 10.0.1.5/32 table eth1-rt
to 10.0.1.5/32 table eth1-rt

cat /etc/sysconfig/network-scripts/route-eth1
10.0.1.0/24 dev eth1 table eth1-rt
default via 10.0.1.1 dev eth1 table eth1-rt
```

Después de crear los archivos y rellenarlos, es necesario reiniciar el servicio de red `systemctl restart network`

Ahora es posible la conexión desde el exterior a eth0 o eth1

## <a name="next-steps"></a>Pasos siguientes
Revise los [tamaños de máquina virtual Linux](sizes.md) al intentar crear una máquina virtual con varias NIC. Preste atención al número máximo de NIC que admite cada tamaño de máquina virtual. 

