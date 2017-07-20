---
title: "Conexión de un disco de datos no administrado a una VM con Windows: Azure | Microsoft Docs"
description: "Se muestra cómo conectar un disco de datos no administrado nuevo o existente a una máquina virtual con Windows en Azure Portal con el modelo de implementación de Resource Manager."
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
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: c0886302c82641f8cc1a00d3972870d58ba8afb4
ms.contentlocale: es-es
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-attach-an-unmanaged-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Conexión de un disco de datos no administrado a una VM con Windows en Azure Portal

En este artículo se muestra cómo conectar discos no administrados nuevos y existentes a una máquina virtual con Windows a través Azure Portal. También puede [conectar un disco de datos mediante PowerShell](./attach-disk-ps.md). Antes de hacerlo, revise estas sugerencias:

* El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](sizes.md).
* Para usar Premium Storage, necesita una máquina virtual de la serie DS o GS. Puede utilizar discos de cuentas de almacenamiento premium y estándar con estas máquinas virtuales. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Para un disco nuevo, no es necesario crearlo en primer lugar porque Azure lo crea cuando lo conecta.


También puede [conectar un disco de datos mediante Powershell](attach-disk-ps.md).


## <a name="find-the-virtual-machine"></a>Búsqueda de la máquina virtual
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú izquierdo, haga clic en **Virtual Machines**.
3. Seleccione la máquina virtual en la lista.
4. En la hoja Virtual Machines, haga clic en **Discos**.
   
Continúe siguiendo las instrucciones para conectar un [nuevo disco](#option-1-attach-a-new-disk) o un [disco existente](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Opción 1: adjunte e inicialice un disco nuevo
1. En la hoja **Discos**, haga clic en **+ Add data disk** (+ Agregar disco de datos).
2. En la hoja **Attach managed disk** (Conectar disco administrado), escriba un nombre para el disco en **Nombre** y luego seleccione **Nuevo (disco vacío)** en **Tipo de origen**.
3. En **Contenedor de almacenamiento**, haga clic en el botón **Examinar** y navegue a la cuenta y el contenedor de almacenamiento en donde quiere almacenar el nuevo VHD. Luego, haga clic en **Seleccionar**. 
  
   ![Revisar configuración de disco](./media/attach-disk-portal/attach-empty-unmanaged.png)
   
3. Cuando haya terminado la configuración del disco de datos, haga clic en **Aceptar**.
4. De nuevo en la hoja **Discos**, haga clic en **Guardar** para agregar el disco a la configuración de la máquina virtual.


### <a name="initialize-a-new-data-disk"></a>Inicio de un nuevo disco de datos

1. Conexión a una máquina virtual. Para obtener instrucciones, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows e inicio de sesión en ella](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
1. Haga clic en el menú **Inicio** dentro de la máquina virtual, escriba **diskmgmt.msc** y presione **Entrar**. Se iniciará el complemento Administración de discos.
2. Administración de discos reconocerá que hay un nuevo disco sin inicializar y se abrirá la ventana Inicializar disco.
3. Asegúrese de que el nuevo disco está seleccionado y haga clic en **Aceptar** para inicializarlo.
4. El nuevo disco aparecerá ahora como **sin asignar**. Haga clic con el botón derecho en cualquier lugar del disco y seleccione **Nuevo volumen simple**. Se inicia el **Asistente para nuevo volumen simple**.
5. Navegue por el asistente, conserve todos los valores predeterminados y, cuando haya terminado, seleccione **Finalizar**.
6. Cierre Administración de discos.
7. Se abrirá una ventana emergente necesaria para formatear el nuevo disco para poder usarlo. Haga clic en **Dar formato al disco**.
8. En el cuadro de diálogo **Formatear disco nuevo**, active las opciones y haga clic en **Iniciar**.
9. Recibirá una advertencia sobre que al formatear los discos se borrarán todos los datos, haga clic en **Aceptar**.
10. Una vez completado el formato, haga clic en **Aceptar**.


## <a name="option-2-attach-an-existing-disk"></a>Opción 2: Conectar un disco existente
1. En la hoja **Discos**, haga clic en **+ Add data disk** (+ Agregar disco de datos).
2. En la hoja **Asociar disco no administrado**, seleccione **Blob existente** en **Tipo de origen**.

    ![Revisar configuración de disco](./media/attach-disk-portal/attach-existing-unmanaged.png)

    3. Haga clic en **Examinar** para navegar a la cuenta y el contenedor de almacenamiento donde se encuentra el VHD existente. Haga clic en VHD y luego, en **Seleccionar**.
4. En la hoja **Asociar disco no administrado**, haga clic en **Aceptar**.
5. En la hoja **Discos**, haga clic en **Guardar** para agregar el disco a la configuración de la máquina virtual.
   


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

Después de eliminar los datos del disco, puede garantizar que las operaciones de TRIM se vacían correctamente mediante la ejecución de desfragmentación con TRIM:

```
defrag.exe <volume:> -l
```

También puede formatear el volumen para asegurarse de que quede recortado por completo.


## <a name="next-steps"></a>Pasos siguientes
Si la aplicación debe usar la unidad D: para almacenar datos, puede [cambiar la letra de la unidad del disco temporal de Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


