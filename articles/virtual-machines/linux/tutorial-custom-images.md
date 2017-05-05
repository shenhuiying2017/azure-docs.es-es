---
title: "Crear imágenes de máquina virtual personalizadas con la CLI de Azure | Microsoft Docs"
description: "Tutorial: crear una imagen de máquina virtual personalizada mediante la CLI de Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: f7fd7d63e6bb1da7022cef782e3b84ea20a64c31
ms.lasthandoff: 04/26/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Crear una imagen personalizada de una máquina virtual de Azure mediante la CLI

En este tutorial, obtendrá información sobre cómo crear su propia imagen personalizada de una máquina virtual de Azure. Las imágenes personalizadas son como las imágenes de Marketplace, pero las puede crear usted mismo. Las imágenes personalizadas pueden usarse para configuraciones de arranque como la carga previa de aplicaciones, configuraciones de aplicaciones y otras configuraciones del sistema operativo. Al crear una imagen personalizada, se incluyen en ella la máquina virtual junto a todos los discos conectados. 

Se pueden completar los pasos de este tutorial con la versión más reciente de la [CLI 2.0 de Azure](/cli/azure/install-azure-cli).

Para completar el ejemplo de este tutorial, debe tener una máquina virtual existente. Si es necesario, este [ejemplo de script](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) puede crear una. Al trabajar en el tutorial, reemplace los nombres de grupo de recursos y máquina virtual cuando sea necesario.

## <a name="create-an-image"></a>Crear una imagen

Para crear una imagen de una máquina virtual, debe preparar la máquina virtual, desaprovisionando, desasignando y marcando después la máquina virtual de origen como generalizada.

### <a name="deprovision-the-vm"></a>Desaprovisionar la máquina virtual 

El desaprovisionamiento generaliza la máquina virtual mediante la eliminación de información específica de la máquina. Esta generalización hace posible implementar muchas máquinas virtuales a partir de una sola imagen. Durante el desaprovisionamiento, el nombre de host se restablece a `localhost.localdomain`. También se eliminan las claves de host SSH, las configuraciones de servidor de nombres, la contraseña raíz y las concesiones DHCP almacenadas en caché.

Para desaprovisionar la máquina virtual, use el agente de máquina virtual de Azure (waagent). El agente de máquina virtual de Azure está instalado en la máquina virtual y administra el aprovisionamiento y la interacción con el controlador de tejido de Azure. Consulte la [Guía de usuario del Agente de Linux de Azure](agent-user-guide.md) para más información.

Conéctese a la máquina virtual mediante SSH y ejecute el comando para desaprovisionar la máquina virtual. Con el argumento `+user` también se elimina la última cuenta de usuario aprovisionada y los datos asociados. Reemplace la dirección IP de ejemplo con la dirección IP pública de la máquina virtual.

```bash
ssh azureuser@52.174.34.95 sudo waagent -deprovision+user -force
```
Cierre la sesión SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desasignar y marcar la máquina virtual como generalizada

Para crear una imagen, es necesario desasignar la máquina virtual. Desasigne la máquina virtual mediante [az vm deallocate](/cli//azure/vm#deallocate). 
   
```azurecli
az vm deallocate --resource-group myRGCaptureImage --name myVM
```

Por último, establezca el estado de la máquina virtual como generalizado con [az vm generalize](/cli//azure/vm#generalize).
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

### <a name="capture-the-image"></a>Capturar la imagen

Ahora puede crear una imagen de la máquina virtual con [az image create](/cli//azure/image#create). En el ejemplo siguiente se crea una imagen denominada `myImage` a partir de la máquina virtual llamada `myVM`.
   
```azurecli
az image create --resource-group myResourceGroupImages --name myImage --source myVM
```
 
## <a name="create-a-vm-from-an-image"></a>Crear una máquina virtual a partir de una imagen

Puede crear una máquina virtual mediante una imagen con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual denominada `myVMfromImage` a partir de la imagen llamada `myImage`.

```azurecli
az vm create --resource-group myResourceGroupImages --name myVMfromImage --image myImage --admin-username azureuser --generate-ssh-keys
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido sobre la creación de imágenes de máquina virtual personalizadas. Avance al siguiente tutorial para obtener información sobre máquinas virtuales de alta disponibilidad.

[Creación de máquinas virtuales de alta disponibilidad](tutorial-availability-sets.md).


