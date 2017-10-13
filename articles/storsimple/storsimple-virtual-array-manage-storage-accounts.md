---
title: "Administración de credenciales de cuentas de almacenamiento de StorSimple Virtual Array | Microsoft Docs"
description: "Se explica cómo usar la página de configuración de StorSimple Device Manager para agregar, editar, eliminar o rotar las claves de seguridad de las credenciales de una cuenta de almacenamiento asociada con StorSimple Virtual Array."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: a4ce2d329d0e1399cffaf886adf2b95e34b9cd7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Uso de StorSimple Device Manager para administrar credenciales de cuentas de almacenamiento de StorSimple Virtual Array

## <a name="overview"></a>Información general
La sección **Configuración** de la hoja del servicio StorSimple Device Manager de StorSimple Virtual Array presenta los parámetros de servicio globales que se pueden crear en el servicio StorSimple Manager. Estos parámetros se pueden aplicar a todos los dispositivos conectados al servicio e incluyen:

* Credenciales de la cuenta de almacenamiento
* Registros de control de acceso
  
  ![Panel del servicio Device Manager](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

En este tutorial se explica cómo puede agregar, editar o eliminar cuentas de almacenamiento de StorSimple Virtual Array. La información de este tutorial solo se aplica a StorSimple Virtual Array. Para más información sobre cómo administrar cuentas de almacenamiento de la serie 8000, consulte [Usar el servicio StorSimple Manager para administrar su cuenta de almacenamiento](storsimple-manage-storage-accounts.md).

Las credenciales de las cuentas de almacenamiento son las credenciales que usa el dispositivo para acceder a su cuenta de almacenamiento con el proveedor de servicios en la nube. En el caso de las cuentas de almacenamiento de Microsoft Azure, se trata de credenciales como el nombre de cuenta y la clave de acceso primaria.

En la hoja **Credenciales de cuenta de almacenamiento**, todas las credenciales de las cuentas de almacenamiento que se crean para la suscripción de facturación se muestran en un formato tabular que contiene la información siguiente:

* **Nombre** : nombre único asignado a la cuenta cuando se creó.
* **SSL habilitado** : indica si el SSL está habilitado y la comunicación de dispositivo a la nube se realiza a través del canal seguro.
  
  ![Sección de configuración](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Las tareas más comunes relacionadas con las credenciales de las cuentas de almacenamiento que se pueden realizar en la hoja **Credenciales de cuenta de almacenamiento** son:

* Incorporación de una credencial de cuenta de almacenamiento
* Edición de una credencial de cuenta de almacenamiento
* Eliminación de una credencial de cuenta de almacenamiento

## <a name="types-of-storage-account-credentials"></a>Tipos de credenciales de cuenta de almacenamiento
Hay tres tipos de credenciales de cuentas de almacenamiento que se pueden usar con el dispositivo StorSimple.

* **Credenciales de cuentas de almacenamiento generadas automáticamente**: como sugiere su nombre, este tipo de credenciales de cuenta de almacenamiento se genera automáticamente cuando se crea el servicio por primera vez. Para más información sobre cómo se crean estas credenciales de cuenta de almacenamiento, consulte [Creación de un nuevo servicio](storsimple-virtual-array-manage-service.md#create-a-service).
* **Credenciales de cuentas de almacenamiento en la suscripción al servicio**: se trata de credenciales de cuentas de almacenamiento de Azure que están asociadas a la misma suscripción que la del servicio. Para más información sobre cómo se crean estas cuentas de almacenamiento, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/common/storage-create-storage-account.md).
* **Credenciales de las cuentas de almacenamiento fuera de la suscripción al servicio**: son las credenciales de las cuentas de almacenamiento de Azure no asociadas al servicio que probablemente existían antes de que se crease el servicio.

## <a name="add-a-storage-account-credential"></a>Incorporación de una credencial de cuenta de almacenamiento
Puede agregar una credencial de cuenta de almacenamiento a la configuración del servicio StorSimple Manager proporcionando un nombre descriptivo único y las credenciales de acceso vinculadas a la cuenta de almacenamiento. También tiene la opción de habilitar el modo de Capa de sockets seguros (SSL) para crear un canal seguro para la comunicación de red entre su dispositivo y la nube.

Puede crear varias cuentas para un proveedor de servicios en la nube determinado. Mientras se guardan las credenciales de la cuenta de almacenamiento, el servicio intenta comunicarse con el proveedor de servicios en la nube. Las credenciales y el material de acceso que proporcionó se autenticarán en este momento. Solo si la autenticación se realiza correctamente se creará una credencial de cuenta de almacenamiento. Si se produce un error en la autenticación, se mostrará un mensaje de error adecuado.

Utilice los procedimientos siguientes para agregar las credenciales de la cuenta de almacenamiento de Azure:

* Agregar una credencial de cuenta de almacenamiento que tenga la misma suscripción de Azure que el servicio Device Manager
* Incorporación de una credencial de cuenta de almacenamiento de Azure que está fuera de la suscripción al servicio Device Manager

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Agregar una credencial de cuenta de almacenamiento que tenga la misma suscripción de Azure que el servicio Device Manager

1. Vaya al servicio Device Manager, selecciónelo y haga doble clic en él. Se abrirá la hoja **Introducción**.
2. Seleccione **Credenciales de cuenta de almacenamiento** dentro de la sección **Configuración**.
3. Haga clic en **Agregar**.
4. En la hoja **Agregar una cuenta de almacenamiento**, haga lo siguiente:
   
    1. En **Suscripción**, seleccione **Actual**.
    2. Proporcione el nombre de la cuenta de almacenamiento de Azure.
    3. Seleccione **Habilitar** para crear un canal seguro para la comunicación de red entre su dispositivo de StorSimple y la nube. Seleccione **Deshabilitar** solo si está trabajando en una nube privada.
    4. Haga clic en **Agregar**. Recibirá una notificación cuando la cuenta de almacenamiento se cree correctamente.<br></br>
   
        ![Incorporación de una credencial de cuenta de almacenamiento existente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Incorporación de una credencial de cuenta de almacenamiento de Azure que está fuera de la suscripción al servicio Device Manager

1. Vaya al servicio Device Manager, selecciónelo y haga doble clic en él. Se abrirá la hoja **Introducción**.
2. Seleccione **Credenciales de cuenta de almacenamiento** dentro de la sección **Configuración**. Esta acción muestra todas las credenciales de cuenta de almacenamiento existentes asociadas con el servicio StorSimple Device Manager.
3. Haga clic en **Agregar**.
4. En la hoja **Agregar una cuenta de almacenamiento**, haga lo siguiente:
   
    1. En **Suscripción**, seleccione **Otro**.
   
    2. Proporcione el nombre de la credencial de la cuenta de almacenamiento de Azure.
   
    3. En el cuadro de texto **Clave de acceso de la cuenta de almacenamiento**, proporcione la clave de acceso principal para la credencial de la cuenta de almacenamiento de Azure. Para obtener esta clave, vaya al servicio Azure Storage, seleccione la credencial de la cuenta de almacenamiento y haga clic en **Administrar claves de cuenta**. Ahora puede copiar la clave de acceso principal.
   
    4. Para habilitar SSL, haga clic en el botón **Habilitar** para crear un canal seguro para la comunicación de red entre StorSimple Device Manager y la nube. Haga clic en el botón **Deshabilitar** solo si está trabajando en una nube privada.
   
    5. Haga clic en **Agregar**. Recibirá una notificación cuando la credencial de la cuenta de almacenamiento se haya creado correctamente.

5. La credencial de la cuenta de almacenamiento recién creada aparece en la hoja del servicio StorSimple Configure Device Manager en **Credenciales de cuenta de almacenamiento**.
   
    ![Incorporación de una credencial de cuenta de almacenamiento que está fuera de la suscripción al servicio Device Manager](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Edición de una credencial de cuenta de almacenamiento
Puede editar una credencial de cuenta de almacenamiento utilizada por su dispositivo. Si edita una credencial de cuenta de almacenamiento que está actualmente en uso, los campos disponibles para modificar serán la clave de acceso y el modo SSL de esta. Puede proporcionar la nueva clave de acceso de almacenamiento o modificar la selección de **Habilitar modo SSL** y guardar la configuración actualizada.

#### <a name="to-edit-a-storage-account-credential"></a>Edición de una credencial de cuenta de almacenamiento
1. Vaya al servicio Device Manager, selecciónelo y haga doble clic en él. Se abrirá la hoja **Introducción**.
2. Seleccione **Credenciales de cuenta de almacenamiento** dentro de la sección **Configuración**. Esta acción muestra todas las credenciales de cuenta de almacenamiento existentes asociadas con el servicio StorSimple Device Manager.
3. En la lista tabular de credenciales de la cuenta de almacenamiento, seleccione y haga doble clic en la cuenta que desea modificar.
4. En la hoja **Propiedades** de la credencial de cuenta de almacenamiento, haga lo siguiente:
   
   1. Si es necesario, puede modificar la selección **Habilitar modo SSL**.
   2. Puede optar por regenerar las claves de acceso de la credencial de la cuenta de almacenamiento. Para más información, consulte [Nueva generación de las claves de acceso de almacenamiento](../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys). Proporcione la nueva clave de la credencial de cuenta de almacenamiento. Para una cuenta de almacenamiento de Azure, esta es la clave de acceso principal.
   3. Haga clic en **Guardar** en la parte superior de la hoja **Propiedades** para guardar los cambios. La configuración se actualizará en la hoja **Credenciales de cuenta de almacenamiento**.
      
      ![Edición de una credencial de cuenta de almacenamiento](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Eliminación de una credencial de cuenta de almacenamiento
> [!IMPORTANT]
> Solo puede eliminar una credencial de cuenta de almacenamiento si no está en uso. Si una credencial de cuenta de almacenamiento está en uso, se le notificará.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Eliminación de una credencial de cuenta de almacenamiento
1. Vaya al servicio Device Manager, selecciónelo y haga doble clic en él. Se abrirá la hoja **Introducción**.
2. Seleccione **Credenciales de cuenta de almacenamiento** dentro de la sección **Configuración**. Esta acción muestra todas las credenciales de cuenta de almacenamiento existentes asociadas con el servicio StorSimple Device Manager.
3. En la lista tabular de credenciales de la cuenta de almacenamiento, seleccione y haga doble clic en la cuenta que desea eliminar.
4. En la hoja **Propiedades** de la credencial de cuenta de almacenamiento, haga lo siguiente:
   
   1. Haga clic en **Eliminar** para eliminar las credenciales.
   2. Cuando se le pida confirmación, haga clic en **Sí** para continuar con la eliminación. La lista tabular se actualizará para reflejar los cambios.
      
      ![Eliminación de una credencial de cuenta de almacenamiento](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Sincronización de las claves de la credencial de cuenta de almacenamiento.
Por motivos de seguridad, la rotación de claves suele ser un requisito en los centros de datos. Un administrador de Microsoft Azure puede volver a generar o cambiar la clave principal o secundaria accediendo directamente a la credencial de la cuenta de almacenamiento (mediante el servicio Microsoft Azure Storage). El servicio StorSimple Device Manager no verá este cambio automáticamente.

Para informar al servicio StorSimple Device Manager del cambio, tendrá que acceder a este servicio, entrar en la credencial de la cuenta de almacenamiento y sincronizar la clave principal o secundaria (en función de cuál se haya cambiado). A continuación, el servicio obtiene la clave más reciente, cifra las claves y envía la clave cifrada al dispositivo.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Sincronización de las claves de las credenciales de las cuentas de almacenamiento de la misma suscripción que el servicio (solo Azure)
1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, en la sección **Configuración**, haga clic en **Credenciales de cuenta de almacenamiento**.
2. En la hoja **Credenciales de cuenta de almacenamiento**, en la lista de credenciales, seleccione aquella cuyas claves desea sincronizar.
3. En la hoja **Propiedades** de la credencial de cuenta de almacenamiento seleccionada, haga lo siguiente:
   
    1. Haga clic en **Más** y, a continuación, haga clic en **Clave de acceso de sincronización**.
   
    2. Cuando se le solicite confirmación, haga clic en **Sincronizar clave** para completar la sincronización.
    
4. En el servicio StorSimple Device Manager, deberá actualizar la clave que se cambió anteriormente en el servicio Microsoft Azure Storage. En la hoja **Sincronizar clave de cuenta de almacenamiento**, si la clave de acceso principal se ha cambiado (regenerado), haga clic en Principal y, a continuación, haga clic en **Sincronizar clave**. Si se cambió la clave secundaria, haga clic en **Secundaria** y, luego, en **Sincronizar clave**.
   
    ![Sincronización de la clave de acceso](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-acess-key.png)

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información para [administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

