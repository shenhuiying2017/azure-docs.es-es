---
title: "Conexión de un disco a una máquina virtual de Azure clásico | Microsoft Docs"
description: "Conecte un disco de datos a una máquina virtual de Windows creada con el modelo de implementación clásica e inicialícelo."
services: virtual-machines-windows, storage
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cynthn
ms.openlocfilehash: 8219ee8bbaa3d53f95e441c934a0524666593c7b
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Conecte un disco de datos a una máquina virtual de Windows creada con el modelo de implementación clásica

En este artículo se muestra cómo conectar discos nuevos y existentes creados con el modelo de implementación clásica a una máquina virtual Windows mediante Azure Portal.



También puede [adjuntar un disco de datos a una máquina virtual Linux en el Portal de Azure](../../linux/attach-disk-portal.md).

Antes de conectar un disco, revise estas sugerencias:

* El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Para usar Premium Storage, necesita una máquina virtual de la serie DS o GS. Puede utilizar discos de cuentas de almacenamiento premium y estándar con estas máquinas virtuales. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Premium Storage: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* Para un disco nuevo, no es necesario crearlo en primer lugar porque Azure lo crea cuando lo conecta.

También puede [conectar un disco de datos mediante Powershell](../../virtual-machines-windows-attach-disk-ps.md).

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="find-the-virtual-machine"></a>Búsqueda de la máquina virtual
1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. Seleccione la máquina virtual en el recurso que aparece en el panel.
3. En el panel izquierdo, en **Configuración**, haga clic en **Discos**.

    ![Abrir configuración de disco](./media/attach-disk/virtualmachinedisks.png)

Continúe siguiendo las instrucciones para conectar un [nuevo disco](#option-1-attach-a-new-disk) o un [disco existente](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Opción 1: adjunte e inicialice un disco nuevo

1. En la hoja **Discos**, haga clic en **Adjuntar nuevo**.
2. Revise la configuración predeterminada, actualice según sea necesario y, después, haga clic en **Aceptar**.

   ![Revisar configuración de disco](./media/attach-disk/attach-new.png)

3. Una vez que Azure crea el disco y lo adjunta a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en el apartado **Discos de datos**.

### <a name="initialize-a-new-data-disk"></a>Inicio de un nuevo disco de datos

1. Conexión a una máquina virtual. Para obtener instrucciones, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows Server e inicio de sesión en ella](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Después de iniciar sesión en la máquina virtual, abra el **Administrador del servidor**. En el panel izquierdo, seleccione **Servicios de archivos y almacenamiento**.

    ![Abrir Administrador de servidores](../media/attach-disk-portal/fileandstorageservices.png)

3. Seleccione **Discos**.
4. En la sección **Discos** aparecen todos los discos. A menudo, una máquina virtual tiene los discos 0, 1 y 2. El disco 0 es el del sistema operativo, el 1 es el disco temporal y el 2 es el disco de datos que acaba de conectar a la máquina virtual. El disco de datos muestra la partición como **Desconocida**.

 Haga clic con el botón derecho en el disco y seleccione **Inicializar**.

5. Se le notificará que se borrarán todos los datos cuando se inicializa el disco. Haga clic en **Sí** para confirmar la advertencia e inicializar el disco. Una vez que se complete el proceso, la partición aparecerá como **GPT**. Vuelva a hacer clic con el botón derecho en el disco y seleccione **Nuevo volumen**.

6. Complete el asistente usando los valores predeterminados que se proporcionan. Cuando haya finalizado el asistente, la sección **Volúmenes** mostrará el nuevo volumen. El disco está ahora conectado y listo para almacenar los datos.

    ![Volumen inicializado correctamente](./media/attach-disk/newdiskafterinitialization.png)

## <a name="option-2-attach-an-existing-disk"></a>Opción 2: Conectar un disco existente
1. En la hoja **Discos**, haga clic en **Adjuntar existente**.
2. En **Adjuntar un disco existente**, haga clic en **Ubicación**.

   ![Conectar disco existente](./media/attach-disk/attachexistingdisksettings.png)
3. En **Cuentas de almacenamiento**, seleccione la cuenta y el contenedor que incluyen el archivo .vhd.

   ![Buscar ubicación de VHD](./media/attach-disk/existdiskstorageaccountandcontainer.png)

4. Seleccione el archivo .vhd.
5. En **Adjuntar un disco existente**, el archivo que acaba de seleccionar aparece en **Archivo VHD**. Haga clic en **OK**.
6. Una vez que Azure adjunta el disco a la máquina virtual,este aparece en la configuración del disco de la máquina virtual en el apartado **Discos de datos**.

## <a name="use-trim-with-standard-storage"></a>Uso de TRIM con el almacenamiento estándar

Si utiliza almacenamiento estándar (HDD), debe habilitar TRIM. TRIM descarta los bloques sin utilizar del disco, por lo que solo se le facturará el almacenamiento que utiliza realmente. Puede ahorrar costos si usa TRIM, además de los bloques sin utilizar por eliminar archivos de gran tamaño.

Puede ejecutar este comando para comprobar la configuración de TRIM. Abra un símbolo del sistema en su máquina virtual de Windows y escriba:

```
fsutil behavior query DisableDeleteNotify
```

Si el comando devuelve 0, significa que TRIM está habilitada correctamente. Si devuelve 1, ejecute el siguiente comando para habilitar TRIM:
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>pasos siguientes
Si la aplicación debe usar la unidad D: para almacenar datos, puede [cambiar la letra de la unidad del disco temporal de Windows](../../virtual-machines-windows-change-drive-letter.md).

## <a name="additional-resources"></a>Recursos adicionales
[Acerca de los discos y los discos duros virtuales para máquinas virtuales](../../virtual-machines-linux-about-disks-vhds.md)
