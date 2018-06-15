---
title: 'Creación de una red virtual de Azure con varias subredes: CLI de Azure | Microsoft Docs'
description: Aprenda a crear una red virtual con varias subredes mediante la CLI de Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 0b0bfae02147910d98231d7c93f5ab260f26364f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880535"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>Creación de una red virtual con varias subredes mediante la CLI de Azure

Una red virtual permite que varios tipos de recursos de Azure se comuniquen en Internet y entre ellos de forma privada. La creación de varias subredes en una red virtual permite segmentar la red para filtrar o controlar el flujo de tráfico entre las subredes. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual
> * Creación de una subred
> * Probar la comunicación de red entre máquinas virtuales

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli.md). 

## <a name="create-a-virtual-network"></a>Crear una red virtual

Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Cree la red virtual con el comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). En el ejemplo siguiente se crea una red virtual denominada *myVirtualNetwork* con el prefijo de dirección *10.0.0.0/16*. El comando crea una subred denominada *Public*, con el prefijo de dirección *10.0.0.0/24*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Puesto que no se especificó una ubicación en el comando anterior, Azure crea la red virtual en la misma ubicación del grupo de recursos *myResourceGroup*. Los **prefijos de dirección** y el **prefijo de subred** se especifican en notación CIDR. El prefijo de dirección especificado incluye las direcciones IP 10.0.0.0-10.0.255.254. El prefijo especificado para la subred debe estar en el prefijo de dirección definido para la red virtual. Azure DHCP asigna direcciones IP a partir de un prefijo de dirección de subred a los recursos implementados en una subred. Azure solo asigna las direcciones 10.0.0.4-10.0.0.254 a los recursos implementados en la subred **Public**, porque Azure reserva las cuatro primeras direcciones (10.0.0.0-10.0.0.3 para la subred, en este ejemplo) y la última dirección (10.0.0.255 para la subred, en este ejemplo) en cada subred.

## <a name="create-a-subnet"></a>Creación de una subred

Cree una subred con [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). En el ejemplo siguiente se crea una subred denominada *Private* en la red virtual *myVirtualNetwork* con el prefijo de dirección *10.0.1.0/24*. El prefijo de dirección se debe encontrar en el prefijo de dirección definido para la red virtual y no puede superponer el prefijo de dirección de las demás subredes de la red virtual.

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

