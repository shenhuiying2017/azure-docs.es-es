---
title: "Solución de problemas en los discos duros virtuales conectados en máquinas virtuales Linux de Azure | Microsoft Docs"
description: "Solución de problemas, como los reinicios inesperados de las máquinas virtuales Linux o problemas al eliminar una cuenta de almacenamiento con discos duros virtuales conectados."
keywords: "conexión ssh rechazada, error de ssh azure ssh, error de conexión ssh"
services: virtual-machines-linux
author: iainfoulds
manager: jeconnoc
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/28/2018
ms.author: iainfou
ms.openlocfilehash: 10f5e2f4a48d335c6b7d05b5eb5b577765675c0e
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshoot-attached-vhds-on-azure-linux-virtual-machines"></a>Solución de problemas en los discos duros virtuales conectados en máquinas virtuales Linux de Azure

Azure Virtual Machines usa discos duros virtuales (VHD) para el disco del sistema operativo y cualquier disco de datos conectado. Los discos duros virtuales se almacenan como blobs en páginas en una o varias cuentas de Azure Storage. En este artículo se describe cómo solucionar los problemas comunes que pueden surgir con los discos duros virtuales. 

  * [Reinicios inesperados de las máquinas virtuales con discos duros virtuales conectados]
  * [Errores de eliminación de almacenamiento en la implementación de Resource Manager]

## <a name="you-are-experiencing-unexpected-reboots"></a>Reinicios inesperados de las máquinas virtuales con discos duros virtuales conectados

Si una máquina virtual de Azure (VM) tiene una gran cantidad de VHD adjuntos que se encuentran en la misma cuenta de almacenamiento, es posible que supere los objetivos de escalabilidad de una cuenta de almacenamiento individual, lo que provocaría un error en la VM. Compruebe las métricas por minuto de la cuenta de almacenamiento (**TotalRequests**/**TotalIngress**/**TotalEgress**) para ver si hay picos que superen los objetivos de escalabilidad de una cuenta de almacenamiento. Consulte la sección "[Las métricas muestran un aumento de PercentThrottlingError]" para recibir asistencia a la hora de averiguar si se produjo alguna limitación en la cuenta de almacenamiento.

En general, cada operación individual de entrada o salida de un VHD desde una máquina virtual se traduce en operaciones **Get Page** o **Put Page** en el blob en páginas subyacente. Por lo tanto, puede usar el IOPS estimado para su entorno con el fin de ajustar la cantidad de VHD que puede tener en una sola cuenta de almacenamiento, en función del comportamiento específico de la aplicación. Microsoft recomienda tener 40 o menos discos en una cuenta de almacenamiento única. Consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](../../storage/common/storage-scalability-targets.md) para detalles de los objetivos de escalabilidad para las cuentas de almacenamiento, en particular, el ancho de banda total y la velocidad de solicitudes total para el tipo de cuenta de almacenamiento que usa.

Si supera los objetivos de escalabilidad para la cuenta de almacenamiento, coloque los VHD en varias cuentas de almacenamiento para reducir la actividad de cada cuenta individual.

## <a name="storage-delete-errors-in-rm"></a>Errores de eliminación de almacenamiento en la implementación de Resource Manager

En esta sección se proporcionan instrucciones para solucionar problemas cuando se produce uno de los errores siguientes al intentar eliminar una cuenta de almacenamiento, un contenedor o un blob de Azure en una implementación de Azure Resource Manager.

>**No se pudo eliminar la cuenta de almacenamiento 'nombreCuentaDeAlmacenamiento'. Error: La cuenta de almacenamiento no se puede eliminar porque sus artefactos están en uso.**

>**No se pudo eliminar # de # contenedores:<br>vhds: Actualmente hay una concesión en el contenedor y no se especificó ningún identificador de concesión en la solicitud.**

>**No se pudo eliminar # de # blobs:<br>BlobName.vhd: Actualmente hay una concesión en el blob y no se especificó ningún identificador de concesión en la solicitud.**

