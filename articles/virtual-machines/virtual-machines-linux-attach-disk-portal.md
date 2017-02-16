---
title: "Incorporación de un disco de datos a una máquina virtual con Linux | Microsoft Docs"
description: "Cómo adjuntar un disco de datos nuevo o existente a una máquina virtual Linux en el Portal de Azure con el modelo de implementación de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 7c481cfae5f97b71c0ab184419ceaa46ab3f5a5b
ms.openlocfilehash: 394d82e444bdbc8b07243d92743ceb660f142509


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Incorporación de un disco de datos a una máquina virtual Linux en el Portal de Azure
En este artículo se muestra cómo adjuntar discos nuevos y existentes a una máquina virtual Linux a través del Portal de Azure. También puede [adjuntar un disco de datos a una máquina virtual con Windows en Azure Portal](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Antes de hacerlo, revise estas sugerencias:

* El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para usar Almacenamiento premium, necesitará una máquina virtual de serie DS o GS. Puede utilizar discos de cuentas de almacenamiento premium y estándar con estas máquinas virtuales. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Los discos conectados a las máquinas virtuales son en realidad archivos .vhd en una cuenta de almacenamiento de Azure. Para obtener más información, vea [Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para un disco nuevo, no es necesario crearlo en primer lugar porque Azure lo crea cuando lo conecta.
* Para un disco existente, el archivo .vhd debe estar disponible en una cuenta de almacenamiento de Azure. Puede usar uno que ya esté disponible allí, si no está conectado a otra máquina virtual, o cargar su propio archivo .vhd en la cuenta de almacenamiento.


## <a name="find-the-virtual-machine"></a>Búsqueda de la máquina virtual
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú del concentrador, haga clic en **Máquinas virtuales**.
3. Seleccione la máquina virtual en la lista.
4. En la hoja Virtual Machines, en **Essentials**, haga clic en **Discos**.
   
    ![Abrir configuración de disco](./media/virtual-machines-linux-attach-disk-portal/find-disk-settings.png)

Continúe siguiendo las instrucciones para conectar un [nuevo disco](#option-1-attach-a-new-disk) o un [disco existente](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-a-new-disk"></a>Opción 1: Conectar un nuevo disco
1. En la hoja **Discos**, haga clic en **Adjuntar nuevo**.
2. Revise la configuración predeterminada, actualice según sea necesario y, después, haga clic en **Aceptar**.
   
   ![Revisar configuración de disco](./media/virtual-machines-linux-attach-disk-portal/attach-new.png)
3. Una vez que Azure crea el disco y lo adjunta a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en el apartado **Discos de datos**.

## <a name="option-2-attach-an-existing-disk"></a>Opción 2: Conectar un disco existente
1. En la hoja **Discos**, haga clic en **Adjuntar existente**.
2. En **Adjuntar un disco existente**, haga clic en **Archivo VHD**.
   
   ![Conectar disco existente](./media/virtual-machines-linux-attach-disk-portal/attach-existing.png)
3. En **Cuentas de almacenamiento**, seleccione la cuenta y el contenedor que incluyen el archivo .vhd.
   
   ![Buscar ubicación de VHD](./media/virtual-machines-linux-attach-disk-portal/find-storage-container.png)
4. Seleccione el archivo .vhd.
5. En **Adjuntar un disco existente**, el archivo que acaba de seleccionar aparece en **Archivo VHD**. Haga clic en **Aceptar**.
6. Una vez que Azure adjunta el disco a la máquina virtual,este aparece en la configuración del disco de la máquina virtual en el apartado **Discos de datos**.



## <a name="next-steps"></a>Pasos siguientes
Después de agregar el disco, debe prepararlo para poder usarlo. Para obtener más información, consulte [Inicio de un nuevo disco de datos](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux).



<!--HONumber=Nov16_HO5-->


