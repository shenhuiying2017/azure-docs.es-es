---
title: "Captura de una imagen de una máquina virtual Linux en Azure mediante la CLI 2.0 | Microsoft Docs"
description: "Capture una imagen de una máquina virtual de Azure que se usará para implementaciones masivas mediante la CLI de Azure 2.0."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f6fbefc212197ca8802e7857c67a5b9c297770a8
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Creación de una imagen de una máquina virtual o un disco duro virtual

<!-- generalize, image - extended version of the tutorial-->

Para crear varias copias de una máquina virtual (VM) para usarla en Azure, capture una imagen de la máquina virtual o el disco duro virtual del sistema operativo. Para crear una imagen, es necesario quitar la información de cuenta personal lo cual hace que sea más seguro implementarla varias veces. En los pasos siguientes, se desaprovisiona una máquina virtual existente, se desasigna y se crea una imagen. Puede usar esta imagen para crear VM en cualquier grupo de recursos dentro de su suscripción.

Si desea crear una copia de la máquina virtual Linux existente para la realización de una copia de seguridad o para su depuración, o cargar un disco duro virtual Linux especializado desde una máquina virtual local, consulte [Carga y creación de una máquina virtual Linux desde una imagen de disco personalizada](upload-vhd.md).  

También puede usar **Packer** para crear la configuración personalizada. Para más información sobre el uso de esta solución, consulte [Uso de Packer para crear imágenes de máquinas virtuales Linux en Azure](build-image-with-packer.md).


## <a name="before-you-begin"></a>Antes de empezar
Asegúrese de que se cumplen los siguientes requisitos previos:

* Necesita crear una máquina virtual de Azure en el modelo de implementación de Resource Manager mediante discos administrados. Si no ha creado una máquina virtual Linux, puede usar el [portal](quick-create-portal.md), la [CLI de Azure](quick-create-cli.md) o [plantillas de Resource Manager](cli-deploy-templates.md). Configure la máquina virtual como considere necesario. Por ejemplo, [agregue discos de datos](add-disk.md), aplique actualizaciones e instale aplicaciones. 

* También necesita tener instalada la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) e iniciar sesión en una cuenta de Azure con [az login](/cli/azure/#login).

## <a name="quick-commands"></a>Comandos rápidos

Para una versión simplificada de este tema, con fines de prueba, evaluación o aprendizaje sobre máquinas virtuales en Azure, consulte [Crear una imagen personalizada de una máquina virtual de Azure mediante la CLI](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Paso 1: Desaprovisionamiento de la máquina virtual
Desaprovisione la máquina virtual con el agente de máquinas virtuales de Azure para eliminar los archivos y datos específicos de la máquina. Use el comando `waagent` con el parámetro *-deprovision+user* en la máquina virtual Linux de origen. Consulte la [Guía de usuario del Agente de Linux de Azure](../windows/agent-user-guide.md) para más información.

1. Conexión a una máquina virtual Linux de Azure mediante un cliente SSH.
2. En la ventana de SSH, escriba el siguiente comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > Solo puede ejecutar este comando en una máquina virtual que quiera capturar como imagen. No garantiza que se haya borrado toda información sensible de la imagen o que sea adecuada para su redistribución. El parámetro *+user* también quita la última cuenta de usuario aprovisionada. Si desea mantener las credenciales de la cuenta en la máquina virtual, use *-deprovision* para dejar la cuenta de usuario implementada.
 
3. Escriba **y** para continuar. Puede agregar el parámetro **-force** para evitar este paso de confirmación.
4. Una vez que se complete el comando, escriba **exit**. Este paso cierra el cliente de SSH.

## <a name="step-2-create-vm-image"></a>Paso 2: Crear la imagen de máquina virtual
Use la CLI de Azure 2.0 para marcar la máquina virtual como generalizada y capture la imagen. En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetro de ejemplo incluyen *myResourceGroup*, *myVnet* y *myVM*.

1. Desasigne la VM que desaprovisionó con [az vm deallocate](/cli//azure/vm#deallocate). En el ejemplo siguiente se desasigna la máquina virtual denominada "*myVM*" en el grupo de recursos *myResourceGroup*:
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Marque la máquina virtual como generalizada con [az vm generalize](/cli//azure/vm#generalize). En el ejemplo siguiente se generaliza la máquina virtual denominada *myVM* en el grupo de recursos *myResourceGroup*:
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Ahora cree una imagen a partir del recurso de VM con [az image create](/cli//azure/image#create). En el ejemplo siguiente se crea una imagen denominada *myImage* en el grupo de recursos llamado *myResourceGroup* por medio del recurso de máquina virtual denominado *myVM*:
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > La imagen se crea en el mismo grupo de recursos que la VM de origen. Puede crear VM en cualquier grupo de recursos dentro de la suscripción a partir de esta imagen. Desde una perspectiva de administración, puede que desee crear un grupo de recursos específico para las imágenes y los recursos de VM.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Paso 3: Crear una máquina virtual a partir de la imagen capturada
Cree una VM con la imagen que ha creado con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual denominada *myVMDeployed* a partir de la imagen *myImage*:

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Creación de la máquina virtual en otro grupo de recursos 

Puede crear máquinas virtuales en cualquier grupo de recursos dentro de la suscripción a partir de una imagen. Para crear una VM en un grupo de recursos diferente al de la imagen, especifique el identificador de recurso completo para la imagen. Use [az image list](/cli/azure/image#list) para ver una lista de imágenes. La salida es similar a la del ejemplo siguiente:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

En el ejemplo siguiente se utiliza [az vm create](/cli/azure/vm#create) para crear una VM en un grupo de recursos diferente al de la imagen de origen especificando el identificador de recurso de imagen:

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Paso 4: Comprobar la implementación

Ahora conéctese por SSH a la máquina virtual que creó para comprobar la implementación y empezar a usar la nueva máquina virtual. Para conectarse a través de SSH, busque la dirección IP o el FQDN de la VM con [az vm show](/cli/azure/vm#show):

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Pasos siguientes
Puede crear varias VM a partir de la imagen de VM de origen. Si necesita realizar cambios en la imagen: 

- Cree una máquina virtual a partir de la imagen.
- Realice actualizaciones o cambios de configuración.
- Vuelva a seguir los pasos para desaprovisionar, desasignar, generalizar y crear una imagen.
- Use esta nueva imagen para implementaciones futuras. Si lo desea, elimine la imagen original.

Para obtener más información sobre la administración de las máquinas virtuales con la CLI, vea [CLI de Azure 2.0](/cli/azure/overview).

