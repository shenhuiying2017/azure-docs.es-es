---
title: Enrutamiento del tráfico de red en la CLI de Azure | Microsoft Docs
description: Aprenda a enrutar el tráfico de red con una tabla de rutas mediante la CLI de Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Enrutamiento del tráfico de red con una tabla de rutas mediante la CLI de Azure

De forma predeterminada, Azure enruta automáticamente el tráfico entre todas las subredes de una red virtual. Sin embargo, puede crear sus propias rutas para invalidar las predeterminadas de Azure. La posibilidad de crear rutas personalizadas resulta de utilidad si, por ejemplo, quiere enrutar el tráfico entre subredes por medio de un firewall. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Creación de una tabla de rutas
> * Creación de una ruta
> * Asociar una tabla de rutas a una subred de red virtual
> * Probar el enrutamiento
> * Solucionar problemas de enrutamiento

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI en un entorno local, para esta guía de inicio rápido es preciso que ejecute la versión 2.0.28 de la CLI de Azure o una versión posterior. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Creación de una tabla de rutas

De forma predeterminada, Azure enruta el tráfico entre todas las subredes de una red virtual. Para más información sobre las rutas predeterminadas de Azure, consulte el artículo sobre las [rutas del sistema](virtual-networks-udr-overview.md). Para invalidar el enrutamiento predeterminado de Azure, cree una tabla de rutas que contenga rutas y asóciela a una subred de la red virtual.

