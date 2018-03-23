---
title: 'Creación de una red virtual de Azure: CLI de Azure | Microsoft Docs'
description: Aprenda a crear una red virtual rápidamente mediante la CLI de Azure. Una red virtual permite que los recursos de Azure, como las máquinas virtuales, se comuniquen entre sí de forma privada y con Internet.
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
ms.date: 03/09/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 46fec48720c817072ce838dd2e4c07725be5a7fe
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Creación de una red virtual mediante la CLI de Azure

Una red virtual permite que los recursos de Azure, como las máquinas virtuales (VM), se comuniquen entre sí de forma privada y con Internet. En este artículo aprenderá a crear una red virtual. Después de crear una red virtual, implementará dos máquinas virtuales en la red virtual. Luego, se conectará a una máquina virtual desde Internet y se comunicará de forma privada con la otra máquina virtual.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0.28 o posterior de la CLI de Azure. Ejecute `az --version` para ver cuál es la versión instalada. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-virtual-network"></a>Crear una red virtual

Para poder crear una red virtual, debe crear un grupo de recursos que contenga la red virtual. Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Cree la red virtual con el comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). En el ejemplo siguiente se crea una red virtual predeterminada denominada *myVirtualNetwork* con una subred denominada *default*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree dos máquinas virtuales en la red virtual:

### <a name="create-the-first-vm"></a>Creación de la primera máquina virtual

Cree la máquina virtual con [az vm create](/cli/azure/vm#az_vm_create). Si las claves SSH no existen en la ubicación de claves predeterminada, el comando las crea. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`. La opción `--no-wait` crea la máquina virtual en segundo plano, así que puede continuar con el siguiente paso. En el ejemplo siguiente se crea una máquina virtual llamada *myVm1*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Creación de la segunda máquina virtual

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

La maquina virtual tarda unos minutos en crearse. Después de crear la máquina virtual, la CLI de Azure devuelve una salida similar a la del ejemplo siguiente: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Anote el valor de **publicIpAddress**. Esta dirección se utiliza para conectarse a la máquina virtual desde Internet en el paso siguiente.

## <a name="connect-to-a-vm-from-the-internet"></a>Conexión a una máquina virtual desde Internet

Reemplace `<publicIpAddress>` por la dirección IP pública de la máquina virtual *myVm2* en el comando siguiente y, a continuación, escriba el comando siguiente:

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-privately-between-vms"></a>Comunicación privada entre máquinas virtuales

Para confirmar la comunicación privada entre las máquinas virtuales *myVm2* y *myVm1*, escriba el siguiente comando:

```bash
ping myVm1 -c 4
```

Recibirá cuatro respuestas de *10.0.0.4*.

Cierre la sesión SSH con la máquina virtual *myVm2*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede utilizar [az group delete](/cli/azure/group#az_group_delete) para eliminar el grupo de recursos y todos los recursos que contenga:

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha creado una red virtual predeterminada y dos máquinas virtuales. Se ha conectado a una máquina virtual desde Internet y se ha comunicado de forma privada entre las dos máquinas virtuales. Para más información sobre la configuración de la red virtual, consulte [Administración de una red virtual](manage-virtual-network.md). 

De forma predeterminada, Azure permite la comunicación privada sin restricciones entre máquinas virtuales, pero solo permite sesiones SSH entrantes a las máquinas virtuales Linux desde Internet. Para más información sobre cómo permitir o restringir diferentes tipo de comunicaciones de red entre máquinas virtuales, avance al siguiente tutorial.

> [!div class="nextstepaction"]
> [Filtrado del tráfico de red](virtual-networks-create-nsg-arm-cli.md)
