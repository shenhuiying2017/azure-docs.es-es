---
title: "Incorporación de un disco de datos a una máquina virtual con Linux | Microsoft Docs"
description: "Use el portal para conectar un disco de datos nuevo o existente a una máquina virtual Linux."
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
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 787f729732accd74c212b3be9520af50a2f04261
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Uso del portal para conectar un disco de datos a una máquina virtual Linux 
En este artículo se muestra cómo adjuntar discos nuevos y existentes a una máquina virtual Linux a través del Portal de Azure. También puede [adjuntar un disco de datos a una máquina virtual con Windows en Azure Portal](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Antes de conectar discos a la VM, revise estas sugerencias:

* El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para usar Premium Storage, necesita una máquina virtual de la serie DS o GS. Puede usar discos Premium y Estándar con estas máquinas virtuales. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../../storage/common/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Los discos conectados a las máquinas virtuales son, en realidad, archivos .vhd almacenados en Azure. Para obtener más información, vea [Acerca de los discos y los discos duros virtuales para máquinas virtuales](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Búsqueda de la máquina virtual
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú de la izquierda, haga clic en **Máquinas virtuales**.
3. Seleccione la máquina virtual en la lista.
4. En la página Máquinas virtuales, en **Essentials**, haga clic en **Discos**.
   
    ![Abrir configuración de disco](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Conexión de un disco nuevo

1. En el panel **Discos**, haga clic en **+ Add data disk** (+ Agregar disco de datos).
2. Haga clic en el menú desplegable para el **nombre** y seleccione **Crear disco**:

    ![Creación de disco administrado de Azure](./media/attach-disk-portal/create-new-md.png)

3. Escriba un nombre para el disco administrado. Revise la configuración predeterminada, actualice según sea necesario y, luego, haga clic en **Crear**.
   
   ![Revisar configuración de disco](./media/attach-disk-portal/create-new-md-settings.png)

4. Haga clic en **Guardar** para crear el disco administrado y actualizar la configuración de VM:

   ![Guardado del nuevo Azure Managed Disk](./media/attach-disk-portal/confirm-create-new-md.png)

5. Una vez que Azure crea el disco y lo adjunta a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en el apartado **Discos de datos**. Como los discos administrados son un recurso de nivel superior, el disco aparece en la raíz del grupo de recursos:

   ![Azure Managed Disk en grupo de recursos](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>un disco existente
1. En el panel **Discos**, haga clic en **+ Add data disk** (+ Agregar disco de datos).
2. Haga clic en el menú desplegable de **nombre** para ver una lista de los discos administrados existentes a los que puede obtener acceso con la suscripción de Azure. Seleccione el disco administrado que va a conectar:

   ![Conexión de un disco administrado de Azure existente](./media/attach-disk-portal/select-existing-md.png)

3. Haga clic en **Guardar** para conectar el disco administrado existente y actualizar la configuración de la VM:
   
   ![Guardado de las actualizaciones del disco administrado de Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Una vez que Azure adjunta el disco a la máquina virtual,este aparece en la configuración del disco de la máquina virtual en el apartado **Discos de datos**.



## <a name="next-steps"></a>Pasos siguientes
También puede [conectar un disco de datos](add-disk.md) mediante la CLI de Azure.