Antes de poder crear una tabla de rutas, debe crear un grupo de recursos con [az group create](/cli/azure/group#az_group_create) para todos los recursos creados en este artículo. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Cree una tabla de rutas con [az network route-table create](/cli/azure/network/route#az_network_route_table_create). En el ejemplo siguiente se crea una tabla de rutas denominada *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Creación de una ruta

Una tabla de rutas puede contener varias rutas o ninguna. Cree una ruta en la tabla de rutas con [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

La ruta dirige todo el tráfico destinado al prefijo de dirección 10.0.1.0/24 por una aplicación virtual de red con la dirección IP 10.0.2.4. En pasos posteriores, se crean la aplicación virtual de red y la subred con el prefijo de dirección especificado. La ruta invalida el enrutamiento predeterminado de Azure, que enruta el tráfico directamente entre subredes. Cada ruta especifica un tipo de próximo salto. El tipo de próximo salto indica a Azure cómo enrutar el tráfico. En este ejemplo, el tipo de próximo salto es *VirtualAppliance*. Para más información sobre todos los tipos de próximo salto disponibles en Azure y cuándo usarlos, consulte [aquí](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Asociación de una tabla de rutas a una subred

Para poder asociar una tabla de rutas a una subred, debe crear una red virtual y una subred. Cree una red virtual con una subred con [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Cree dos subredes adicionales con [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Puede asociar una tabla de rutas a varias subredes o a ninguna. Una subred puede tener una tabla de ruta asociada a ella o ninguna. El tráfico saliente de una subred se enruta en función de las rutas predeterminadas de Azure y de cualquier ruta que haya agregado a una tabla de rutas que asocie a una subred. Asocie la tabla de rutas *myRouteTablePublic* a la subred *Pública* con [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

Antes de implementar tablas de rutas para su uso en producción, se recomienda familiarizarse bien con el [enrutamiento en Azure](virtual-networks-udr-overview.md) y los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Probar el enrutamiento

Para probar el enrutamiento, creará una máquina virtual que actúe como aplicación virtual de red mediante la que se enruta la ruta que creó en un paso anterior. Después de crear la aplicación virtual de red, implementará una máquina virtual en las subredes *Pública* y *Privada*. A continuación, enrutará el tráfico de la subred *Pública* a la subred *Privada* mediante la aplicación virtual de red.

### <a name="create-a-network-virtual-appliance"></a>Creación de una aplicación virtual de red

En un paso anterior, creó una ruta que especificaba una aplicación virtual de red como el tipo de próximo salto. Una máquina virtual que se ejecuta en una aplicación de red se conoce a menudo como aplicación virtual de red. En entornos de producción, la aplicación virtual de red se suele implementar en una máquina virtual configurada previamente. Hay varias aplicaciones virtuales de red disponibles en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). En este artículo, se crea una máquina virtual básica. 

Cree una aplicación virtual de red en la subred *DMZ* con [az vm create](/cli/azure/vm#az_vm_create). Cuando se crea una máquina virtual, Azure crea y asigna una dirección IP pública a la máquina virtual de forma predeterminada. El parámetro `--public-ip-address ""` indica a Azure que no cree ni asigne una dirección IP pública a la máquina virtual, dado que no es necesario que la máquina esté conectada a Internet. Si las claves SSH no existen en la ubicación de claves predeterminada, el comando las crea. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

La creación de la máquina virtual tarda algunos minutos. No continúe con el paso siguiente hasta que Azure termine de crear la máquina virtual y devuelva información sobre ella. En entornos de producción, la aplicación virtual de red se suele implementar en una máquina virtual configurada previamente. Hay varias aplicaciones virtuales de red disponibles en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Puede habilitar el reenvío IP para cada [interfaz de red](virtual-network-network-interface.md) de Azure asociada a una máquina virtual que reenvía el tráfico destinado a cualquier dirección IP no asignada a la interfaz de red. Cuando creó la aplicación virtual de red en la subred *DMZ*, Azure creó automáticamente una interfaz de red llamada *myVmNvaVMNic*, la asoció a la máquina virtual y asignó la dirección IP privada *10.0.2.4* a la interfaz de red. Habilite el reenvío IP para la interfaz de red con [az network nic update](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

Dentro de la máquina virtual, el sistema operativo o una aplicación que se ejecuta dentro de la máquina virtual, también debe poderse reenviar el tráfico de red. Al implementar una aplicación virtual de red en un entorno de producción, la aplicación normalmente filtra, registra o realiza alguna otra función antes de reenviar el tráfico. Sin embargo, en este artículo el sistema operativo simplemente reenvía todo el tráfico que recibe. Habilite el reenvío IP dentro del sistema operativo de la máquina virtual con [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set).

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
El comando puede tardar un minuto en ejecutarse.

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree dos máquinas virtuales en la red virtual para que pueda validar que el tráfico que procede de la subred *Pública* se enruta a la subred *Privada* mediante la aplicación virtual de red de un paso posterior. 

Cree una máquina virtual en la subred *Pública* con [az vm create](/cli/azure/vm#az_vm_create). El parámetro `--no-wait` permite a Azure ejecutar el comando en segundo plano para que pueda continuar con el siguiente comando. Para simplificar los pasos de este artículo, se usa una contraseña. Las claves se utilizan habitualmente en las implementaciones de producción. Si usa claves, también debe configurar el reenvío del agente SSH. Para más información, consulte la documentación del cliente SSH. Reemplace `<replace-with-your-password>` en el siguiente comando por una contraseña de su elección.

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

La creación de la máquina virtual tarda algunos minutos. Después de crear la máquina virtual, la CLI de Azure muestra información similar al ejemplo siguiente: 

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
Anote el valor de **publicIpAddress**. En un paso posterior, usaremos esta dirección para obtener acceso a la máquina virtual desde Internet.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Redirigir el tráfico a través de una aplicación virtual de red

Use el siguiente comando para crear una sesión SSH con la máquina virtual *myVmMgmt*. Reemplace *<publicIpAddress>* por la dirección IP pública de la máquina virtual. En el ejemplo anterior, la dirección IP era *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Cuando se le solicite una contraseña, escriba la que seleccionó en [Creación de máquinas virtuales](#create-virtual-machines).

Use el siguiente comando para instalar traceroute en la máquina virtual *myVmMgmt*:

```bash 
sudo apt-get install traceroute
```

Use el comando siguiente para probar el enrutamiento del tráfico de red a la máquina virtual *myVmWeb* desde la máquina virtual *myVmMgmt*.

```bash
traceroute myvmweb
```

La respuesta es similar al siguiente ejemplo:

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Puede ver que el tráfico se enruta directamente desde la máquina virtual *myVmMgmt* hasta la máquina virtual *myVmWeb*. Las rutas predeterminadas de Azure enrutan el tráfico entre las subredes. 

Use el comando siguiente para conectarse a la máquina virtual *myVmWeb* mediante SSH desde la máquina virtual *myVmMgmt*:

```bash 
ssh azureuser@myVmWeb
```

Use el siguiente comando para instalar traceroute en la máquina virtual *myVmWeb*:

```bash 
sudo apt-get install traceroute
```

Use el comando siguiente para probar el enrutamiento del tráfico de red a la máquina virtual *myVmMgmt* desde la máquina virtual *myVmWeb*.

```bash
traceroute myvmmgmt
```

La respuesta es similar al siguiente ejemplo:

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Puede ver que el primer salto es 10.0.2.4, que es la dirección IP privada de la aplicación virtual de red. El segundo salto es 10.0.1.4, la dirección IP privada de la máquina virtual *myVmMgmt*. La ruta agregada a la tabla de rutas *myRouteTablePublic* y asociada a la subred *Pública* hizo que Azure enrutara el tráfico mediante la NVA, en lugar de a la subred *Privada* directamente.

Cierre las sesiones de SSH tanto en la máquina virtual *myVmWeb* como en la máquina virtual *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Solucionar problemas de enrutamiento

Como ha aprendido en pasos anteriores, Azure aplica las rutas predeterminadas, que puede, opcionalmente, invalidar con las suyas propias. En ocasiones, puede que el tráfico no se enrute de la forma esperada. Use [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) para determinar cómo se enruta el tráfico entre dos máquinas virtuales. Por ejemplo, el comando siguiente comprueba el enrutamiento del tráfico desde la máquina virtual *myVmWeb* (10.0.0.4) hasta la máquina virtual *myVmMgmt* (10.0.1.4):

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
Tras una breve espera, se devuelve la siguiente salida:

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

La salida le informa que la dirección IP del próximo salto para el tráfico que va desde *myVmWeb* hasta *myVmMgmt* es 10.0.2.4 (la máquina virtual *myVmNva*), que el tipo de próximo salto es *VirtualAppliance* y que la tabla de rutas que da lugar al enrutamiento es *myRouteTablePublic*.

Las rutas eficaces para cada interfaz de red son una combinación de las rutas predeterminadas de Azure y las rutas que defina. Vea todas las rutas eficaces de una interfaz de red de una máquina virtual con [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table). Por ejemplo, para mostrar las rutas eficaces de la interfaz de red *MyVmWebVMNic* de la máquina virtual *myVmWeb*, escriba el siguiente comando:

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

Se devuelven todas las rutas predeterminadas y la ruta que agregó en un paso anterior.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, use [az group delete](/cli/azure/group#az_group_delete) para quitar el grupo de recursos y todos los recursos que contenga.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, creó una tabla de rutas y la asoció a una subred. Creó una aplicación virtual de red que enrutó el tráfico desde una subred pública hasta una subred privada. Aunque puede implementar muchos recursos de Azure en una red virtual, no es el caso de los recursos de algunos servicios de PaaS de Azure. Pero puede restringir el acceso a los recursos de algunos servicios de PaaS de Azure solo al tráfico que procede de una subred de una red virtual. Avance al siguiente tutorial para aprender a restringir el acceso de red a los recursos de PaaS de Azure.

> [!div class="nextstepaction"]
> [Restringir el acceso de red a los recursos de PaaS](virtual-network-service-endpoints-configure.md#azure-cli)
