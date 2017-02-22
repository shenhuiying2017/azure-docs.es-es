---
title: "Conexión de un disco de datos a una máquina virtual Windows | Microsoft Docs"
description: "Cómo conectar un disco de datos nuevo o existente a una máquina virtual Windows en el Portal de Azure con el modelo de implementación del Administrador de recursos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: b84e07b26506149cf9475491b32b9ff3ea9ae80d
ms.openlocfilehash: 40375aa411920f966aa6923f0899ca2f88a9ed39


---
# <a name="how-to-attach-a-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Cómo adjuntar un disco de datos a una máquina virtual Windows en el Portal de Azure
En este artículo se muestra cómo adjuntar discos nuevos y existentes a una máquina virtual Windows a través del Portal de Azure. También puede [adjuntar un disco de datos a una máquina virtual Linux en el Portal de Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Antes de hacerlo, revise estas sugerencias:

* El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para usar Almacenamiento premium, necesitará una máquina virtual de serie DS o GS. Puede utilizar discos de cuentas de almacenamiento premium y estándar con estas máquinas virtuales. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Para un disco nuevo, no es necesario crearlo en primer lugar porque Azure lo crea cuando lo conecta.
* Para un disco existente, el archivo .vhd debe estar disponible en una cuenta de almacenamiento de Azure. Puede usar uno que ya esté disponible allí, si no está conectado a otra máquina virtual, o cargar su propio archivo .vhd en la cuenta de almacenamiento.

También puede [conectar un disco de datos mediante Powershell](virtual-machines-windows-attach-disk-ps.md).


## <a name="find-the-virtual-machine"></a>Búsqueda de la máquina virtual
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú del concentrador, haga clic en **Máquinas virtuales**.
3. Seleccione la máquina virtual en la lista.
4. En la hoja Virtual Machines, en **Essentials**, haga clic en **Discos**.
   
    ![Abrir configuración de disco](./media/virtual-machines-windows-attach-disk-portal/find-disk-settings.png)

Continúe siguiendo las instrucciones para conectar un [nuevo disco](#option-1-attach-a-new-disk) o un [disco existente](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Opción 1: adjunte e inicialice un disco nuevo
1. En la hoja **Discos**, haga clic en **Adjuntar nuevo**.
2. Revise la configuración predeterminada, actualice según sea necesario y, después, haga clic en **Aceptar**.
   
   ![Revisar configuración de disco](./media/virtual-machines-windows-attach-disk-portal/attach-new.png)
3. Una vez que Azure crea el disco y lo adjunta a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en el apartado **Discos de datos**.

### <a name="initialize-a-new-data-disk"></a>Inicio de un nuevo disco de datos

1. Conexión a una máquina virtual. Para obtener instrucciones, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows Server e inicio de sesión en ella](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Después de iniciar sesión en la máquina virtual, abra el **Administrador del servidor**. En el panel izquierdo, seleccione **Servicios de archivos y almacenamiento**.
   
    ![Abrir Administrador de servidores](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. Expanda el menú y seleccione **Discos**.
4. En la sección **Discos** aparecen todos los discos. En la mayoría de los casos, habrá un disco 0, un disco 1 y un disco 2. El disco 0 es el disco del sistema operativo, el disco 1 es el disco temporal y el disco 2 es el disco de datos que acaba de conectar a la VM. El nuevo disco de datos mostrará la partición como **Desconocida**. Haga clic con el botón derecho en el disco y seleccione **Inicializar**.
5. Se le notificará que se borrarán todos los datos cuando se inicializa el disco. Haga clic en **Sí** para confirmar la advertencia e inicializar el disco. Una vez que se complete el proceso, la partición aparecerá como **GPT**. Vuelva a hacer clic con el botón derecho en el disco y seleccione **Nuevo volumen**.
6. Complete el asistente usando los valores predeterminados que se proporcionan. Cuando haya finalizado el asistente, la sección **Volúmenes** mostrará el nuevo volumen. El disco está ahora conectado y listo para almacenar los datos.

    ![Volumen inicializado correctamente](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)


## <a name="option-2-attach-an-existing-disk"></a>Opción 2: Conectar un disco existente
1. En la hoja **Discos**, haga clic en **Adjuntar existente**.
2. En **Adjuntar un disco existente**, haga clic en **Archivo VHD**.
   
   ![Conectar disco existente](./media/virtual-machines-windows-attach-disk-portal/attach-existing.png)
3. En **Cuentas de almacenamiento**, seleccione la cuenta y el contenedor que incluyen el archivo .vhd.
   
   ![Buscar ubicación de VHD](./media/virtual-machines-windows-attach-disk-portal/find-storage-container.png)
4. Seleccione el archivo .vhd.
5. En **Adjuntar un disco existente**, el archivo que acaba de seleccionar aparece en **Archivo VHD**. Haga clic en **Aceptar**.
6. Una vez que Azure adjunta el disco a la máquina virtual,este aparece en la configuración del disco de la máquina virtual en el apartado **Discos de datos**.



## <a name="use-trim-with-standard-storage"></a>Uso de TRIM con el almacenamiento estándar

Si utiliza almacenamiento estándar (HDD), debe habilitar TRIM. TRIM descarta los bloques sin utilizar del disco, por lo que solo se le facturará el almacenamiento que utiliza realmente. Esto puede suponerle un ahorro si crea archivos grandes y, luego, los elimina. 

Puede ejecutar este comando para comprobar la configuración de TRIM. Abra un símbolo del sistema en su máquina virtual de Windows y escriba:

```
fsutil behavior query DisableDeleteNotify
```

Si el comando devuelve 0, significa que TRIM está habilitada correctamente. Si devuelve 1, ejecute el siguiente comando para habilitar TRIM:
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>Pasos siguientes
Si la aplicación debe usar la unidad D: para almacenar datos, puede [cambiar la letra de la unidad del disco temporal de Windows](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).




<!--HONumber=Feb17_HO2-->


