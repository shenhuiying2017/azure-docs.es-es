---
title: "Solución de los errores que aparecen al eliminar cuentas de almacenamiento, contenedores o VHD de Azure | Microsoft Docs"
description: "Solución de los errores que aparecen al eliminar cuentas de almacenamiento, contenedores o VHD de Azure"
services: storage
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 11944dd38b1cc30106c0b76a108480c018ca39d4
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds"></a>Solución de los errores que aparecen al eliminar cuentas de almacenamiento, contenedores o VHD de Azure

Podrían surgirle errores cuando trate de eliminar una cuenta de almacenamiento, un contenedor o un disco duro virtual (VHD) de Azure en [Azure Portal](https://portal.azure.com). En este artículo se proporciona orientación para ayudarle a resolver problemas en una implementación de Azure Resource Manager.

Si este artículo no resuelve su problema con Azure, visite los foros de Azure en [MSDN y Stack Overflow](https://azure.microsoft.com/support/forums/). Puede publicar su problema en ellos o @AzureSupport en Twitter. También puede presentar una solicitud de soporte técnico de Azure; para ello seleccione **Obtener soporte técnico** en el sitio de [soporte técnico de Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Síntomas
### <a name="scenario-1"></a>Escenario 1.
Cuando trate de eliminar un VHD en una cuenta de almacenamiento en una implementación de Resource Manager, recibirá el siguiente mensaje de error:

**Error al eliminar el blob 'vhds/BlobName.vhd'. Error: Actualmente hay una concesión en el blob y no se especificó ningún identificador de concesión en la solicitud.**

Este problema puede producirse debido a que una máquina virtual (VM) tenga una concesión para el VHD que trata de eliminar.

### <a name="scenario-2"></a>Escenario 2.
Cuando trate de eliminar un contenedor en una cuenta de almacenamiento en una implementación de Resource Manager, recibirá el siguiente mensaje de error:

**No se pudo eliminar el contenedor de almacenamiento 'vhds'. Error: Actualmente hay una concesión en el contenedor y no se especificó ningún identificador de concesión en la solicitud.**

Este problema puede producirse porque el contenedor tiene un VHD que está bloqueado en el estado de la concesión.

### <a name="scenario-3"></a>Escenario 3.
Cuando trate de eliminar una cuenta de almacenamiento en una implementación de Resource Manager, recibirá el siguiente mensaje de error:

**No se pudo eliminar la cuenta de almacenamiento 'nombreCuentaDeAlmacenamiento'. Error: La cuenta de almacenamiento no se puede eliminar porque sus artefactos están en uso.**

Este problema puede producirse porque la cuenta de almacenamiento contiene un VHD que se encuentra en el estado de concesión.

## <a name="solution"></a>Solución 
Para resolver estos problemas, debe identificar el VHD que está ocasionando el error y la VM asociada. Después, desconecte el VHD de la máquina virtual (para los discos de datos) o elimine la VM que utilice el VHD (para discos de sistema operativo). De este modo, se retirará la concesión del VHD y se podrá eliminar. 

Para ello, use uno de los siguientes métodos:

### <a name="method-1---use-azure-storage-explorer"></a>Método 1: Usar el Explorador Azure Storage

### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>Paso 1: Identificar el disco duro virtual que impide la eliminación de la cuenta de almacenamiento

1. Cuando elimine la cuenta de almacenamiento, verá un cuadro de diálogo como el siguiente: 

    ![mensaje al eliminar la cuenta de almacenamiento](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. Active **Dirección URL del disco** para identificar la cuenta de almacenamiento y el disco duro virtual que impide que se elimine la cuenta de almacenamiento. En el ejemplo siguiente, la cadena que está delante de ".blob.core.windows.net" es el nombre de la cuenta de almacenamiento y "SCCM2012-2015-08-28.vhd" es el nombre del VHD.  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

### <a name="step-2-delete-the-vhd-by-using-azure-storage-explorer"></a>Paso 2 Eliminar el disco duro virtual mediante el Explorador de Azure Storage

1. Descargue e instale la versión más reciente del [Explorador de Azure Storage](http://storageexplorer.com/). Esta herramienta es una aplicación independiente de Microsoft que permite trabajar fácilmente con los datos de Azure Storage en Windows, Mac OS y Linux.
2. Abra el Explorador de Azure Storage, seleccione ![icono de cuenta](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/account.png) en la barra de la izquierda, seleccione el entorno de Azure y, después, inicie sesión.

3. Seleccione todas las suscripciones o la suscripción que contiene la cuenta de almacenamiento que desea eliminar.

    ![agregar suscripción](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/addsub.png)

4. Vaya a la cuenta de almacenamiento que se ha obtenido en la dirección URL del disco, seleccione **Contenedores de blob** > **discos duros virtuales** y busque el disco duro virtual que impide que elimine la cuenta de almacenamiento.
5. Si se encuentra el VHD, seleccione la columna **VM Name** para buscar la máquina virtual que usa este disco duro virtual.

    ![Seleccionar máquina virtual](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/check-vm.png)

6. Quite la concesión del disco duro virtual mediante Azure Portal. Para más información, consulte [Eliminación de la concesión del VHD](#remove-the-lease-from-the-vhd). 

7. Vaya al Explorador de Azure Storage, haga clic con el botón derecho en el VHD y seleccione Eliminar.

8. Elimina la cuenta de almacenamiento.

### <a name="method-2---use-azure-portal"></a>Método 2: Usar Azure Portal 

#### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>Paso 1: Identificar el disco duro virtual que impide la eliminación de la cuenta de almacenamiento

1. Cuando elimine la cuenta de almacenamiento, verá un cuadro de diálogo como el siguiente: 

    ![mensaje al eliminar la cuenta de almacenamiento](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. Active **Dirección URL del disco** para identificar la cuenta de almacenamiento y el disco duro virtual que impide que se elimine la cuenta de almacenamiento. En el ejemplo siguiente, la cadena que está delante de ".blob.core.windows.net" es el nombre de la cuenta de almacenamiento y "SCCM2012-2015-08-28.vhd" es el nombre del VHD.  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

2. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
3. En el menú Concentrador, seleccione **Todos los recursos**. Vaya a la cuenta de almacenamiento y seleccione **Blobs** > **vhds**.

    ![Captura de pantalla del portal en la que se han resaltado la cuenta de almacenamiento y el contenedor "vhds".](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

4. Busque el disco duro virtual que se ha obtenido de la dirección URL del disco. Después, determine la VM que esté usando el VHD. Normalmente, para determinar la VM que contiene el VHD, basta con consultar el nombre de este último:

Máquina virtual en el modelo de desarrollo mediante Resource Manager

   * Los discos de sistema operativo suelen seguir esta convención de nomenclatura: NombreVM-AAAA-MM-DD-HHMMSS.vhd
   * Los discos de sistema operativo suelen seguir esta convención de nomenclatura: NombreVM-AAAA-MM-DD-HHMMSS.vhd

Máquina virtual en modelo de desarrollo clásico

   * Los discos de sistema operativo suelen seguir esta convención de nomenclatura: NombreServicioEnLaNube-NombreVM-AAAA-MM-DD-HHMMSS.vhd
   * Los discos de datos suelen seguir esta convención de nomenclatura: NombreServicioEnLaNube-NombreVM-AAAA-MM-DD-HHMMSS.vhd

#### <a name="step-2-remove-the-lease-from-the-vhd"></a>Paso 2: Eliminación de la concesión del VHD

[Quite la concesión del disco duro virtual](#remove-the-lease-from-the-vhd)y, después, elimine la cuenta de almacenamiento.

## <a name="what-is-a-lease"></a>¿Qué es una concesión?
Una concesión es un bloqueo que puede utilizarse para controlar el acceso a un blob (por ejemplo, un VHD). Cuando un blob está concedido, solo los propietarios de la concesión pueden acceder a él. Una concesión es importante por los motivos siguientes:

* Se evitan daños en los datos si varios propietarios tratan de escribir en la misma parte del blob a la vez.
* Impide que el blob se elimine si hay algo que lo esté usando activamente (por ejemplo, una VM).
* Impide que la cuenta de almacenamiento se elimine si hay algo que la esté usando activamente (por ejemplo, una VM).

### <a name="remove-the-lease-from-the-vhd"></a>Eliminación de la concesión del VHD
Si el disco duro virtual es un disco de sistema operativo, debe eliminar la máquina virtual para quitar la concesión:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú **Concentrador**, haga clic en **Máquinas virtuales**.
3. Seleccione la VM que tenga una concesión para el VHD.
4. Asegúrese de que no haya nada usando activamente la máquina virtual y de que ya no la necesite.
5. En la parte superior de la hoja **VM details** (Detalles de la VM), seleccione **Eliminar** y, después, haga clic en **Sí** para confirmar.
6. La máquina virtual se debe eliminar, pero el disco duro virtual se puede conservar. No obstante, el VHD ya no debería tener ninguna concesión. Es posible que la concesión tarde unos minutos en liberarse. Para comprobar que se haya retirado la concesión, vaya a **Todos los recursos** > **Nombre de cuenta de almacenamiento** > **Blobs** > **vhds**. En el panel **Propiedades del blob**, el valor de **Estado de concesión** debe ser **Desbloqueado**.

Si el disco duro virtual es un disco de datos, desasócielo de la máquina virtual para quitar la concesión:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú **Concentrador**, haga clic en **Máquinas virtuales**.
3. Seleccione la VM que tenga una concesión para el VHD.
4. Seleccione **Discos** en la hoja **VM details** (Detalles de la VM).
5. Seleccione el disco de datos que tenga una concesión para el VHD. Puede determinar que VHD está conectado al disco consultando la URL del primero.
6. Asegúrese de que no haya nada utilizando el disco de datos de forma activa.
7. Haga clic en **Desconectar** en la hoja **Detalles del disco**.
8. Ahora el disco debe estar desconectado de la VM y el VHD no debe tener ninguna concesión. Es posible que la concesión tarde unos minutos en liberarse. Para comprobar que se haya retirado la concesión, vaya a **Todos los recursos** > **Nombre de cuenta de almacenamiento** > **Blobs** > **vhds**. En el panel **Propiedades del blob**, el valor de **Estado de concesión** debe ser **Desbloqueado**.

## <a name="next-steps"></a>Pasos siguientes
* [Eliminar una cuenta de almacenamiento](storage-create-storage-account.md#delete-a-storage-account)
* [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Cómo interrumpir la concesión bloqueada de Almacenamiento de blobs en Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

