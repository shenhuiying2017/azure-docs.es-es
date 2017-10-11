---
title: "Administración de credenciales de la cuenta de almacenamiento de StorSimple para dispositivos de la serie Microsoft Azure StorSimple 8000 | Microsoft Docs"
description: "Explica cómo usar la página Configurar de StorSimple Device Manager para agregar, editar, eliminar o girar las claves de seguridad de una cuenta de almacenamiento."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 36058ad69ea670998b50cf9038741c294a5b79ab
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Uso del servicio StorSimple Device Manager para administrar las credenciales de la cuenta de almacenamiento

## <a name="overview"></a>Información general

La sección **Configuración** de la hoja del servicio StorSimple Device Manager presenta todos los parámetros de servicio globales que se pueden crear en el servicio StorSimple Device Manager. Estos parámetros se pueden aplicar a todos los dispositivos conectados al servicio e incluyen:

* Credenciales de la cuenta de almacenamiento
* Plantillas de ancho de banda 
* Registros de control de acceso 

Este tutorial explica cómo agregar, editar o eliminar las credenciales de la cuenta de almacenamiento, o cómo girar las claves de seguridad de una cuenta de almacenamiento.

 ![Lista de credenciales de cuenta de almacenamiento](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Las cuentas de almacenamiento contienen las credenciales que usa el dispositivo StorSimple para acceder a su cuenta de almacenamiento con el proveedor de servicios en la nube. En el caso de las cuentas de almacenamiento de Microsoft Azure, se trata de credenciales como el nombre de cuenta y la clave de acceso primaria. 

En la hoja **Credenciales de cuenta de almacenamiento**, todas las cuentas de almacenamiento que se crean para la suscripción de facturación se muestran en un formato tabular que contiene la información siguiente:

* **Nombre** : nombre único asignado a la cuenta cuando se creó.
* **SSL habilitado** : indica si el SSL está habilitado y la comunicación de dispositivo a la nube se realiza a través del canal seguro.
* **Usado por** : número de volúmenes que usan la cuenta de almacenamiento.

Las tareas más comunes relacionadas con las cuentas de almacenamiento que se pueden realizar son:

* Agregar una cuenta de almacenamiento 
* Editar una cuenta de almacenamiento 
* Eliminar una cuenta de almacenamiento 
* Rotación de claves de cuentas de almacenamiento 

## <a name="types-of-storage-accounts"></a>Tipos de cuentas de almacenamiento

Hay tres tipos de cuentas de almacenamiento que se pueden usar con el dispositivo StorSimple.

* **Cuentas de almacenamiento generadas automáticamente** : como sugiere su nombre, este tipo de cuenta de almacenamiento se genera automáticamente cuando se crea el servicio. Para obtener más información sobre cómo se crea esta cuenta de almacenamiento, consulte el [Paso 1: Crear un nuevo servicio](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service), en [Implementar el dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md). 
* **Cuentas de almacenamiento en la suscripción al servicio** : se trata de cuentas de almacenamiento de Azure que están asociadas a la misma suscripción que la del servicio. Para obtener más información sobre cómo se crean estas cuentas de almacenamiento, vea [Acerca de las cuentas de almacenamiento de Azure](../storage/common/storage-create-storage-account.md). 
* **Cuentas de almacenamiento fuera de la suscripción al servicio** : son las cuentas de almacenamiento de Azure no asociadas al servicio que probablemente existían antes de que se crease el servicio.

## <a name="add-a-storage-account"></a>Agregar una cuenta de almacenamiento

Puede agregar una cuenta de almacenamiento proporcionando un nombre descriptivo único y las credenciales de acceso vinculadas a la cuenta de almacenamiento (con el proveedor de servicios en la nube especificado). También tiene la opción de habilitar el modo de Capa de sockets seguros (SSL) para crear un canal seguro para la comunicación de red entre su dispositivo y la nube.

Puede crear varias cuentas para un proveedor de servicios en la nube determinado. No obstante, tenga en cuenta que, después de crear una cuenta de almacenamiento, no puede cambiar el proveedor de servicios en la nube.

Mientras se guarda la cuenta de almacenamiento, el servicio intenta comunicarse con el proveedor de servicios en la nube. Las credenciales y el material de acceso que proporcionó se autenticarán en este momento. Solo si la autenticación se realiza correctamente se crea una cuenta de almacenamiento. Si se produce un error en la autenticación, se mostrará un mensaje de error adecuado.

Utilice los procedimientos siguientes para agregar las credenciales de la cuenta de almacenamiento de Azure:

* Agregar una credencial de cuenta de almacenamiento que tenga la misma suscripción de Azure que el servicio Device Manager
* Incorporación de una credencial de cuenta de almacenamiento de Azure que está fuera de la suscripción al servicio Device Manager

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Incorporación de una credencial de cuenta de almacenamiento de Azure que está fuera de la suscripción al servicio StorSimple Device Manager

1. Vaya al servicio StorSimple Device Manager, selecciónelo y haga doble clic en él. Se abrirá la hoja **Introducción**.
2. Seleccione **Credenciales de cuenta de almacenamiento** dentro de la sección **Configuración**. Esta acción muestra todas las credenciales de cuenta de almacenamiento existentes asociadas con el servicio StorSimple Device Manager.
3. Haga clic en **Agregar**.
4. En la hoja **Agregar credencial de cuenta de almacenamiento**, realice los siguientes pasos:
   
    1. En **Suscripción**, seleccione **Otro**.
   
    2. Proporcione el nombre de la credencial de la cuenta de almacenamiento de Azure.
   
    3. En el cuadro de texto **Clave de acceso de la cuenta de almacenamiento**, proporcione la clave de acceso principal para la credencial de la cuenta de almacenamiento de Azure. Para obtener esta clave, vaya al servicio Azure Storage, seleccione la credencial de la cuenta de almacenamiento y haga clic en **Administrar claves de cuenta**. Ahora puede copiar la clave de acceso principal.
   
    4. Para habilitar SSL, haga clic en el botón **Habilitar** para crear un canal seguro para la comunicación de red entre StorSimple Device Manager y la nube. Haga clic en el botón **Deshabilitar** solo si está trabajando en una nube privada.
   
    5. Haga clic en **Agregar**. Recibirá una notificación cuando la credencial de la cuenta de almacenamiento se haya creado correctamente.

5. La credencial de la cuenta de almacenamiento recién creada aparece en la hoja del servicio StorSimple Configure Device Manager en **Credenciales de cuenta de almacenamiento**.
   


## <a name="edit-a-storage-account"></a>Editar una cuenta de almacenamiento

Puede editar una cuenta de almacenamiento usada por un contenedor de volúmenes. Si edita una cuenta de almacenamiento que está actualmente en uso, el único campo que puede modificar es la clave de acceso de la cuenta de almacenamiento. Puede proporcionar la nueva clave de acceso de almacenamiento y guardar la configuración actualizada.

#### <a name="to-edit-a-storage-account"></a>Para editar una cuenta de almacenamiento

1. Vaya al servicio StorSimple Device Manager. En la sección **Configuración**, haga clic en **Credenciales de cuenta de almacenamiento**.

    ![Credenciales de la cuenta de almacenamiento](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. En la hoja **Credenciales de la cuenta de almacenamiento**, en la lista de credenciales de la cuenta de almacenamiento, seleccione y haga clic en la que se va a editar. 

3. Puede modificar la selección **Habilitar SSL**. También puede hacer clic en **Más...** y, a continuación, seleccionar **Clave de acceso de sincronización** para girar las claves de acceso de la cuenta de almacenamiento. Vaya a [Rotación de claves de cuentas de almacenamiento](#key-rotation-of-storage-accounts) para más información sobre cómo realizar la rotación de claves. Después de haber modificado la configuración, haga clic en **Guardar**. 

    ![Guardar credenciales de la cuenta de almacenamiento editada](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Cuando se le pida confirmación, haga clic en **Sí**. 

    ![Confirmar modificaciones](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

La configuración se actualiza y se guarda para su cuenta de almacenamiento. 

## <a name="delete-a-storage-account"></a>Eliminar una cuenta de almacenamiento

> [!IMPORTANT]
> Puede eliminar una cuenta de almacenamiento solo si no la usa un contenedor de volúmenes. Si un contenedor de volúmenes está usando una cuenta de almacenamiento, primero elimine el contenedor de volúmenes y, a continuación, elimine la cuenta de almacenamiento asociada.

#### <a name="to-delete-a-storage-account"></a>Para eliminar una cuenta de almacenamiento

1. Vaya al servicio StorSimple Device Manager. En la sección **Configuración**, haga clic en **Credenciales de cuenta de almacenamiento**.

2. En la lista tabular de cuentas de almacenamiento, desplace el mouse sobre la cuenta que desea eliminar. Haga clic con el botón derecho para invocar el menú contextual y seleccione **Eliminar**.

    ![Eliminar credenciales de la cuenta de almacenamiento](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Cuando se le pida confirmación, haga clic en **Sí** para continuar con la eliminación. La lista tabular se actualizará para reflejar los cambios.

    ![Confirmar eliminación](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Rotación de claves de cuentas de almacenamiento

Por motivos de seguridad, la rotación de claves suele ser un requisito en los centros de datos. Cada suscripción de Microsoft Azure puede tener una o más cuentas de almacenamiento asociadas. El acceso a estas cuentas se controla mediante la suscripción y las teclas de acceso de cada cuenta de almacenamiento. 

Cuando se crea una cuenta de almacenamiento, Microsoft Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para la autenticación cuando se accede a la cuenta de almacenamiento. El hecho de tener dos claves de acceso de almacenamiento permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien tener acceso a ese servicio. La clave que está actualmente en uso es la clave *principal* y la clave de la copia de seguridad es la clave *secundaria*. Una de estas dos claves debe suministrarse cuando el dispositivo de Microsoft Azure StorSimple tiene acceso a su proveedor de servicios de almacenamiento en la nube.

## <a name="what-is-key-rotation"></a>¿Qué es la rotación de claves?

Normalmente, las aplicaciones usan solo una de las claves para acceder a los datos. Después de un período de tiempo, puede hacer que sus aplicaciones pasen a usar la clave secundaria. Después de hacer que sus aplicaciones cambien a la clave secundaria, puede retirar la primera clave y, a continuación, generar una nueva clave. Al usar las dos claves de esta forma permite que las aplicaciones accedan a los datos sin incurrir en tiempo de inactividad.

Las claves de la cuenta de almacenamiento siempre se almacenan en el servicio en un formato cifrado. Sin embargo, pueden restablecerse mediante el servicio StorSimple Device Manager. El servicio puede obtener la clave principal y secundaria de todas las cuentas de almacenamiento de la misma suscripción, incluidas las cuentas creadas en el servicio de almacenamiento, así como las cuentas de almacenamiento predeterminadas que se generaron cuando se creó el servicio StorSimple Device Manager. El servicio StorSimple Device Manager siempre obtendrá estas claves del Portal de Azure clásico y, a continuación, las almacenará de forma cifrada.

## <a name="rotation-workflow"></a>Flujo de trabajo de rotación

Un administrador de Microsoft Azure puede volver a generar o cambiar la clave principal o secundaria accediendo directamente a la cuenta de almacenamiento (mediante el servicio de almacenamiento de Microsoft Azure). El servicio StorSimple Device Manager no verá este cambio automáticamente.

Para informar al servicio StorSimple Device Manager del cambio, tendrá que acceder a este servicio, acceder a la cuenta de almacenamiento y sincronizar la clave principal o secundaria (en función de cuál se haya cambiado). A continuación, el servicio obtiene la clave más reciente, cifra las claves y envía la clave cifrada al dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Para sincronizar las claves de cuentas de almacenamiento de la misma suscripción que el servicio 
1. Vaya al servicio StorSimple Device Manager. En la sección **Configuración**, haga clic en **Credenciales de cuenta de almacenamiento**.
2. En la lista tabular de las cuentas de almacenamiento, haga clic en la que desea modificar. 

    ![sincronizar claves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Haga clic en **...Más** y, a continuación, seleccione **Clave de acceso de sincronización para girar**.   

    ![sincronizar claves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. En el servicio StorSimple Device Manager, deberá actualizar la clave que se cambió anteriormente en el servicio Microsoft Azure Storage. Si la clave de acceso principal se cambió (se volvió a generar), seleccione la clave **principal**. Si se cambió la clave secundaria, seleccione la clave **secundaria**. Haga clic en **Sincronizar clave**.
      
      ![sincronizar claves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Recibirá una notificación cuando la clave se sincronice correctamente.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Para sincronizar las claves de cuentas de almacenamiento fuera de la suscripción al servicio
1. En la página **Servicios**, haga clic en la pestaña **Configurar**.
2. Haga clic en **Agregar/editar cuentas de almacenamiento**.
3. En el cuadro de diálogo, haga lo siguiente:
   
   1. Seleccione la cuenta de almacenamiento con la clave de acceso que desea actualizar.
   2. Deberá actualizar la clave de acceso de almacenamiento en el servicio de StorSimple Device Manager. En este caso, puede ver la clave de acceso de almacenamiento. Escriba la nueva clave en el cuadro **Clave de acceso de la cuenta de almacenamiento**. 
   3. Guarde los cambios. Ahora debería estar actualizada la clave de acceso de la cuenta de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información acerca de la [Seguridad de StorSimple](storsimple-8000-security.md).
* Más información sobre el [uso del servicio StorSimple Device Manager para administrar su dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

