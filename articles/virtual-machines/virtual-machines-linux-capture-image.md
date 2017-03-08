---
title: "Captura de una máquina virtual Linux con la CLI de Azure 2.0 | Microsoft Docs"
description: "Captura y generalización de una imagen de una máquina virtual de Azure basada en Linux mediante discos administrados creados con la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 93abc8b1b14f58b0d0be52517a2b63dfe2dc32fb
ms.openlocfilehash: c72f576d992c9adfa83b9744672397045833c43f
ms.lasthandoff: 02/27/2017


---
# <a name="how-to-generalize-and-capture-a-linux-virtual-machine"></a>Procedimiento para generalizar y capturar una máquina virtual Linux
Para volver a usar máquinas virtuales (VM) implementadas y configuradas en Azure, capture una imagen de la VM. El proceso implica también la generalización de la VM para quitar la información de cuenta personal antes de implementar nuevas VM a partir de la imagen. En este artículo se detalla cómo capturar una imagen de máquina virtual con la CLI de Azure 2.0 para una máquina virtual mediante Azure Managed Disks. Estos discos se controlan mediante la plataforma de Azure y no requieren preparativos ni ubicación para el almacenamiento. Para obtener más información, consulte [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Información general sobre Azure Managed Disks). En este artículo se detalla cómo capturar una máquina virtual Linux con la CLI de Azure 2.0. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](virtual-machines-linux-capture-image-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!TIP]
> Si desea crear una copia de la máquina virtual de Linux existente con su estado especializado para copia de seguridad o depuración, consulte [Creación de una copia de una máquina virtual Linux que se ejecuta en Azure](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Y si desea cargar un VHD de Linux desde una máquina virtual local, consulte [Carga y creación de una máquina virtual Linux desde una imagen de disco personalizada](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  


## <a name="before-you-begin"></a>Antes de empezar
Asegúrese de que se cumplen los siguientes requisitos previos:

* **VM de Azure creada en el modelo de implementación de Resource Manager**: si no ha creado una VM de Linux, puede usar el [portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), la [CLI de Azure](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [plantillas de Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Configure la máquina virtual como considere necesario. Por ejemplo, [agregue discos de datos](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), aplique actualizaciones e instale aplicaciones. 

También necesita tener instalada la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) e iniciar sesión en una cuenta de Azure con [az login](/cli/azure/#login).

## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos base para capturar una imagen de una VM con Linux en Azure. En el resto del documento puede encontrar información más detallada y el contexto de cada paso, comenzando [aquí](#detailed-steps). En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `myVM` y `myImage`.

1. Desaprovisione la VM de origen:

    ```bash
    ssh ops@myvm.westus.cloudapp.azure.com
    sudo waagent -deprovision+user -force
    exit
    ```

2. Desasigne la VM con [az vm deallocate](/cli/azure/vm#deallocate):

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Generalice la VM con [az vm generalize](/cli/azure/vm#generalize):
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

4. Cree una imagen a partir del recurso de VM con [az image create](/cli/azure/image#create):
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```

5. Cree una VM a partir de un recurso de imagen con [az vm create](/cli/azure/vm#create):

    ```azurecli
    az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="detailed-steps"></a>Pasos detallados
En los pasos siguientes, se desaprovisiona una VM existente, se desasigna y generaliza el recurso de VM y, luego, se crea una imagen. Puede usar esta imagen para crear VM en cualquier grupo de recursos dentro de su suscripción. Este proceso proporciona a [Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) una ventaja con respecto a los discos no administrados. Con los discos no administrados, se crea una copia de blob del disco duro virtual (VHD) subyacente y, después, están limitados a la creación de VM en la misma cuenta de almacenamiento que el blob de disco duro virtual copiado. Con los discos administrados, se crea un recurso de imagen que puede implementarse en toda la suscripción.

## <a name="step-1-remove-the-azure-linux-agent"></a>Paso 1: Quitar el agente Linux de Azure
Para que la VM esté lista para la generalización, debe desaprovisionar la VM con el agente de VM de Azure para eliminar archivos y datos. Use el comando **waagent** con el parámetro **deprovision** en la VM con Linux de destino. Consulte la [Guía de usuario del Agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información.

1. Conexión a una máquina virtual Linux de Azure mediante un cliente SSH.
2. En la ventana de SSH, escriba el siguiente comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Solo puede ejecutar este comando en una máquina virtual que quiera capturar como imagen. No garantiza que se haya borrado toda información sensible de la imagen o que sea adecuada para su redistribución.
 
3. Escriba **y** para continuar. Puede agregar el parámetro **-force** para evitar este paso de confirmación.
4. Una vez que se complete el comando, escriba **exit**. Este paso cierra el cliente de SSH.

## <a name="step-2-capture-the-vm"></a>Paso 2: Capturar la máquina virtual
Use la CLI de Azure 2.0 para generalizar y capturar la máquina virtual. En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetro de ejemplo incluyen **myResourceGroup**, **myVnet** y **myVM**.

1. Desasigne la VM que desaprovisionó con [az vm deallocate](/cli//azure/vm#deallocate). En el ejemplo siguiente se desasigna la VM `myVM` en el grupo de recursos denominado `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Generalice la VM con [az vm generalize](/cli//azure/vm#generalize). En el ejemplo siguiente se generaliza la VM llamada `myVM` en el grupo de recursos `myResourceGroup`:
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

3. Ahora cree una imagen a partir del recurso de VM con [az image create](/cli//azure/image#create). En el ejemplo siguiente se crea una imagen denominada `myImage` en el grupo de recursos llamado `myResourceGroup` utilizando el recurso de VM `myVM`:
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```
   
   > [!NOTE]
   > La imagen se crea en el mismo grupo de recursos que la VM de origen. Puede crear VM en cualquier grupo de recursos dentro de la suscripción a partir de esta imagen. Desde una perspectiva de administración, puede que desee crear un grupo de recursos específico para las imágenes y los recursos de VM.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Paso 3: Crear una máquina virtual a partir de la imagen capturada
Cree una VM con la imagen que ha creado con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una VM denominada `myVMDeployed` a partir de la imagen llamada `myImage`:

```azurecli
az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```

Con los discos administrados, puede crear VM en cualquier grupo de recursos dentro de la suscripción a partir de una imagen. Este comportamiento es un cambio con respecto a los discos no administrados donde solo se pueden crear VM en la misma cuenta de almacenamiento que el VHD de origen. Para crear una VM en un grupo de recursos diferente al de la imagen, especifique el identificador de recurso completo para la imagen. Use [az image list](/cli/azure/image#list) para ver una lista de imágenes. La salida es similar a la del ejemplo siguiente:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

En el ejemplo siguiente se utiliza **az vm create** para crear una VM en un grupo de recursos diferente al de la imagen de origen especificando el identificador de recurso de imagen:

```azurecli
az vm create --resource-group myOtherResourceGroup --name myOtherVMDeployed 
    --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage"
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```


### <a name="verify-the-deployment"></a>Comprobar la implementación
Ahora conéctese por SSH a la máquina virtual que creó para comprobar la implementación y empezar a usar la nueva máquina virtual. Para conectarse a través de SSH, busque la dirección IP o el FQDN de la VM con [az vm show](/cli/azure/vm#show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM --show-details
```

## <a name="next-steps"></a>Pasos siguientes
Puede crear varias VM a partir de la imagen de VM de origen. Si necesita realizar cambios en la imagen: 

- Cree una máquina virtual a partir de la imagen.
- Realice actualizaciones o cambios de configuración.
- Vuelva a seguir los pasos para desaprovisionar, desasignar, generalizar y crear una imagen.
- Use esta nueva imagen para implementaciones futuras. Si lo desea, elimine la imagen original.

Para obtener más información sobre la administración de las máquinas virtuales con la CLI, vea [CLI de Azure 2.0](/cli/azure/overview).

