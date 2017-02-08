---
title: "Solución de errores al eliminar cuentas, contenedores o VHD de Azure Storage en una implementación de Resource Manager | Microsoft Docs"
description: "Solución de errores al eliminar cuentas de almacenamiento, contenedores o VHD de Azure en una implementación de Resource Manager"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 75d5e82e2f4e747747f24376239e23f6512f916a


---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>Solución de errores al eliminar cuentas de almacenamiento, contenedores o VHD de Azure en una implementación de Resource Manager
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

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

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>Paso 1: Identificación del VHD que causa el problema y la VM asociada
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú **Concentrador**, seleccione **Todos los recursos**. Vaya a la cuenta de almacenamiento que quiere eliminar y, después, seleccione **Blobs** > **vhds**.

    ![Captura de pantalla del portal en la que se han resaltado la cuenta de almacenamiento y el contenedor "vhds".](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)
3. Compruebe las propiedades de cada VHD en el contenedor. Busque el VHD que se encuentre en el estado **Concedido**. Después, determine la VM que esté usando el VHD. Normalmente, para determinar la VM que contiene el VHD, basta con consultar el nombre de este último:

   * Los discos de sistema operativo suelen seguir esta convención de nomenclatura: NombreVMAAAAMMDDHHMMSS.vhd.
   * Los discos de datos suelen seguir esta convención de nomenclatura: NombreVM-AAAAMMDD-HHMMSS.vhd.

     ![Captura de pantalla de la información del contenedor en el portal, donde se muestran el nombre de la VM, el estado de concesión "Bloqueado" y el estado de concesión "Concedido" resaltados.](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>Paso 2: Eliminación de la concesión del VHD
Para eliminar la VM que esté usando el VHD (en el caso de los discos de sistema operativo), siga estos pasos:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú **Concentrador**, haga clic en **Máquinas virtuales**.
3. Seleccione la VM que tenga una concesión para el VHD.
4. Asegúrese de que no haya nada usando activamente la máquina virtual y de que ya no la necesite.
5. En la parte superior de la hoja **VM details** (Detalles de la VM), seleccione **Eliminar** y, después, haga clic en **Sí** para confirmar.
6. Se debe haber eliminado la VM, pero el VHD se habrá conservado. No obstante, el VHD ya no debería tener ninguna concesión. Es posible que la concesión tarde unos minutos en liberarse. Para comprobar que se haya retirado la concesión, vaya a **Todos los recursos** > **Nombre de cuenta de almacenamiento** > **Blobs** > **vhds**. En el panel **Propiedades del blob**, el valor de **Estado de concesión** debe ser **Desbloqueado**.

Para desconectar el VHD de la máquina virtual que lo está utilizando (en el caso de los discos de datos), siga estos pasos:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú **Concentrador**, haga clic en **Máquinas virtuales**.
3. Seleccione la VM que tenga una concesión para el VHD.
4. Seleccione **Discos** en la hoja **VM details** (Detalles de la VM).
5. Seleccione el disco de datos que tenga una concesión para el VHD. Puede determinar que VHD está conectado al disco consultando la URL del primero.
6. Asegúrese de que no haya nada utilizando el disco de datos de forma activa.
7. Haga clic en **Desconectar** en la hoja **Detalles del disco**.
8. Ahora el disco debe estar desconectado de la VM y el VHD no debe tener ninguna concesión. Es posible que la concesión tarde unos minutos en liberarse. Para comprobar que se haya retirado la concesión, vaya a **Todos los recursos** > **Nombre de cuenta de almacenamiento** > **Blobs** > **vhds**. En el panel **Propiedades del blob**, el valor de **Estado de concesión** debe ser **Desbloqueado**.

## <a name="what-is-a-lease"></a>¿Qué es una concesión?
Una concesión es un bloqueo que puede utilizarse para controlar el acceso a un blob (por ejemplo, un VHD). Cuando un blob está concedido, solo los propietarios de la concesión pueden acceder a él. Una concesión es importante por los motivos siguientes:

* Se evitan daños en los datos si varios propietarios tratan de escribir en la misma parte del blob a la vez.
* Impide que el blob se elimine si hay algo que lo esté usando activamente (por ejemplo, una VM).
* Impide que la cuenta de almacenamiento se elimine si hay algo que la esté usando activamente (por ejemplo, una VM).

## <a name="next-steps"></a>Pasos siguientes
* [Eliminar una cuenta de almacenamiento](storage-create-storage-account.md#delete-a-storage-account)
* [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Cómo interrumpir la concesión bloqueada de Almacenamiento de blobs en Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)



<!--HONumber=Nov16_HO3-->


