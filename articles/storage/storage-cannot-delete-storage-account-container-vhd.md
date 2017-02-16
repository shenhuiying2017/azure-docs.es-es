---
title: "Solución de problemas de eliminación de cuentas, contenedores o discos duros virtuales de Azure Storage en una implementación clásica | Microsoft Docs"
description: "Solución de problemas de eliminación de cuentas de almacenamiento de Azure, contenedores o discos duros virtuales"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: tysonn
tags: storage
ms.assetid: 0f7a8243-d8dc-432a-9d37-1272a0cb3a5c
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 822bace005a6244a47c9484487dab85b1aec9d9a
ms.openlocfilehash: e20b1ca582c56da7b4fb1e2df3be90bd1c29a8b6


---
# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Solución de problemas de eliminación de cuentas de almacenamiento de Azure, contenedores o discos duros virtuales
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Es posible que al intentar eliminar la cuenta de almacenamiento de Azure, un contenedor o un disco duro virtual en [Azure Portal](https://portal.azure.com/) o el [Portal de Azure clásico](https://manage.windowsazure.com/) reciba errores. Los problemas pueden deberse a las siguientes circunstancias:

* Al eliminar una VM, el disco y el disco duro virtual no se eliminan automáticamente. Este podría ser el motivo del error en la eliminación de la cuenta de almacenamiento. No eliminamos el disco, por tanto puede usarlo para montar otra máquina virtual.
* Todavía hay una concesión en un disco o en el blob asociado con el disco.
* Sigue siendo una imagen de máquina virtual que está usando una cuenta de almacenamiento, un contenedor o un blob.

Si su problema con Azure no se trata en este artículo, visite los foros de Azure en [MSDN y Stack Overflow](https://azure.microsoft.com/support/forums/). Puede publicar su problema en ellos o en @AzureSupport en Twitter. También puede presentar una solicitud de soporte técnico de Azure; para ello seleccione **Obtener soporte técnico** en el sitio de [soporte técnico de Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Síntomas
La sección siguiente muestra los errores comunes que puede recibir al intentar eliminar las cuentas de almacenamiento de Azure, los contenedores o los discos duros virtuales.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Escenario 1: No se puede eliminar una cuenta de almacenamiento
Cuando navegue hasta la cuenta de almacenamiento en [Azure Portal](https://portal.azure.com/) o el [Portal de Azure clásico](https://manage.windowsazure.com/) y seleccione **Eliminar**, verá el mensaje de error siguiente:

*La cuenta de almacenamiento StorageAccountName contiene imágenes de máquina virtual. Asegúrese de que estas imágenes de máquina virtual se quiten antes de eliminar esta cuenta de almacenamiento.*.

También puede ver este error:

**En el Portal de Azure**:

*Error al eliminar la cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento-de-vm>. No se puede eliminar la cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento-de-vm>: 'La cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento-de-vm> tiene algunas imágenes o discos activos. Controle que se quiten las imágenes y los discos antes de eliminar esta cuenta de almacenamiento.'.*

**En el Portal de Azure clásico**:

*La cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento-de-vm> tiene algunas imágenes o discos activos; por ejemplo, xxxxxxxxx- xxxxxxxxx-O-209490240936090599. Controle que se quiten las imágenes y los discos antes de eliminar esta cuenta de almacenamiento.*

o

**En el Portal de Azure**:

*La cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento> tiene 1 contenedor que tiene artefactos de imagen o disco activos. Compruebe que estos artefactos se quitan del repositorio de imágenes antes de eliminar esta cuenta de almacenamiento*.

**En el Portal de Azure clásico**:

*Error en el envío. La cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento-de-vm> tiene 1 contenedor que tiene artefactos de imagen o disco activos. Compruebe que estos artefactos se quitan del repositorio de imágenes antes de eliminar esta cuenta de almacenamiento. Cuando se intenta eliminar una cuenta de almacenamiento y todavía hay discos activos asociados a ella, verá un mensaje que le indica que hay discos activos que deben eliminarse*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Escenario 2: No se puede eliminar un contenedor
Al intentar eliminar el contenedor de almacenamiento, podría ver el error siguiente:

*No se pudo eliminar el contenedor de almacenamiento <container name>. Error: 'Actualmente hay una concesión en el contenedor y no se especificó ningún identificador de concesión en la solicitud*.

o

*Los siguientes discos de máquina virtual usan blobs en este contenedor, por lo que este no puede eliminarse: VirtualMachineDiskName1, VirtualMachineDiskName2, etc.*

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Escenario 3: No se puede eliminar un VHD
Después de eliminar una VM e intentar eliminar después los blobs de los discos duros virtuales asociados, podría recibir el mensaje siguiente:

*Error al eliminar el blob 'path/XXXXXX-XXXXXX-os-1447379084699.vhd'. Error: 'Actualmente hay una concesión en el blob y no se especificó ningún identificador de concesión en la solicitud.*

o

*El blob "BlobName.vhd" se está usando como disco de máquina virtual "VirtualMachineDiskName", por lo que no se puede eliminar.*

## <a name="solution"></a>Solución
Para resolver los problemas más comunes, pruebe el siguiente método:

### <a name="step-1-delete-any-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Paso 1: Eliminar los discos que impiden la eliminación de la cuenta de almacenamiento, el contenedor o el VHD
1. Cambie al [Portal de Azure clásico](https://manage.windowsazure.com/).
2. Seleccione **MÁQUINA VIRTUAL** > **DISCOS**.

    ![Imagen de discos en máquinas virtuales en el Portal de Azure clásico.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)
3. Busque los discos asociados a la cuenta de almacenamiento, contenedor o disco duro virtual que quiere eliminar. Cuando compruebe la ubicación del disco, encontrará la cuenta de almacenamiento asociada, el contenedor y el disco duro virtual.

    ![Imagen que muestra la información de ubicación de los discos en el Portal de Azure clásico](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)
4. Elimine los discos realizando uno de los métodos siguientes:

  - Si no hay ninguna máquina virtual en el campo **Conectado a** del disco y, después, elimine los discos directamente.

  - Si se trata de un disco de datos, siga estos pasos:

    1. Compruebe el nombre de la máquina virtual al que está conectado el disco.
    2. Vaya a **Máquinas virtuales** > **Instancias** y, luego, busque la máquina virtual.
    3. Confirme que no hay ningún recurso usando activamente el disco.
    4. Seleccione **Desconectar disco** en la parte inferior del portal para desconectar el disco.
    5. Vaya a **Máquinas virtuales** > **Discos** y espere a que el campo **Conectado a** se quede vacío. Esto indica que el disco se ha desconectado correctamente de la máquina virtual.
    6. Seleccione **Eliminar** en la parte inferior de **Máquinas virtuales** > **Discos** para eliminar el disco.

  - Si se trata de un disco del sistema operativo (el campo **Contiene SO** tiene un valor de Windows) y se conecta a una máquina virtual, siga estos pasos para eliminar la máquina virtual. No se puede desconectar el disco del sistema operativo, por lo que hay que eliminar la máquina virtual para liberar la concesión.

    1. Compruebe el nombre de la máquina virtual a la que está conectado el disco de datos.  
    2. Vaya a **Máquinas virtuales** > **Instancias** y, luego, seleccione la máquina virtual a la que está conectado el disco.
    3. Asegúrese de que no haya nada usando activamente la máquina virtual y de que ya no la necesite.
    4. Seleccione la máquina virtual a la que está conectado el disco y, luego, haga clic en **Eliminar** > **Eliminar los discos conectados**.
    5. Vaya a **Máquinas virtuales** > **Discos** y espere a que el disco desaparezca.  Puede tardar unos minutos y es posible que tenga que actualizar la página.
    6. Si el disco no desaparece, espere a que el campo **Conectado a** se quede vacío. Esto indica que el disco se ha desconectado correctamente de la máquina virtual.  Después, seleccione el disco y haga clic en la opción **Eliminar** de la parte inferior de la página para eliminar el disco.


   > [!NOTE]
   > Si un disco está conectado a una VM, no podrá eliminarlo. Los discos se desconectan de una máquina virtual eliminada de forma asincrónica. Este campo puede tardar unos minutos en borrarse después de eliminar la VM.
   >
   >


### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Paso 2: Eliminación de las imágenes de máquina virtual que impiden la eliminación de la cuenta de almacenamiento o el contenedor
1. Cambie al [Portal de Azure clásico](https://manage.windowsazure.com/).
2. Seleccione **MÁQUINA VIRTUAL** > **IMÁGENES** y luego elimine las imágenes asociadas con la cuenta de almacenamiento, el contenedor o el disco duro virtual.

    Después, trate de eliminar otra vez la cuenta de almacenamiento, contenedor o disco duro virtual.

> [!WARNING]
> Asegúrese de hacer una copia de seguridad de cualquier contenido que desee guardar antes de eliminar la cuenta. No es posible restaurar una cuenta de almacenamiento eliminada ni recuperar el contenido que contenía antes de la eliminación. Esto también se aplica a los recursos de la cuenta: cuando se elimina un disco duro virtual, un blob, una tabla, una cola o un archivo, este se eliminará definitivamente. Asegúrese de que el recurso no está en uso.
>
>

## <a name="about-the-stopped-deallocated-status"></a>Acerca del estado Detenido (desasignado)
Las VM que se crearon en el modelo de implementación clásica y que se han conservado tendrán el estado **Detenido (desasignado)** tanto en [Azure Portal](https://portal.azure.com/) como en el [Portal de Azure clásico](https://manage.windowsazure.com/).

**Portal de Azure clásico**:

![Estado Detenido (desasignado) de VM en el Portal de Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

**Portal de Azure**:

![Estado Detenido (desasignado) de VM en el Portal de Azure clásico.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Un estado "Detenido (desasignado)" libera los recursos del equipo, como la CPU, la memoria y la red. Sin embargo, los discos se siguen conservando para que el usuario pueda volver a crear rápidamente la máquina virtual si fuera necesario. Estos discos se crean encima de los discos duros virtuales que están respaldados por Almacenamiento de Azure. La cuenta de almacenamiento tiene estos discos duros virtuales y los discos tienen concesiones sobre esos discos duros virtuales.

## <a name="next-steps"></a>Pasos siguientes
* [Eliminar una cuenta de almacenamiento](storage-create-storage-account.md#delete-a-storage-account)
* [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Cómo interrumpir la concesión bloqueada de Almacenamiento de blobs en Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)



<!--HONumber=Nov16_HO4-->


