---
title: "Cambio de tamaño de una máquina virtual Linux | Microsoft Docs"
description: "Escalado o reducción en vertical de una máquina virtual de Linux cambiando el tamaño de la VM."
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: 788efb5d1cbbd5fd20096c54ca702b99eb2b5a18


---
# <a name="how-to-resize-a-linux-vm"></a>Cambio de tamaño de una máquina virtual de Linux
## <a name="overview"></a>Información general
Después de aprovisionar una máquina virtual (VM), puede escalarla o reducirla verticalmente cambiando su [tamaño][vm-sizes]. En algunos casos, hay que desasignarla antes. Esto puede suceder si el nuevo tamaño no está disponible en el clúster de hardware que hospeda la VM.

Este artículo muestra cómo cambiar el tamaño de una VM de Linux mediante la [CLI de Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="resize-a-linux-vm"></a>Cambio de tamaño de una VM de Linux
Para cambiar el tamaño de una máquina virtual, realice los pasos siguientes.

1. Ejecute el siguiente comando de la CLI: Este comando muestra los tamaños de máquina virtual que están disponibles en el clúster de hardware donde se hospeda la VM.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. Si se muestra el tamaño deseado, ejecute el comando siguiente para cambiar el tamaño de la VM.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    La máquina virtual se reiniciará durante este proceso. Tras reiniciar, se reasignarán los discos del SO y de datos. Se perderá todo lo que haya en el disco temporal.
   
    Al usar la opción `--enable-boot-diagnostics`, se habilita el [diagnóstico de arranque][boot-diagnostics], que permite registrar cualquier error relacionado con el inicio.
3. Si, por el contrario, no aparece el tamaño deseado, ejecute los siguientes comandos para desasignar la máquina virtual, cambiar su tamaño y reiniciarla.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > Al desasignar la máquina virtual, también se liberan todas las direcciones IP dinámicas asignadas a ella. Esto no afecta a los discos del SO y de datos.
   > 
   > 

## <a name="next-steps"></a>Pasos siguientes
Para obtener una mayor escalabilidad, ejecute varias instancias de VM y escálelas horizontalmente. Para obtener más información, consulte [Escalado automático de máquinas de Linux en un conjunto de escalado de máquinas virtuales][scale-set]. 

<!-- links -->

[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md



<!--HONumber=Dec16_HO2-->


