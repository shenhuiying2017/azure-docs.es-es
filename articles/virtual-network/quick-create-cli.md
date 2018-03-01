---
title: "Creación de una red virtual: CLI de Azure | Microsoft Docs"
description: "Aprenda a crear una red virtual rápidamente mediante la CLI de Azure. Una red virtual permite que una gran cantidad de tipos de recursos de Azure se comuniquen entre ellos de forma privada."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 2cb32ddc67060d9860d172b90cc399622c52b04b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Creación de una red virtual mediante la CLI de Azure

En este artículo aprenderá a crear una red virtual. Después de crear una red virtual, implementará dos máquinas virtuales en esa red y podrán comunicarse entre ellas de forma privada.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para ver cuál es la versión instalada. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#az_group_create). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*. Todos los recursos de Azure deben crearse dentro de una ubicación (o región) de Azure.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-network"></a>Crear una red virtual

Cree una red virtual con el comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). En el ejemplo siguiente se crea una red virtual predeterminada denominada *myVirtualNetwork* con una subred denominada *default*. Puesto que no se especifica ninguna ubicación, Azure crea la red virtual en la misma ubicación que el grupo de recursos.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

Una vez creada la red virtual, una parte de la información devuelta se muestra tal como se indica a continuación.

```azurecli
"newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
```

Todas las redes virtuales tienen uno o más prefijos de dirección asignado. Puesto que un prefijo de dirección no se especificó al crear la red virtual, Azure define el espacio de direcciones 10.0.0.0/16 de forma predeterminada. El espacio de direcciones se especifica en la notación CIDR. El espacio de direcciones 10.0.0.0/16 abarca el intervalo de direcciones 10.0.0.0-10.0.255.254.

Otra parte de la información que se devuelve es el elemento **addressPrefix** de *10.0.0.0/24* para la red *default* que se especificó en el comando. Una red virtual contiene cero o más subredes. El comando creó una sola subred denominada *default*, pero no se especificó ningún prefijo de dirección para la subred. Cuando no se especifica un prefijo de dirección en una red o subred virtual, Azure define 10.0.0.0/24 como prefijo de dirección para la primera subred de forma predeterminada. Como resultado, la subred abarca el intervalo de direcciones 10.0.0.0-10.0.0.254, pero solo están disponibles las direcciones 10.0.0.4-10.0.0.254, ya que Azure reserva las cuatro primeras direcciones (0-3) y la última dirección de cada subred.

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Una red virtual permite que una gran cantidad de tipos de recursos de Azure se comuniquen entre ellos de forma privada. Un tipo de recurso que se puede implementar en una red virtual es una máquina virtual. Cree dos máquinas virtuales en la red virtual y, más adelante, tendrá la oportunidad de validar y comprender cómo funciona la comunicación entre máquinas virtuales en una red virtual.

Cree la máquina virtual con el comando [az vm create](/cli/azure/vm#az_vm_create). En el ejemplo siguiente se crea una máquina virtual denominada *myVm1*. Si las claves SSH no existen en la ubicación de claves predeterminada, el comando las crea. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`. La opción `--no-wait` crea la máquina virtual en segundo plano para que pueda realizar el siguiente paso.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

Azure crea automáticamente la máquina virtual en la subred *default* de la red virtual *myVirtualNetwork*, ya que la red virtual existe en el grupo de recursos y no se especificó ninguna red o subred virtual en el comando. El protocolo DHCP de Azure asigna de forma automática la dirección 10.0.0.4 a la máquina virtual durante la creación, ya que esta es la primera dirección que está disponible en la subred *default*. La ubicación en la que se crea la máquina virtual debe ser la misma ubicación en la que está la red virtual. De todas formas, no es necesario que la máquina virtual esté en el mismo grupo de recursos que la red virtual, aunque lo esté en este artículo.

Cree una segunda máquina virtual. Azure también crea esta máquina virtual en la subred *default* de forma predeterminada.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

La creación de la máquina virtual tarda algunos minutos. Después de crear la máquina virtual, la CLI de Azure devuelve un resultado similar al ejemplo siguiente: 

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

En el ejemplo, verá que el elemento **privateIpAddress** es *10.0.0.5*. El protocolo DHCP de Azure asigna de forma automática la dirección *10.0.0.5* a la máquina virtual porque era la siguiente dirección disponible en la subred *default*. Anote el valor de **publicIpAddress**. En un paso posterior, usaremos esta dirección para obtener acceso a la máquina virtual desde Internet. La dirección IP pública no se asigna mediante los prefijos de direcciones de la red o subred virtual. Las direcciones IP públicas se asignan desde un [grupo de direcciones asignadas a cada región de Azure](https://www.microsoft.com/download/details.aspx?id=41653). Aunque Azure sabe qué dirección IP pública tiene asignada una máquina virtual, el sistema operativo que se ejecuta en una máquina virtual no sabe si tiene asignada alguna dirección IP pública.

## <a name="connect-to-a-virtual-machine"></a>Conexión a una máquina virtual

Use el siguiente comando para crear una sesión SSH con la máquina virtual *myVm2*. Reemplace `<publicIpAddress>` con la dirección IP pública de la máquina virtual. En el ejemplo anterior, la dirección IP era *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

## <a name="validate-communication"></a>Validar la comunicación

Use el siguiente comando para confirmar la comunicación con *myVm1* desde *myVm2*:

```bash
ping myVm1 -c 4
```

Recibirá cuatro respuestas de *10.0.0.4*. Se puede comunicar con *myVm1* desde *myVm2*, ya que ambas máquinas virtuales tienen direcciones IP privadas asignadas desde la subred *default*. Es posible hacer ping por nombre de host, dado que Azure proporciona automáticamente la resolución de nombres DNS de todos los hosts de una red virtual.

Utilice el siguiente comando para confirmar la comunicación saliente a Internet:

```bash
ping bing.com -c 4
```

Recibirá cuatro respuestas de bing.com. De forma predeterminada, una máquina virtual que se encuentre en una red virtual puede realizar comunicaciones salientes a Internet.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#az_group_delete) para quitar el grupo de recursos y todos los recursos que contenga. Salga de la sesión SSH en la VM y elimine los recursos.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>pasos siguientes

En este artículo implementó una red virtual predeterminada con una subred y dos máquinas virtuales. Para saber cómo crear una red virtual personalizada que tenga varias subredes y que realice tareas básicas de administración, consulte el tutorial que indica cómo crear y administrar una red virtual personalizada.


> [!div class="nextstepaction"]
> [Crear y administrar una red virtual personalizada](virtual-networks-create-vnet-arm-pportal.md#azure-cli)
