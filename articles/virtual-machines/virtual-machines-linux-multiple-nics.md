<properties
   pageTitle="Configuración de varias NIC en una máquina virtual Linux | Microsoft Azure"
   description="Aprenda a crear una máquina virtual con varias NIC conectadas a ella mediante la CLI de Azure o plantillas de Resource Manager."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/02/2016"
   ms.author="iainfou"/>

# Creación de una máquina virtual con varias NIC
Puede crear una máquina virtual (VM) en Azure que tenga asociadas varias interfaces de red virtual (NIC). Un escenario común sería tener distintas subredes para la conectividad front-end y back-end o una red dedicada a una solución de supervisión o copia de seguridad. En este artículo se proporcionan comandos rápidos para crear una máquina virtual que tiene conectadas varias NIC. Para más información, lo que incluye cómo crear varias NIC dentro de sus propios scripts de Bash, lea más sobre la [implementación de máquinas virtuales con varias NIC](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Diferentes [tamaños de máquina virtual](virtual-machines-linux-sizes.md) admiten un número distinto de NIC, así que ajuste el tamaño de su máquina virtual teniendo esto en cuenta.

>[AZURE.WARNING] Cuando crea una máquina virtual, debe asociar varias NIC; no es posible agregar NIC a una máquina virtual existente. También puede [crear una nueva máquina virtual en función de los discos virtuales originales](virtual-machines-linux-copy-vm.md) y crear varias NIC mientras implementa la máquina virtual.

## Comandos rápidos
Asegúrese de haber iniciado sesión en la [CLI de Azure](../xplat-cli-install.md) y que usa el modo de Resource Manager (`azure config mode arm`).

En primer lugar, crea un grupo de recursos:

```bash
azure group create TestRG --location WestUS
```

Cree una cuenta de almacenamiento que contenga las máquinas virtuales:

```bash
azure storage account create teststorage --resource-group TestRG \
    --location WestUS --kind Storage --sku-name PLRS
```

Cree una red virtual para conectar las máquinas virtuales a:

```bash
azure network vnet create --resource-group TestRG --location WestUS \
    --name TestVNet --address-prefixes 192.168.0.0/16 
```

Cree dos subredes de red virtual: una para el tráfico front-end y otra para el tráfico back-end:

```bash
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name FrontEnd --address-prefix 192.168.1.0/24
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name BackEnd --address-prefix 192.168.2.0/24
```

Cree dos NIC y asocie una de ellas a la subred front-end y la otra a la subred back-end:

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

Por último, cree la máquina virtual y asocie las dos NIC creadas anteriormente:

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Creación de varias NIC con la CLI de Azure
Si anteriormente ha creado una máquina virtual mediante la CLI de Azure, debería estar familiarizado con los comandos rápidos. El proceso es el mismo tanto si crea una NIC como varias de ellas. Puede leer información más detallada sobre la [implementación de varias NIC con la CLI de Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), como los scripts del proceso de paso de bucle para crear todas las NIC.

En el ejemplo siguiente se crean dos NIC, una de las cuales conecta con cada subred:

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

Normalmente también crearía un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) o un [equilibrador de carga](../load-balancer/load-balancer-overview.md) para administrar y distribuir el tráfico entre las máquinas virtuales. De nuevo, los comandos son los mismos que cuando se trabaja con varias NIC. Las NIC que se crean se enlazan a un grupo de seguridad de red o a un equilibrador de carga mediante `azure network nic set`, como en el ejemplo siguiente:

```bash
azure network nic set --resource-group TestRG --name NIC1 \
    --network-security-group-name TestNSG
```

Al crear la máquina virtual, ahora especifica varias NIC. En lugar de usar `--nic-name` para proporcionar una única NIC, usará `--nic-names` y proporcionará una lista de NIC separadas por coma. También debe tener cuidado al seleccionar el tamaño de la máquina virtual. Existen límites para el número total de NIC que se pueden agregar a una máquina virtual. Más información sobre los [tamaños de máquina virtual Linux](virtual-machines-linux-sizes.md). En el ejemplo siguiente se muestra cómo especificar varias NIC y, luego, un tamaño de máquina virtual que admita el uso de varias NIC (`Standard_DS2_v2`):

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Creación de varias NIC con plantillas de Resource Manager
Las plantillas de Azure Resource Manager emplean archivos JSON declarativos para definir el entorno. Puede leer la [información general de Azure Resource Manager](../resource-group-overview.md). Las plantillas de Resource Manager ofrecen una manera de crear varias instancias de un recurso durante la implementación; por ejemplo, se pueden crear varias NIC. Utilizará el comando *copy* para especificar el número de instancias que se crearán:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Más información sobre la [creación de varias instancias mediante *copy*](../resource-group-create-multiple.md).

También puede utilizar `copyIndex()` para anexar un número a un nombre de recurso, lo que le permite crear `NIC1`, `NIC2`, etc. A continuación se muestra un ejemplo de cómo anexar el valor de índice:

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

Puede leer un ejemplo completo de [cómo crear varias NIC con plantillas de Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## Pasos siguientes
Asegúrese de revisar los [tamaños de máquina virtual Linux](virtual-machines-linux-sizes.md) al intentar crear una máquina virtual con varias NIC. Preste atención al número máximo de NIC que admite cada tamaño de máquina virtual.

Recuerde que no se pueden agregar NIC adicionales a una máquina virtual existente; debe crear todas las NIC al implementar la máquina virtual. Tenga cuidado al planear las implementaciones para asegurarse de que dispone de toda la conectividad de red necesaria desde el principio.

<!---HONumber=AcomDC_0817_2016-->