Antes de implementar las redes virtuales de Azure y las subredes para usarlas en producción, se recomienda que conozca todo lo que hay que tener en cuenta sobre el [espacio de direcciones](manage-virtual-network.md#create-a-virtual-network) y los [límites de red virtual](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Una vez implementados los recursos en las subredes, puede que algunos cambios en la red virtual y la subred, como el de los intervalos de direcciones, requieran la reimplementación de los recursos de Azure en las subredes.

## <a name="test-network-communication"></a>Prueba de la comunicación de red

Una red virtual permite que varios tipos de recursos de Azure se comuniquen en Internet y entre ellos de forma privada. Un tipo de recurso que se puede implementar en una red virtual es una máquina virtual. Cree dos máquinas virtuales en la red virtual de modo que pueda probar la comunicación de red entre ellas e Internet en un paso posterior.

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree la máquina virtual con [az vm create](/cli/azure/vm#az_vm_create). En el ejemplo siguiente se crea una máquina virtual denominada *myVmWeb* en la subred *Public*. El parámetro `--no-wait` permite a Azure ejecutar el comando en segundo plano para que pueda continuar con el siguiente comando. Para simplificar este tutorial, se utiliza una contraseña. Las claves se utilizan habitualmente en las implementaciones de producción. Si utiliza claves, también debe configurar el agente SSH de reenvío para completar los pasos restantes. Para más información sobre el agente SSH de reenvío, consulte la documentación del cliente SSH. Reemplace `<replace-with-your-password>` en el siguiente comando por una contraseña de su elección.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Azure asignó automáticamente 10.0.0.4 como dirección IP privada de la máquina virtual, porque 10.0.0.4 es la primera dirección IP disponible en la subred *Public*. 

Cree una máquina virtual en la subred *Private*.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```
La creación de la máquina virtual tarda algunos minutos. Después de crear la máquina virtual, la CLI de Azure devuelve un resultado similar al ejemplo siguiente: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

En la salida del ejemplo verá que **privateIpAddress** es *10.0.1.4*. Azure creó una [interfaz de red](virtual-network-network-interface.md), la conectó a la máquina virtual, y le asignó una dirección IP privada y un valor **macAddress**. El protocolo DHCP de Azure asignó de forma automática la dirección 10.0.1.4 a la interfaz de red, ya que es la primera dirección IP disponible en la subred *Private*. Las direcciones IP y MAC privadas permanecen asignadas a la interfaz de red hasta que esta última se elimine. 

Anote el valor de **publicIpAddress**. En un paso posterior, usaremos esta dirección para obtener acceso a la máquina virtual desde Internet. Aunque no es necesario que una máquina virtual tenga asignada una dirección IP pública, de manera predeterminada, Azure asigna una a cada máquina virtual que cree. Para comunicarse con una máquina virtual desde Internet, se debe asignar una dirección IP pública a la máquina virtual. Todas las máquinas virtuales pueden establecer una comunicación saliente con Internet, tanto si se les asigna una dirección IP pública como si no. Para más información sobre las conexiones a Internet salientes en Azure, consulte [Conexiones salientes en Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Las máquinas virtuales creadas en este artículo tiene una [interfaz de red](virtual-network-network-interface.md) con una dirección IP que se le asigna dinámicamente. Después de implementar la máquina virtual, puede [agregar varias direcciones IP públicas y privadas o cambiar el método de asignación de direcciones IP a estático](virtual-network-network-interface-addresses.md#add-ip-addresses). También puede [agregar interfaces de red](virtual-network-network-interface-vm.md#vm-add-nic), hasta el límite admitido por el [tamaño de máquina virtual](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seleccionó al crear una máquina virtual. Además, puede [habilitar la virtualización de E/S de raíz única (SR-IOV)](create-vm-accelerated-networking-cli.md) en una máquina virtual, pero solo al crear una máquina virtual con un tamaño que admita la funcionalidad.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Comunicación entre máquinas virtuales y con Internet

Use el siguiente comando para crear una sesión SSH con la máquina virtual *myVmMgmt*. Reemplace `<publicIpAddress>` con la dirección IP pública de la máquina virtual. En el ejemplo anterior, la dirección IP pública es *13.90.242.231*. Cuando se le solicite una contraseña, escriba la que escribió en el artículo de [creación de máquinas virtuales](#create-virtual-machines).

```bash 
ssh azureuser@<publicIpAddress>
```

Por motivos de seguridad, es habitual limitar el número de máquinas virtuales que se pueden conectar de forma remota en una red virtual. En este tutorial, la máquina virtual *myVmMgmt* se usa para administrar la máquina virtual *myVmWeb* en la red virtual. Use el comando siguiente para conectarse a la máquina virtual *myVmWeb* mediante SSH desde la máquina virtual *myVmMgmt*:

```bash 
ssh azureuser@myVmWeb
```

Para comunicarse con la máquina virtual *myVmMgmt* desde la máquina virtual *myVmWeb*, escriba el siguiente comando desde un símbolo del sistema:

```
ping -c 4 myvmmgmt
```

Recibirá una salida similar al ejemplo siguiente:
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
Puede ver que la dirección de la máquina virtual *myVmMgmt* es 10.0.1.4. 10.0.1.4 era la primera dirección IP disponible en el intervalo de direcciones de la subred *Private* donde implementó la máquina virtual *myVmMgmt* en un paso anterior.  Verá que el nombre de dominio completo de la máquina virtual es *myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net*. Aunque la parte *hxehizax3z1udjnrx1r4gr30pg* del nombre de dominio es diferente para su máquina virtual, todas las demás son iguales. De forma predeterminada, todas las máquinas virtuales de Azure usan el servicio Azure DNS predeterminado. Todas las máquinas virtuales de una red virtual pueden resolver los nombres de las demás máquinas virtuales de la misma red virtual mediante el servicio Azure DNS predeterminado. En lugar de usar el servicio DNS predeterminado de Azure, puede usar su propio servidor DNS o la funcionalidad de dominio privado del servicio Azure DNS. Para más información, consulte [Resolución de nombre mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) o [Uso de Azure DNS para dominios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Use los siguientes comandos para instalar el servidor web nginx en la máquina virtual *myVmWeb*:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Una vez instalado nginx, cierre la sesión de SSH de *myVmWeb*, lo cual le llevará al símbolo del sistema de la máquina virtual *myVmMgmt*. Escriba el siguiente comando para recuperar la pantalla de bienvenida de nginx desde la máquina virtual *myVmWeb*.

```bash
curl myVmWeb
```

Aparecerá la pantalla de bienvenida de nginx.

Cierre la sesión SSH con la máquina virtual *myVmMgmt*.

Cuando Azure creó la máquina virtual *myVmWeb*, también se creó una dirección IP pública llamada *myVmWebPublicIP* y se asignó a la máquina virtual. Con [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) se obtiene la dirección pública de Azure asignada.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

Desde su propio equipo, escriba el siguiente comando, reemplazando `<publicIpAddress>` por la dirección devuelta desde el comando anterior:

```bash
curl <publicIpAddress>
```

Al intentar utilizar curl en la página de bienvenida de nginx desde su equipo, se producirá un error. El motivo es que cuando se implementaron las máquinas virtuales, Azure creó un grupo de seguridad de red para cada máquina virtual, de forma predeterminada. 

Un grupo de seguridad de red contiene reglas de seguridad que permiten o deniegan el tráfico de red entrante y saliente por dirección IP y puerto. El grupo de seguridad de red predeterminado que creó Azure permite la comunicación a través de todos los puertos entre los recursos de la misma red virtual. En las máquinas virtuales Linux, el grupo de seguridad de red predeterminado deniega todo el tráfico entrante desde Internet a través de todos los puertos y acepta el puerto TCP 22 (SSH). Como resultado, de forma predeterminada, también puede crear una sesión SSH directamente a la máquina virtual *myVmWeb* desde Internet, aunque puede que no quiera que el puerto 22 se abra a un servidor web. Dado que el comando `curl` se comunica por el puerto 80, se produce un error en la comunicación desde Internet porque no hay ninguna regla en el grupo de seguridad de red predeterminado que permita el tráfico en ese puerto.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, use [az group delete](/cli/azure/group#az_group_delete) para quitar el grupo de recursos y todos los recursos que contenga.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a implementar una red virtual con varias subredes. También aprendió que, al crear una máquina virtual Linux, Azure crea una interfaz de red que se asocia a la máquina virtual y crea un grupo de seguridad de red que solo permite el tráfico por el puerto 22, desde Internet. Avance al siguiente tutorial para aprender a filtrar el tráfico de red que va a las subredes, en lugar de a máquinas virtuales individuales.

> [!div class="nextstepaction"]
> [Filtrado del tráfico de red que va a las subredes](./virtual-networks-create-nsg-arm-cli.md)
