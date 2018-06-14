---
title: 'Guía de inicio rápido: Creación de una red virtual mediante CLI de Azure | Microsoft Docs'
description: En esta guía de inicio rápido, aprenderá a crear una red virtual mediante Azure Portal. Una red virtual permite que los recursos de Azure, como las máquinas virtuales, se comuniquen entre sí de forma privada y con Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: bb45b2b4ecd89187e94066bc81782174738fe3a9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842295"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Guía de inicio rápido: Creación de una red virtual mediante la CLI de Azure

Una red virtual permite que los recursos de Azure, como máquinas virtuales (VM), se comuniquen entre sí de forma privada y con Internet. En esta guía de inicio rápido aprenderá a crear una red virtual. Después de crear una red virtual, implementará dos máquinas virtuales en la red virtual. Luego, se conectará a una máquina virtual desde Internet y se comunicará de forma privada con la otra máquina virtual.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI en un entorno local, para esta guía de inicio rápido es preciso que ejecute la versión 2.0.28 de la CLI de Azure o una versión posterior. Ejecute `az --version` para ver cuál es la versión instalada. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 


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

## <a name="communicate-between-vms"></a>Comunicarse entre máquinas virtuales

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

En esta guía de inicio rápido, ha creado una red virtual predeterminada y dos máquinas virtuales. Se ha conectado a una máquina virtual desde Internet y se ha comunicado de forma privada entre las dos máquinas virtuales. Para más información sobre la configuración de red virtual, consulte [Administración de una red virtual](manage-virtual-network.md). 

De forma predeterminada, Azure permite la comunicación privada sin restricciones entre máquinas virtuales, pero solo permite conexiones de Escritorio remoto entrantes a máquinas virtuales Windows desde Internet. Para aprender a permitir o restringir los distintos tipos de comunicación de red tanto hacia las máquinas virtuales como desde estas, consulte [Filtro del tráfico de red](tutorial-filter-network-traffic.md).
