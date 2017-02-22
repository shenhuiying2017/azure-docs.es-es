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
ms.sourcegitcommit: de058c58764fc37a02df86ebd3a865d425e4b0a2
ms.openlocfilehash: 3e2c73c3d4ddad3641dacf9caebf9b8f57c1139c


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Incorporación de un disco de datos a una máquina virtual Linux en el Portal de Azure
En este artículo se muestra cómo adjuntar discos nuevos y existentes a una máquina virtual Linux a través del Portal de Azure. También puede [adjuntar un disco de datos a una máquina virtual con Windows en Azure Portal](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Puede elegir usar Azure Managed Disks o discos no administrados. Los discos administrados se controlan mediante la plataforma de Azure y no requieren preparativos ni ubicación para el almacenamiento. Los discos no administrados requieren una cuenta de almacenamiento y tienen algunas [cuotas y límites que se deben aplicar](../azure-subscription-service-limits.md#storage-limits). Para más información sobre Azure Managed Disks, consulte [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Introducción a Azure Managed Disks).

Antes de conectar discos a la VM, revise estas sugerencias:

* El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para usar Premium Storage, necesita una máquina virtual de la serie DS o GS. Puede usar discos Premium y Estándar con estas máquinas virtuales. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Los discos conectados a las máquinas virtuales son, en realidad, archivos .vhd almacenados en Azure. Para obtener más información, vea [Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Búsqueda de la máquina virtual
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú del concentrador, haga clic en **Máquinas virtuales**.
3. Seleccione la máquina virtual en la lista.
4. En la hoja Virtual Machines, en **Essentials**, haga clic en **Discos**.
   
    ![Abrir configuración de disco](./media/virtual-machines-linux-attach-disk-portal/find-disk-settings.png)

Para continuar, siga las instrucciones y conecte un [disco administrado](#use-azure-managed-disks) o un [disco no administrado](#use-unmanaged-disks).

## <a name="use-azure-managed-disks"></a>Uso de Azure Managed Disks

### <a name="attach-a-new-disk"></a>Conexión de un disco nuevo

1. En la hoja **Discos**, haga clic en **+ Add data disk** (+ Agregar disco de datos).
2. Haga clic en el menú desplegable para el **nombre** y seleccione **Crear disco**:

    ![Creación de disco administrado de Azure](./media/virtual-machines-linux-attach-disk-portal/create-new-md.png)

3. Escriba un nombre para el disco administrado. Revise la configuración predeterminada, actualice según sea necesario y, luego, haga clic en **Crear**.
   
   ![Revisar configuración de disco](./media/virtual-machines-linux-attach-disk-portal/create-new-md-settings.png)

4. Haga clic en **Guardar** para crear el disco administrado y actualizar la configuración de VM:

   ![Guardado del nuevo Azure Managed Disk](./media/virtual-machines-linux-attach-disk-portal/confirm-create-new-md.png)

5. Una vez que Azure crea el disco y lo adjunta a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en el apartado **Discos de datos**. Como los discos administrados son un recurso de nivel superior, el disco aparece en la raíz del grupo de recursos:

   ![Azure Managed Disk en grupo de recursos](./media/virtual-machines-linux-attach-disk-portal/view-md-resource-group.png)

### <a name="attach-an-existing-disk"></a>un disco existente
1. En la hoja **Discos**, haga clic en **+ Add data disk** (+ Agregar disco de datos).
2. Haga clic en el menú desplegable de **nombre** para ver una lista de los discos administrados existentes a los que puede obtener acceso con la suscripción de Azure. Seleccione el disco administrado que va a conectar:

   ![Conexión de un disco administrado de Azure existente](./media/virtual-machines-linux-attach-disk-portal/select-existing-md.png)

3. Haga clic en **Guardar** para conectar el disco administrado existente y actualizar la configuración de la VM:
   
   ![Guardado de las actualizaciones del disco administrado de Azure](./media/virtual-machines-linux-attach-disk-portal/confirm-attach-existing-md.png)

4. Una vez que Azure adjunta el disco a la máquina virtual,este aparece en la configuración del disco de la máquina virtual en el apartado **Discos de datos**.

## <a name="use-unmanaged-disks"></a>Uso de discos no administrados

### <a name="attach-a-new-disk"></a>Conexión de un disco nuevo

1. En la hoja **Discos**, haga clic en **+ Add data disk** (+ Agregar disco de datos).
2. Revise la configuración predeterminada, actualice según sea necesario y, después, haga clic en **Aceptar**.
   
   ![Revisar configuración de disco](./media/virtual-machines-linux-attach-disk-portal/attach-new.png)
3. Una vez que Azure crea el disco y lo adjunta a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en el apartado **Discos de datos**.

### <a name="attach-an-existing-disk"></a>un disco existente
1. En la hoja **Discos**, haga clic en **+ Add data disk** (+ Agregar disco de datos).
2. En **Adjuntar un disco existente**, haga clic en **Archivo VHD**.
   
   ![Conectar disco existente](./media/virtual-machines-linux-attach-disk-portal/attach-existing.png)
3. En **Cuentas de almacenamiento**, seleccione la cuenta y el contenedor que incluyen el archivo .vhd.
   
   ![Buscar ubicación de VHD](./media/virtual-machines-linux-attach-disk-portal/find-storage-container.png)
4. Seleccione el archivo .vhd.
5. En **Adjuntar un disco existente**, el archivo que acaba de seleccionar aparece en **Archivo VHD**. Haga clic en **Aceptar**.
6. Una vez que Azure adjunta el disco a la máquina virtual,este aparece en la configuración del disco de la máquina virtual en el apartado **Discos de datos**.


## <a name="next-steps"></a>Pasos siguientes
Después de agregar el disco, debe prepararlo para poder usarlo. Para obtener más información, consulte [Inicio de un nuevo disco de datos](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux).



<!--HONumber=Feb17_HO2-->


