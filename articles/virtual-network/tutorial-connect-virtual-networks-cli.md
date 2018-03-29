---
title: 'Conexión de redes virtuales con emparejamiento de redes virtuales: CLI de Azure | Microsoft Docs'
description: Aprenda a conectar redes virtuales con el emparejamiento de redes virtuales.
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
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: bbf2e757e2d9ad76c59394ba0138a61fd4029d15
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Conexión de redes virtuales con emparejamiento de redes virtuales con la CLI de Azure

Puede conectar redes virtuales entre sí con el emparejamiento de redes virtuales. Una vez que las redes virtuales están emparejadas, los recursos de ambas se pueden comunicar entre sí con el mismo ancho de banda y la misma latencia que si estuvieran en la misma red virtual. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Crear dos redes virtuales
> * Conectar dos redes virtuales con el emparejamiento de redes virtuales
> * Implementar una máquina virtual (VM) en cada red virtual
> * Comunicarse entre máquinas virtuales

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI en un entorno local, para esta guía de inicio rápido es preciso que ejecute la versión 2.0.28 de la CLI de Azure o una versión posterior. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Creación de redes virtuales

Antes de crear una red virtual, cree un grupo de recursos para ella y los demás recursos que se crearon en este artículo. Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Cree la red virtual con el comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). En el ejemplo siguiente se crea una red virtual denominada *myVirtualNetwork1* con el prefijo de dirección *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Cree una red virtual denominada *myVirtualNetwork2* con el prefijo de dirección *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Emparejamiento de redes virtuales

Los emparejamientos se establecen entre los identificadores de red virtual, por lo que primero debe obtener el identificador de cada red virtual con [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) y almacenarlo en una variable.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Cree un emparejamiento de *myVirtualNetwork1* con *myVirtualNetwork2* con [az network vnet peering create](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Si el parámetro `--allow-vnet-access` no se especifica, se establece un emparejamiento, pero no fluye la comunicación.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

En la salida que se devuelve al ejecutarse el comando anterior, verá que **peeringState** está en estado *iniciado*. El emparejamiento permanece en estado *iniciado* hasta que cree el emparejamiento de *myVirtualNetwork2* con *myVirtualNetwork1*. Cree un emparejamiento de *myVirtualNetwork2* con *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

En la salida que se devuelve al ejecutarse el comando anterior, verá que **peeringState** está en estado *conectado*. Azure también cambia el estado del emparejamiento *myVirtualNetwork1-myVirtualNetwork2* a *conectado*. Confirme que el estado de del emparejamiento *myVirtualNetwork1-myVirtualNetwork2* cambia a *conectado* con [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Los recursos de una red virtual no se comunican con los de la otra hasta que el estado **peeringState** de los emparejamientos de ambas redes virtuales es *conectado*. 

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree una máquina virtual en cada red virtual para que puedan comunicarse entre ellas en un paso posterior.

### <a name="create-the-first-vm"></a>Creación de la primera máquina virtual

Cree la máquina virtual con [az vm create](/cli/azure/vm#az_vm_create). En el ejemplo siguiente se crea una máquina virtual llamada *myVm1* en la red virtual *myVirtualNetwork1*. Si las claves SSH no existen en la ubicación de claves predeterminada, el comando las crea. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`. La opción `--no-wait` crea la máquina virtual en segundo plano, así que puede continuar con el siguiente paso.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Creación de la segunda máquina virtual

Cree una red virtual en la red virtual *myVirtualNetwork2*.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

La máquina virtual tarda en crearse unos minutos. Después de crear la máquina virtual, la CLI de Azure muestra información similar a la del ejemplo siguiente: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anote el valor de **publicIpAddress**. En un paso posterior, usaremos esta dirección para obtener acceso a la máquina virtual desde Internet.

## <a name="communicate-between-vms"></a>Comunicarse entre máquinas virtuales

Use el siguiente comando para crear una sesión de SSH con la máquina virtual *myVm2*. Reemplace `<publicIpAddress>` con la dirección IP pública de la máquina virtual. En el ejemplo anterior, la dirección IP pública es *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Haga ping a la máquina virtual en *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Recibirá cuatro respuestas. 

Cierre la sesión de SSH en la máquina virtual *myVm2*. 

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, use [az group delete](/cli/azure/group#az_group_delete) para quitar el grupo de recursos y todos los recursos que contenga.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

**<a name="register"></a>Registro para la versión preliminar del emparejamiento de red virtual global**

El emparejamiento de redes virtuales en las mismas regiones tiene disponibilidad general. El emparejamiento de redes virtuales de diferentes regiones actualmente se encuentra en versión preliminar. Consulte las [actualizaciones de redes virtuales](https://azure.microsoft.com/updates/?product=virtual-network) para ver las regiones disponibles. Para emparejar redes virtuales entre regiones, primero tiene que registrarse en la versión preliminar; para ello, complete los pasos siguientes (en la suscripción en que se encuentra cada red virtual que desea emparejar):

1. Regístrese para obtener la versión preliminar mediante estos comandos:

  ```azurecli-interactive
  az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
  az provider register --name Microsoft.Network
  ```

2. Escriba el siguiente comando para confirmar que está registrado para la versión preliminar:

  ```azurecli-interactive
  az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
  ```

  Si intenta emparejar redes virtuales en diferentes regiones antes de que la salida **RegistrationState** que recibe después de escribir el comando anterior sea **Registrado** para ambas suscripciones, el emparejamiento produce un error.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a conectar dos redes con el emparejamiento de redes virtuales. En este artículo, ha aprendido a conectar dos redes, de la misma ubicación de Azure, con el emparejamiento de redes virtuales. También puede emparejar redes virtuales de [diferentes regiones](#register), en [diferentes suscripciones de Azure](create-peering-different-subscriptions.md#portal) y puede crear [diseños de red de concentrador y radio](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con emparejamiento. Antes de emparejar redes virtuales de producción, se recomienda familiarizarse en detalle con la [introducción al emparejamiento](virtual-network-peering-overview.md), la [administración del emparejamiento](virtual-network-manage-peering.md) y los [límites de red virtual ](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Puede [conectar su equipo a una red virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mediante VPN e interactuar con recursos en una red virtual, o en redes virtuales emparejadas. Siga con los ejemplos de scripts reutilizables para completar muchas de las tareas descritas en los artículos sobre las redes virtuales.

> [!div class="nextstepaction"]
> [Ejemplos de scripts de red virtual](../networking/cli-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
