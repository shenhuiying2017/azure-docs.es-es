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
ms.date: 05/02/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 2ce92b3f0a21f80eb4294161d6d3a5275c992600
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Crear una imagen personalizada de una máquina virtual de Azure mediante la CLI

En este tutorial, aprenderá a definir su propia imagen personalizada de una máquina virtual de Azure. Las imágenes personalizadas permiten crear máquinas virtuales con una imagen que ya ha configurado. Las imágenes personalizadas pueden usarse para configuraciones de arranque como la carga previa de aplicaciones y archivos binarios, configuraciones de aplicaciones, definiciones de discos de datos de máquinas virtuales y otras configuraciones del sistema operativo. Al crear una imagen personalizada, se incluirán en la imagen la máquina virtual que haya personalizado junto a todos los discos conectados.

Se pueden completar los pasos de este tutorial con la versión más reciente de la [CLI 2.0 de Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de empezar

Los siguientes pasos explican cómo tomar una máquina virtual existente y convertirla en una imagen personalizada reutilizable que puede usar para crear nuevas instancias de máquinas virtuales.

Para completar el ejemplo de este tutorial, debe tener una máquina virtual. Si es necesario, este [script de ejemplo](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) puede crear una. Al trabajar en el tutorial, reemplace los nombres de grupo de recursos y máquina virtual cuando proceda.

## <a name="prepare-vm"></a>Preparación de la máquina virtual

Para crear una imagen de una máquina virtual, debe preparar la máquina virtual, desaprovisionando, desasignando y marcando después la máquina virtual de origen como generalizada.

### <a name="deprovision-the-vm"></a>Desaprovisionar la máquina virtual 

El desaprovisionamiento generaliza la máquina virtual mediante la eliminación de información específica de la máquina. Esta generalización hace posible implementar muchas máquinas virtuales a partir de una sola imagen. Durante el desaprovisionamiento, el nombre de host se restablece a *localhost.localdomain*. También se eliminan las claves de host SSH, las configuraciones de servidor de nombres, la contraseña raíz y las concesiones DHCP almacenadas en caché.

Para desaprovisionar la máquina virtual, use el agente de máquina virtual de Azure (waagent). El agente de máquina virtual de Azure está instalado en la máquina virtual y administra el aprovisionamiento y la interacción con el controlador de tejido de Azure. Consulte la [Guía de usuario del Agente de Linux de Azure](agent-user-guide.md) para más información.

Conéctese a la máquina virtual mediante SSH y ejecute el comando para desaprovisionar la máquina virtual. Con el argumento `+user` también se elimina la última cuenta de usuario aprovisionada y los datos asociados. Reemplace la dirección IP de ejemplo con la dirección IP pública de la máquina virtual.

SSH a la máquina virtual
```bash
ssh azureuser@52.174.34.95
```
Desaprovisione la máquina virtual.

```bash
sudo waagent -deprovision+user -force
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

Por último, establezca el estado de la máquina virtual como generalizado con [az vm generalize](/cli//azure/vm#generalize) para que la plataforma Azure lo sepa. Solo se puede crear una imagen de una máquina virtual generalizada.
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

## <a name="create-the-image"></a>Crear la imagen

Ahora puede crear una imagen de la máquina virtual con [az image create](/cli//azure/image#create). En el ejemplo siguiente se crea una imagen denominada *myImage* a partir de la máquina virtual llamada *myVM*.
   
```azurecli
az image create \
    --resource-group myResourceGroupImages \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Creación de máquinas virtuales a partir de la imagen

Ahora que tiene una imagen, puede crear una o varias máquinas virtuales de la imagen mediante [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual denominada *myVMfromImage* a partir de la máquina virtual llamada *myImage*.

```azurecli
az vm create \
    --resource-group myResourceGroupImages \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido sobre la creación de imágenes de máquina virtual personalizadas. Avance al siguiente tutorial para obtener información sobre máquinas virtuales de alta disponibilidad.

[Creación de máquinas virtuales de alta disponibilidad](tutorial-availability-sets.md).


