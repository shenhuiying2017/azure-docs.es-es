---
title: 'Tutorial: Uso de una imagen de máquina virtual personalizada en un conjunto de escalado con la CLI de Azure 2.0 | Microsoft Docs'
description: Obtenga información sobre cómo usar la CLI de Azure 2.0 para crear una imagen de máquina virtual personalizada que puede usar para implementar un conjunto de escalado de máquinas virtuales
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b01ff98667523f498c06ba867e017f696059d120
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
ms.locfileid: "30908017"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Tutorial: Creación y uso de una imagen personalizada para conjuntos de escalado de máquinas virtuales con la CLI de Azure 2.0
Al crear el conjunto de escalado, se especifica la imagen que se usará cuando se implementen las instancias de máquina virtual. Para reducir el número de tareas después de implementar las instancias de máquina virtual, puede usar una imagen de máquina virtual personalizada. Esta imagen de máquina virtual personalizada incluye la instalación o configuración de las aplicaciones necesarias. Las instancias de máquina virtual creadas en el conjunto de escalado usan la imagen de máquina virtual personalizada y están listas para atender el tráfico de la aplicación. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear y personalizar una máquina virtual
> * Desaprovisionar y generalizar la máquina virtual
> * Crear una imagen de máquina virtual personalizada
> * Implementar un conjunto de escalado que usa la imagen de máquina virtual personalizada

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, en este tutorial es preciso que ejecute la CLI de Azure 2.0.29 o versiones posteriores. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Creación y configuración de una máquina virtual de origen

>[!NOTE]
> Este tutorial le guía por el proceso de creación y uso de una imagen de máquina virtual generalizada. No permite crear un conjunto de escalado a partir de una imagen de máquina virtual especializada.

En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create) y luego cree una máquina virtual con [az vm create](/cli/azure/vm#az_vm_create). Esta máquina virtual se usará después como origen para la imagen de máquina virtual personalizada. En el ejemplo siguiente, se crea una máquina virtual llamada *myVM* en el grupo de recursos llamado *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

La dirección IP pública de la máquina virtual se muestra en la salida del comando [az vm create](/cli/azure/vm#az_vm_create). Conéctese mediante SSH a la dirección IP pública de la máquina virtual, como se indica a continuación:

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

Para personalizar la máquina virtual, vamos a instalar un servidor web básico. Cuando la instancia de máquina virtual del conjunto de escalado se implemente, tendrá todos los paquetes necesarios para ejecutar una aplicación web. Use `apt-get` para instalar *NGINX* de la siguiente manera:

```bash
sudo apt-get install -y nginx
```

El último paso para preparar la máquina virtual para su uso como una imagen personalizada es desaprovisionar la máquina virtual. Este paso elimina de la máquina virtual información específica de la máquina y hace posible implementar muchas máquinas virtuales a partir de una sola imagen. Al desaprovisionar la máquina virtual, el nombre de host se restablece a *localhost.localdomain*. También se eliminan las claves de host SSH, las configuraciones de servidor de nombres, la contraseña raíz y las concesiones DHCP almacenadas en caché.

Para desaprovisionar la máquina virtual, use el agente de máquina virtual de Azure (*waagent*). El agente de máquina virtual de Azure se instala en todas las máquinas virtuales y se utiliza para comunicarse con la plataforma Azure. El parámetro `-force` indica al agente que acepte solicitudes para restablecer la información específica de la máquina.

```bash
sudo waagent -deprovision+user -force
```

Cierre la conexión SSH a la máquina virtual:

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Crear una imagen de máquina virtual personalizada a partir de la máquina virtual de origen
La máquina virtual de origen ahora está personalizada con el servidor web de Nginx instalado. Vamos a crear la imagen de máquina virtual personalizada para usarla con un conjunto de escalado.

Para crear una imagen, es necesario desasignar la máquina virtual. Desasigne la VM con [az vm deallocate](/cli//azure/vm#az_vm_deallocate). A continuación, establezca el estado de la máquina virtual como generalizado con [az vm generalize](/cli//azure/vm#az_vm_generalize) para que la plataforma Azure sepa que la máquina virtual está preparada para usarla como imagen personalizada. Solo se puede crear una imagen de una máquina virtual generalizada:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

La desasignación y generalización de la máquina virtual puede tardar unos minutos.

Ahora cree una imagen de la máquina virtual con [az image create](/cli//azure/image#az_image_create). En el ejemplo siguiente se crea una imagen llamada *myImage* a partir de la máquina virtual:

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Creación de un conjunto de escalado a partir de la imagen de máquina virtual personalizada
Cree un conjunto de escalado con [az vmss create](/cli/az/vmss#az_vmss_create). En lugar de una imagen de plataforma, como *UbuntuLTS* o *CentOS*, especifique el nombre de la imagen de la máquina virtual personalizada. En el ejemplo siguiente, se crea un conjunto de escalado denominado *myScaleSet* que usa la imagen personalizada llamada *myImage* del paso anterior:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

Se tardan unos minutos en crear y configurar todos los recursos de conjunto de escalado y máquinas virtuales.


## <a name="test-your-scale-set"></a>Prueba del conjunto de escalado
Para permitir que el tráfico llegue al conjunto de escalado y comprobar que el servidor web funciona correctamente, cree una regla del equilibrador de carga con [az network lb rule create](/cli/azure/network/lb/rule#create). En el ejemplo siguiente, se crea una regla denominada *myLoadBalancerRuleWeb* que permite tráfico en el puerto *TCP* *80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Para ver el conjunto de escalado en acción, obtenga la dirección IP pública del equilibrador de carga con [az network public-ip show](/cli/azure/network/public-ip#show). En el ejemplo siguiente se obtiene la dirección IP de *myScaleSetLBPublicIP* que se ha creado como parte del conjunto de escalado:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Escriba la dirección IP pública en un explorador web. Se muestra la página web predeterminada de NGIN, como en el ejemplo siguiente:

![NGINX en ejecución desde la imagen de máquina virtual personalizada](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Limpieza de recursos
Para quitar el conjunto de escalado y los recursos adicionales, elimine el grupo de recursos y todos sus recursos con [az group delete](/cli/azure/group#az_group_delete). El parámetro `--no-wait` devuelve el control a la petición de confirmación sin esperar a que finalice la operación. El parámetro `--yes` confirma que desea eliminar los recursos sin pedir confirmación adicional.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, aprendió a crear y utilizar una imagen de máquina virtual personalizada para los conjuntos de escalado con la CLI de Azure 2.0:

> [!div class="checklist"]
> * Crear y personalizar una máquina virtual
> * Desaprovisionar y generalizar la máquina virtual
> * Crear una imagen de máquina virtual personalizada
> * Implementar un conjunto de escalado que usa la imagen de máquina virtual personalizada

Vaya al siguiente tutorial para aprender cómo implementar aplicaciones en el conjunto de escalado.

> [!div class="nextstepaction"]
> [Implementación de aplicaciones en los conjuntos de escalado](tutorial-install-apps-cli.md)