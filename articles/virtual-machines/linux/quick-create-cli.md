---
title: 'Guía de inicio rápido: Creación de una máquina virtual Linux con la CLI de Azure 2.0 | Microsoft Docs'
description: En esta guía de inicio rápido, aprenderá a utilizar la CLI de Azure 2.0 para crear una máquina virtual Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1c45f8f010d69337d21fce327933990a573988a4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli-20"></a>Guía de inicio rápido: Creación de una máquina virtual Linux con la CLI de Azure 2.0

La CLI de Azure 2.0 se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía de inicio rápido se muestra como usar la CLI de Azure 2.0 para implementar una máquina virtual Linux en Azure que ejecuta Ubuntu. Para ver la máquina virtual en acción, conéctese a la máquina virtual mediante SSH e instale el servidor web de NGINX.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI en un entorno local, en esta guía de inicio rápido es preciso que ejecute la versión 2.0.30 de la CLI de Azure o una posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#az_group_create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Crear máquina virtual

Cree la máquina virtual con el comando [az vm create](/cli/azure/vm#az_vm_create).

En el ejemplo siguiente, se crea una máquina virtual denominada *myVM*, se agrega una cuenta de usuario denominada *azureuser* y se generan claves SSH, si aún no existen en una ubicación de claves predeterminada (*~/.ssh*). Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La creación de la máquina virtual y los recursos auxiliares tarda unos minutos en realizarse. En la salida de ejemplo siguiente se muestra que la operación de creación de la máquina virtual se realizó correctamente.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Tenga en cuenta sus propios valores de `publicIpAddress` en la salida de la máquina virtual. Esta dirección se usa para acceder a la máquina virtual en los siguientes pasos.

## <a name="open-port-80-for-web-traffic"></a>Apertura del puerto 80 para el tráfico web

De forma predeterminada, solo las conexiones de SSH se abren cuando se crea una máquina virtual Linux en Azure. Use [az vm open-port](/cli/azure/vm#az_vm_open_port) para abrir el puerto TCP 80 que se usará con el servidor web de NGINX:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Conexión a la máquina virtual

Conéctese a la máquina virtual mediante SSH, como hace habitualmente. Reemplace **publicIpAddress** por la dirección IP pública de la máquina virtual como se indica en la salida anterior de la máquina virtual:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Instalación del servidor web

Para ver la máquina virtual en acción, instale al servidor de web de NGINX. Para actualizar los orígenes del paquete e instalar el paquete más reciente de NGINX, ejecute los siguientes comandos en la sesión de SSH.

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Cuando haya finalizado, use el comando `exit` para salir de la sesión de SSH.

## <a name="view-the-web-server-in-action"></a>Visualización del servidor web en acción

Con NGINX instalado y el puerto 80 ahora abierto en la máquina virtual desde Internet, use el explorador web que prefiera para ver la página principal de NGINX predeterminada. Use la dirección IP pública de la máquina virtual que obtuvo en el paso anterior. En el ejemplo siguiente se muestra el sitio web de NGINX predeterminado:

![Sitio NGINX predeterminado](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#az_group_delete) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados. Asegúrese de que ha cerrado la sesión de SSH en la máquina virtual y elimine los recursos de la manera siguiente:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, implementará una máquina virtual sencilla, abrirá un puerto de red para el tráfico web e instalará un servidor web básico. Para más información acerca de las máquinas virtuales de Azure, continúe con el tutorial de máquinas virtuales Linux.


> [!div class="nextstepaction"]
> [Tutoriales de máquinas virtuales Linux de Azure](./tutorial-manage-vm.md)