Los discos duros virtuales usados en las máquinas virtuales de Azure son archivos .vhd almacenados como blobs en páginas en una cuenta de almacenamiento estándar o premium de Azure.  Se puede encontrar más información sobre los discos de Azure [aquí](../../virtual-machines/windows/about-disks-and-vhds.md). Azure impide la eliminación de un disco que esté asociado a una máquina virtual para evitar daños. También impide la eliminación de contenedores y cuentas de almacenamiento que tengan un blob en páginas que esté asociado a una máquina virtual. 

El proceso para eliminar una cuenta de almacenamiento, un contenedor o un blob cuando se recibe uno de estos errores es el siguiente: 
1. [Identificar los blobs asociados a una máquina virtual](#step-1-identify-blobs-attached-to-a-vm)
2. [Eliminar las máquinas virtuales con el **disco del sistema operativo**](#step-2-delete-vm-to-detach-os-disk) asociado
3. [Desasociar todos los **discos de datos** del resto de máquinas virtuales](#step-3-detach-data-disk-from-the-vm)

Intente eliminar de nuevo la cuenta de almacenamiento, el contenedor o el blob una vez completados estos pasos.

### <a name="step-1-identify-blob-attached-to-a-vm"></a>Paso 1: Identificación del blob asociado a una máquina virtual

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Escenario 1: Eliminación de un blob: identificación de la máquina virtual asociada
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú Concentrador, seleccione **Todos los recursos**. Vaya a la cuenta de almacenamiento, en **Blob service** seleccione **Contenedores** y navegue hasta el blob que se va a eliminar.
3. Si el **estado de concesión** del blob es **Concedido**, haga clic con el botón derecho y seleccione **Editar metadatos** para abrir el panel de metadatos del blob. 

    ![Captura de pantalla del portal, con los blobs de la cuenta de almacenamiento y clic con el botón derecho en > "Editar metadatos" resaltado](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. En el panel de metadatos de blob, compruebe y registre el valor de **MicrosoftAzureCompute_VMName**. Este valor es el nombre de la máquina virtual a la que está asociada. (Consulte **Importante** si este campo no existe).
5. En el panel de metadatos de blob, compruebe y registre el valor de **MicrosoftAzureCompute_DiskType**. Este valor identifica si el disco asociado es un disco de datos o del sistema operativo (consulte **Importante** si este campo no existe). 

     ![Captura de pantalla del portal, con el panel "Metadatos de blob" de almacenamiento abierto](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Si el tipo de disco de blob es **OSDisk**, siga el [Paso 2: Eliminación de una máquina virtual para desasociar el disco del sistema operativo](#step-2-delete-vm-to-detach-os-disk). En caso contrario, si el tipo de disco de blob es **DataDisk**, siga los pasos del [Paso 3: Desasociación del disco de datos de la máquina virtual](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Si **MicrosoftAzureCompute_VMName** y **MicrosoftAzureCompute_DiskType** no aparecen en los metadatos de blob, indica que el blob se ha concedido explícitamente y no está asociado a una máquina virtual. Los blobs concedidos no se pueden eliminar sin romper primero la concesión. Para interrumpir la concesión, haga clic con el botón derecho en el blob y seleccione **Interrumpir concesión**. Los blobs concedidos que no están asociados a una máquina virtual impiden la eliminación del blob pero no impiden la eliminación del contenedor o de la cuenta de almacenamiento.

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Escenario 2: Eliminación de un contenedor: identificación de todos los blobs de un contenedor que están asociados a máquinas virtuales
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú Concentrador, seleccione **Todos los recursos**. Vaya a la cuenta de almacenamiento, en **Blob service** seleccione **Contenedores** y busque el contenedor que se va a eliminar.
3. Haga clic para abrir el contenedor y se mostrará la lista de blobs que contiene. Identifique todos los blobs con Tipo de blob = **Blob en páginas** y Estado de concesión = **Concedido** de esta lista. Siga el [Escenario 1](#step-1-identify-blobs-attached-to-a-vm) para identificar la máquina virtual asociada a cada uno de estos blobs.

    ![Captura de pantalla del portal, con los blobs de la cuenta de almacenamiento y "Estado de concesión" con "Concedido" resaltado](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Siga el [paso 2](#step-2-delete-vm-to-detach-os-disk) y el [paso 3](#step-3-detach-data-disk-from-the-vm) para eliminar máquinas virtuales con **OSDisk** y desasociar **DataDisk**. 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Escenario 3: Eliminación de la cuenta de almacenamiento: identificación de todos los blobs de una cuenta de almacenamiento que están asociados a máquinas virtuales
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú Concentrador, seleccione **Todos los recursos**. Vaya a la cuenta de almacenamiento y, en **Blob service** seleccione **Contenedores**.

    ![Captura de pantalla del portal, con los contenedores de la cuenta de almacenamiento y "Estado de concesión" con "Concedido" resaltado](./media/troubleshoot-vhds/utd-containers-sm.png)

3. En el panel **Contenedores**, identifique todos los contenedores donde **Estado de concesión** sea **Concedido** y siga el [escenario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) con cada contenedor **Concedido**.
4. Siga el [paso 2](#step-2-delete-vm-to-detach-os-disk) y el [paso 3](#step-3-detach-data-disk-from-the-vm) para eliminar máquinas virtuales con **OSDisk** y desasociar **DataDisk**. 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>Paso 2: Eliminación de la máquina virtual para desasociar el disco del sistema operativo
Si el disco duro virtual es un disco del sistema operativo, debe eliminar la máquina virtual antes de eliminar el disco duro virtual asociado. No será necesaria ninguna acción adicional en los discos asociados a la misma máquina virtual una vez finalizados estos pasos:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú del concentrador, haga clic en **Máquinas virtuales**.
3. Seleccione la máquina virtual a la que está conectado el disco duro virtual.
4. Asegúrese de que no haya nada usando activamente la máquina virtual y de que ya no la necesite.
5. En la parte superior del panel **Detalles de máquina virtual**, seleccione **Eliminar** y, después, haga clic en **Sí** para confirmar.
6. La máquina virtual se debe eliminar, pero el disco duro virtual se puede conservar. Sin embargo, el disco duro virtual ya no debe asociarse a una máquina virtual o tener una concesión en ella. Es posible que la concesión tarde unos minutos en liberarse. Para comprobar que la concesión se libera, vaya a la ubicación del blob y, en el panel **Propiedades de blob**, la opción **Estado de concesión** debe ser **Disponible**.

### <a name="step-3-detach-data-disk-from-the-vm"></a>Paso 3: Desasociación del disco de datos de la máquina virtual
Si el disco duro virtual es un disco de datos, desasócielo de la máquina virtual para quitar la concesión:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú del concentrador, haga clic en **Máquinas virtuales**.
3. Seleccione la máquina virtual a la que está conectado el disco duro virtual.
4. Seleccione **Discos** en el panel **Detalles de máquina virtual**.
5. Seleccione el disco de datos del que se va a eliminar el disco duro virtual que tiene asociado. Para determinar qué blob está asociado al disco, compruebe la dirección URL del disco duro virtual.
6. Para comprobar la ubicación del blob, haga clic en el disco para comprobar la ruta de acceso que se muestra en el campo **URI de VHD**.
7. Seleccione **Editar** en la parte superior del panel **Discos**.
8. Haga clic en el **icono de desasociación** del disco de datos que se va a eliminar.

     ![Captura de pantalla del portal, con el panel "Metadatos de blob" de almacenamiento abierto](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Seleccione **Guardar**. Ahora el disco no está conectado a la máquina virtual y el VHD ya no está concedido. Es posible que la concesión tarde unos minutos en liberarse. Para comprobar que la concesión se ha liberado, vaya a la ubicación del blob y, en el panel **Propiedades de blob**, el valor de **Estado de concesión** debe ser **Desbloqueado** o **Disponible**.



[Reinicios inesperados de las máquinas virtuales con discos duros virtuales conectados]: #you-are-experiencing-unexpected-reboots
[Errores de eliminación de almacenamiento en la implementación de Resource Manager]: #storage-delete-errors-in-rm