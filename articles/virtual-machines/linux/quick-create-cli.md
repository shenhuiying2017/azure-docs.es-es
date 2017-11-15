---
title: "Inicio rápido de Azure: creación de máquinas virtuales con la CLI | Microsoft Docs"
description: "Aprenda rápidamente a crear una máquina virtual con la CLI de Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/13/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c10f4c575c7b9a68e7a1ff8fedf0f17f4fb8599d
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Creación de una máquina virtual Linux con la CLI de Azure

La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En este inicio rápido se detalla cómo usar la CLI de Azure para implementar máquinas virtuales en las que se ejecuta el servidor de Ubuntu. Una vez implementado el servidor, se crea una conexión SSH y se instala un servidor web NGINX.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Create virtual machine

Cree la máquina virtual con el comando [az vm create](/cli/azure/vm#az_vm_create). 

En el ejemplo siguiente, se crea una máquina virtual denominada *myVM* y las claves SSH si aún no existen en una ubicación de claves predeterminada. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`.  

```azurecli-interactive 
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Cuando se ha creado la máquina virtual, la CLI de Azure muestra información similar al ejemplo siguiente. Anote el valor de `publicIpAddress`. Esta dirección se utiliza para tener acceso a la máquina virtual.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Apertura del puerto 80 para el tráfico web 

De forma predeterminada, solo se permiten conexiones mediante SSH con las máquinas virtuales Linux implementadas en Azure. Si esta máquina virtual va a ser un servidor web, debe abrir el puerto 80 desde Internet. Use el comando [az vm open-port](/cli/azure/vm#open-port) para abrir el puerto deseado.  
 
 ```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Conexión SSH con la máquina virtual

Ejecute el comando siguiente para crear una sesión SSH con la máquina virtual. Asegúrese de reemplazar *<publicIpAddress>* por la dirección IP pública correcta de la máquina virtual.  En el ejemplo anterior, la dirección IP era *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Instalación de NGINX

Use los siguientes comandos para actualizar los orígenes del paquete e instalar el paquete NGINX más reciente. 

```bash 
# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Visualización de la página de bienvenida de NGINX

Con NGINX instalado y el puerto 80 abierto en la máquina virtual desde Internet, puede usar el explorador web que elija para ver la página principal de NGINX. Asegúrese de utilizar el valor de *publicIpAddress* que ha anotado antes para visitar la página predeterminada. 

![Sitio predeterminado de NGINX](./media/quick-create-cli/nginx.png) 


## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados. Salga de la sesión SSH a la máquina virtual y, luego, elimine los recursos como se indica a continuación:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha implementado una máquina virtual simple y una regla de grupo de seguridad de red, y ha instalado un servidor web. Para más información acerca de las máquinas virtuales de Azure, continúe con el tutorial de máquinas virtuales Linux.


> [!div class="nextstepaction"]
> [Tutoriales de máquinas virtuales Linux de Azure](./tutorial-manage-vm.md)
