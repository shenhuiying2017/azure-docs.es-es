---
title: "Conexión de un disco a una máquina virtual de Azure clásico | Microsoft Docs"
description: "Conecte un disco de datos a una máquina virtual de Windows creada con el modelo de implementación clásica e inicialícelo."
services: virtual-machines-windows, storage
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 26c58ae4c509cb768807875ecdf96e9a24d6a472
ms.openlocfilehash: 8393b8ce2b373e8ff33454a61c944a5f8f7a8168


---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Conecte un disco de datos a una máquina virtual de Windows creada con el modelo de implementación clásica
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Si quiere usar el nuevo portal, consulte [Cómo adjuntar un disco de datos a una máquina virtual Windows en Azure Portal](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Si necesita un disco de datos adicional, puede conectar un disco vacío o un disco existente con datos a una máquina virtual. En ambos casos, se trata de archivos .vhd que residen en una cuenta de almacenamiento de Azure. En el caso de un disco nuevo, una vez que lo conecta, también deberá inicializarlo para que esté listo para su uso por parte de una máquina virtual de Windows.

Para obtener más detalles acerca de los discos, consulte [Acerca de los discos y discos duros virtuales para máquinas virtuales](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>Initialize the disk
1. Conexión a una máquina virtual. Para obtener instrucciones, vea [Iniciar sesión en una máquina virtual con Windows Server][logon].
2. Después de iniciar sesión en la máquina virtual, abra el **Administrador del servidor**. En el panel izquierdo, seleccione **Servicios de archivos y almacenamiento**.
   
    ![Abrir Administrador de servidores](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. Expanda el menú y seleccione **Discos**.
4. En la sección **Discos** aparecen todos los discos. En la mayoría de los casos, habrá un disco 0, un disco 1 y un disco 2. El disco 0 es el disco del sistema operativo, el disco 1 es el disco temporal y el disco 2 es el disco de datos que acaba de conectar a la VM. El nuevo disco de datos mostrará la partición como **Desconocida**. Haga clic con el botón derecho en el disco y seleccione **Inicializar**.
5. Se le notificará que se borrarán todos los datos cuando se inicializa el disco. Haga clic en **Sí** para confirmar la advertencia e inicializar el disco. Una vez que se complete el proceso, la partición aparecerá como **GPT**. Vuelva a hacer clic con el botón derecho en el disco y seleccione **Nuevo volumen**.
6. Complete el asistente usando los valores predeterminados que se proporcionan. Cuando haya finalizado el asistente, la sección **Volúmenes** mostrará el nuevo volumen. El disco está ahora conectado y listo para almacenar los datos.
   
   ![Volumen inicializado correctamente](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [!NOTE]
> El tamaño de la VM determina el número de discos que le puede asociar. Para obtener más información, vea [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="additional-resources"></a>Recursos adicionales
[Desacoplamiento de un disco de una máquina virtual de Windows](virtual-machines-windows-classic-detach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[logon]: virtual-machines-windows-classic-connect-logon.md



<!--HONumber=Feb17_HO3-->


