---
title: Implementar el servicio Administrador de dispositivos de StorSimple en Azure | Microsoft Docs
description: "Aquí encontrará información acerca de cómo crear y eliminar el servicio StorSimple Device Manager en Azure Portal, así como una descripción acerca de cómo administrar la clave de registro del servicio."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: alkohli
ms.openlocfilehash: 96dcda25cde2473387842fd01421b6bb619e4ece
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Implementar el servicio Administrador de dispositivos de StorSimple para dispositivos de la serie StorSimple 8000

## <a name="overview"></a>Información general

El servicio StorSimple Device Manager se ejecuta en Microsoft Azure y se conecta a varios dispositivos de StorSimple. Después de crear el servicio, lo puede usar para administrar todos los dispositivos que están conectados al servicio Administrador de dispositivos de StorSimple desde una única ubicación central, con lo que se reduce la carga administrativa.

En este tutorial se describen los pasos necesarios para la creación, la eliminación, la migración del servicio y la administración de la clave de registro del servicio. La información contenida en este artículo se aplica solo a los dispositivos de la serie StorSimple 8000. Para más información sobre StorSimple Virtual Array, vaya a [Implementación del servicio Administrador de dispositivos de StorSimple en una instancia de StorSimple Virtual Array](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> Todos los administradores de dispositivos de StorSimple clásico realizarán la transición automáticamente al nuevo Azure Portal. Si tiene alguna pregunta, consulte [Preguntas más frecuentes: realizar la transición a Azure Portal](storsimple-8000-move-azure-portal-faq.md). No se admiten los cmdlets de PowerShell de Azure Service Manager (ASM) después de cambiar al nuevo Azure Portal. Actualice los scripts para administrar los dispositivos y, después, vea [Use Azure Resource Manager SDK-based scripts to manage StorSimple devices](storsimple-8000-automation-azurerm-scripts.md) (Uso de scripts basados en SKD de Azure Resource Manager para administrar dispositivos de StorSimple) para obtener más información. El nuevo Azure Portal admite dispositivos que ejecutan la versión Update 5.0 o posterior. Si el dispositivo no está actualizado, instale Update 5 inmediatamente. Para más información, vaya a [Instalación de Update 5](storsimple-8000-install-update-5.md). Si está usando StorSimple Cloud Appliance (8010/8020), no puede actualizar una aplicación en la nube. Utilice la versión más reciente del software para crear una nueva aplicación en la nube con la versión Update 5.0 y después realizar una conmutación por error en la nueva aplicación en la nube creada. Todos los dispositivos que ejecutan Update 4.0 o una versión anterior experimentarán [la funcionalidad de administración reducida](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-5.0). 

## <a name="create-a-service"></a>Crear un servicio
Para crear un servicio Administrador de dispositivos de StorSimple, debe tener:

* Una suscripción con un contrato Enterprise
* Una cuenta de Almacenamiento de Microsoft Azure activa.
* La información de facturación que se usa para la administración de acceso

Solo se permiten las suscripciones con un contrato Enterprise. En Azure Portal no se admiten las suscripciones de Microsoft Sponsorship que se permitían en el Portal de Azure clásico. Cuando se usa una suscripción no compatible, verá el siguiente mensaje:

![La suscripción no es válida](./media/storsimple-8000-manage-service/subscription-not-valid.jpg)

También puede generar una cuenta de almacenamiento predeterminada cuando se crea el servicio.

Un único servicio puede administrar varios dispositivos. Sin embargo, un dispositivo no puede abarcar varios servicios. Una gran empresa puede tener varias instancias de servicio para trabajar con distintas suscripciones, organizaciones o incluso las ubicaciones de implementación. 

> [!NOTE]
> Necesita instancias separadas del servicio StorSimple Device Manager para administrar instancias de StorSimple Virtual Array y dispositivos de StorSimple de la serie 8000.

Realice los siguientes pasos para crear un servicio.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Para cada servicio Administrador de dispositivos de StorSimple, existen los siguientes atributos:

* **Nombre**: El nombre asignado al servicio Administrador de dispositivos de StorSimple cuando se creó. **El nombre del servicio no se puede cambiar una vez creado el servicio. Esto también es cierto para otras entidades como dispositivos, volúmenes, contenedores de volúmenes y directivas de copia de seguridad que no se pueden cambiar en Azure Portal.**
* **Estado**: estado del servicio, que puede ser **Activo**, **Creando** o **En línea**.
* **Ubicación** : ubicación geográfica en la que se implementará el dispositivo StorSimple.
* **Suscripción** : suscripción de facturación asociada a su servicio.

## <a name="delete-a-service"></a>Eliminar un servicio

Antes de eliminar un servicio, asegúrese de que no lo esté utilizando ningún dispositivo conectado. Si se está utilizando el servicio, desactive los dispositivos conectados. La operación de desactivación eliminará la conexión entre el dispositivo y el servicio, pero conservará los datos del dispositivo en la nube.

> [!IMPORTANT]
> Después de eliminar un servicio, no se puede revertir la operación. Cualquier dispositivo que usara el servicio debe restablecerse a los valores de fábrica antes de que pueda ser usado con otro servicio. En este escenario, se pierden los datos locales del dispositivo, así como la configuración.

Realice los siguientes pasos para eliminar un servicio.

### <a name="to-delete-a-service"></a>Para eliminar un servicio

1. Busque el servicio que quiere eliminar. Haga clic en el icono **Recursos** y, después, especifique los términos adecuados para buscar. En los resultados de la búsqueda, haga clic en el servicio que quiere eliminar.

    ![Buscar servicio para eliminar](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Esto le llevará a la hoja del servicio Administrador de dispositivos de StorSimple. Hacer clic en **Eliminar**.

    ![Eliminar servicio](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Haga clic en **Sí** en la notificación de confirmación. Es posible que el servicio tarde unos minutos en eliminarse.

    ![Confirmar eliminación](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Obtener la clave de registro del servicio

Después de haber creado correctamente un servicio, deberá registrar el dispositivo StorSimple con el servicio. Para registrar el primer dispositivo StorSimple, necesitará la clave de registro del servicio. Para registrar dispositivos adicionales con un servicio existente de StorSimple, necesita la clave de registro y la clave de cifrado de datos del servicio (que se genera en el primer dispositivo durante el registro). Para obtener más información acerca de la clave de cifrado de datos de servicio, consulte [Seguridad de StorSimple](storsimple-8000-security.md). Puede obtener la clave de registro accediendo a **Claves** en la hoja Administrador de dispositivos de StorSimple.

Realice los pasos siguientes para obtener la clave de registro del servicio.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Mantenga la clave de registro del servicio en una ubicación segura. Necesitará esta clave, así como la clave de cifrado de datos de servicio para registrar dispositivos adicionales con este servicio. Después de obtener la clave de registro del servicio, tiene que configurar el dispositivo a través de la interfaz de Windows PowerShell para StorSimple.

Para obtener más información sobre cómo usar esta clave de registro, consulte [Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Volver a generar la clave de registro de servicio
Tiene que volver a generar una clave de registro del servicio si tiene que realizar la rotación de claves o si la lista de administradores de servicios ha cambiado. Cuando se regenera la clave, la nueva clave se utiliza solo para registrar dispositivos posteriores. Los dispositivos que ya se han registrado no se ven afectados por este proceso.

Realice los pasos siguientes para volver a generar una clave de registro de servicio.

### <a name="to-regenerate-the-service-registration-key"></a>Para volver a generar la clave de registro de servicio
1. En la hoja **StorSimple Device Manager**, vaya a **Administración &gt;** **Claves**.
    
    ![Hoja de claves](./media/storsimple-8000-manage-service/regenregkey2.png)

2. En la hoja **Claves**, haga clic en **Regenerar**.

    ![Haga clic en Regenerar](./media/storsimple-8000-manage-service/regenregkey3.png)
3. En la hoja **Regenerar clave de registro del servicio**, revise la acción necesaria cuando se regeneren las claves. Todos los dispositivos posteriores que están registrados en este servicio usan la nueva clave del registro. Haga clic en **Regenerar** para confirmarlo. Se le notifica una vez completada la regeneración.

    ![Confirmar regeneración](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Aparecerá una nueva clave de registro de servicio.

5. Copie esta clave y guárdela para registrar los dispositivos nuevos con este servicio.



## <a name="change-the-service-data-encryption-key"></a>Cambiar la clave de cifrado de datos de servicio
Las claves de cifrado de datos del servicio se usan para cifrar datos confidenciales de los clientes, como las credenciales de la cuenta de almacenamiento, que se envían desde el servicio StorSimple Manager al dispositivo de StorSimple. Necesitará cambiar estas claves periódicamente si su organización de TI tiene una directiva de rotación de claves en los dispositivos de almacenamiento. El proceso de cambio de clave puede ser ligeramente diferente dependiendo de si el servicio StorSimple Manager administra uno o varios dispositivos. Para más información, vaya a [StorSimple security and data protection](storsimple-8000-security.md) (Protección de datos y seguridad de StorSimple).

El cambio de la clave de cifrado de datos del servicio se realiza en 3 pasos:

1. Mediante scripts de Windows PowerShell para Azure Resource Manager, autorice a un dispositivo cambiar la clave de cifrado de datos del servicio.
2. En Windows PowerShell para StorSimple, inicie el cambio de claves de cifrado de datos del servicio.
3. Si tiene más de un dispositivo de StorSimple, actualice la clave de cifrado de datos del servicio en los demás dispositivos.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Paso 1: Usar un script de Windows PowerShell para autorizar a un dispositivo cambiar la clave de cifrado de datos del servicio
Normalmente, el administrador de dispositivos solicitará que el administrador de servicios autorice que un dispositivo cambie las claves de cifrado de datos del servicio. A continuación, el administrador de servicios autorizará que el dispositivo cambie la clave.

Este paso se realiza con el script basado en Azure Resource Manager. El administrador de servicios puede seleccionar un dispositivo que sea apto para la autorización. A continuación, se autoriza que el dispositivo inicie el proceso de cambio de las claves de cifrado de datos del servicio. 

Para más información sobre el uso del script, vaya a [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>¿Qué dispositivos se pueden autorizar para que cambien las claves de cifrado de datos del servicio?
Para poder autorizar que un dispositivo inicie los cambios de las claves de cifrado de datos del servicio debe cumplir los siguientes criterios:

* Para que sea válido para la autorización de cambio de claves de cifrado de datos del servicio, el dispositivo debe estar en línea.
* Si el cambio de claves no se ha iniciado, es posible autorizar el mismo dispositivo 30 minutos después.
* Se puede autorizar otro dispositivo, siempre que el dispositivo autorizado anteriormente no haya iniciado el cambio de claves. Una vez que se haya autorizado el nuevo dispositivo, el anterior no puede iniciar el cambio.
* No se puede autorizar un dispositivo mientras la sustitución de la clave de cifrado de datos del servicio esté en curso.
* Se puede autorizar un dispositivo cuando algunos de los dispositivos registrados en el servicio hayan sustituido el cifrado, mientras que otros no lo hayan hecho. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Paso 2: usar Windows PowerShell para StorSimple para iniciar el cambio de claves de cifrado de datos del servicio
Este paso se realiza en la interfaz de Windows PowerShell para StorSimple del dispositivo de StorSimple autorizado.

> [!NOTE]
> Hasta que se complete la sustitución de claves no se pueden realizar operaciones en Azure Portal del servicio StorSimple Manager.
> 
> 

Si utiliza la consola serie del dispositivo para conectarse a la interfaz de Windows PowerShell, realice los pasos siguientes.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Para iniciar el cambio de claves de cifrado de datos del servicio
1. Seleccione la opción 1 para iniciar sesión con acceso total.
2. En el símbolo del sistema, escriba:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Una vez que se haya completado correctamente el cmdlet, obtendrá una nueva clave de cifrado de datos del servicio. Copie y guarde esta clave para usarla en el paso 3 de este proceso. Esta clave se utilizará para actualizar todos los dispositivos restantes registrados en el servicio StorSimple Manager.
   
   > [!NOTE]
   > Este proceso debe iniciarse en las cuatro horas siguientes a la autorización de un dispositivo de StorSimple.
   > 
   > 
   
   A continuación, esta nueva clave se envía al servicio de inserción en todos los dispositivos que están registrados en el servicio. Seguidamente, aparecerá una alerta en el panel del servicio. El servicio deshabilitará todas las operaciones en los dispositivos registrados y, a continuación, el administrador de dispositivos tendrá que actualizar la clave de cifrado de datos del servicio en el resto de dispositivos. Sin embargo, las E/S (hosts que envían datos a la nube) no se interrumpirán.
   
   Si tiene un único dispositivo registrado en el servicio, el proceso de sustitución habrá finalizado y puede omitir el paso siguiente. Si tiene varios dispositivos registrados en el servicio, vaya al paso 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Paso 3: actualizar la clave de cifrado de datos del servicio en otros dispositivos de StorSimple
Estos pasos deben realizarse en la interfaz de Windows PowerShell del dispositivo de StorSimple si tiene varios dispositivos registrados en el servicio StorSimple Manager. La clave que obtuvo en el paso 2 se debe usar para actualizar todos los dispositivos de StorSimple restantes registrados con el servicio StorSimple Manager.

Realice los pasos siguientes para actualizar el cifrado de datos del servicio en el dispositivo.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Para actualizar la clave de cifrado de datos del servicio en dispositivos físicos
1. Use Windows PowerShell para StorSimple para conectarse a la consola. Seleccione la opción 1 para iniciar sesión con acceso total.
2. En el símbolo del sistema, escriba: `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Proporcione la clave de cifrado de datos del servicio que obtuvo en [Paso 2: usar Windows PowerShell para StorSimple para iniciar el cambio de claves de cifrado de datos del servicio](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Para actualizar la clave de cifrado de datos del servicio en todas las aplicaciones en la nube 8010/8020
1. Descargue y configure el script de PowerShell [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1). 
2. Abra PowerShell y, en el símbolo del sistema, escriba:`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Este script asegurará de que esa clave de cifrado de datos del servicio se establece en todas las aplicaciones en la nube 8010/8020 en el administrador de dispositivos.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Operaciones admitidas en dispositivos que ejecutan versiones anteriores a Update 5.0
En Azure Portal, solo se admiten los dispositivos de StorSimple que ejecuten Update 5.0 y versiones posteriores. Los dispositivos que ejecutan versiones anteriores tienen compatibilidad limitada. Después de haber migrado a Azure Portal, utilice la siguiente tabla para comprender las operaciones que se admiten en los dispositivos que ejecutan versiones anteriores a Update 5.0.

| Operación                                                                                                                       | Compatible      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registrar un dispositivo                                                                                                               | Sí            |
| Configurar opciones de dispositivo como generales, de red y de seguridad                                                                | Sí            |
| Buscar, descargar e instalar actualizaciones                                                                                             | Sí            |
| Desactivar un dispositivo                                                                                                               | Sí            |
| Eliminar un dispositivo                                                                                                                   | Sí            |
| Crear, modificar y eliminar un contenedor de volúmenes                                                                                   | Sin              |
| Crear, modificar y eliminar un volumen                                                                                             | Sin              |
| Crear, modificar y eliminar una directiva de copia de seguridad                                                                                      | Sin              |
| Creación de una copia de seguridad manual                                                                                                            | Sin              |
| Realizar una copia de seguridad programada                                                                                                         | No aplicable |
| Restaurar desde un conjunto de copias de seguridad                                                                                                        | Sin              |
| Clonar en un dispositivo que ejecuta Update 3.0 y versiones posteriores <br> El dispositivo de origen está ejecutando una versión anterior a Update 3.0.                                | Sí            |
| Clonar en un dispositivo que ejecuta versiones anteriores a Update 3.0                                                                          | Sin              |
| Conmutación por error como dispositivo de origen <br> (desde un dispositivo que ejecuta la versión anterior a Update 3.0 a un dispositivo que ejecuta Update 3.0 y versiones posteriores)                                                               | Sí            |
| Conmutación por error como dispositivo de destino <br> (a un dispositivo que ejecuta una versión de software anterior a Update 3.0)                                                                                   | Sin              |
| Desactivar una alerta                                                                                                                  | Sí            |
| Ver las directivas de copia de seguridad, el catálogo de copias de seguridad, volúmenes, contenedores de volúmenes, gráficos de supervisión, trabajos y alertas creadas en el Portal clásico | Sí            |
| Activar y desactivar los controladores de dispositivo                                                                                              | Sí            |


## <a name="next-steps"></a>pasos siguientes
* Obtenga más información sobre el [proceso de implementación de StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Obtenga más información sobre cómo [administrar su cuenta de almacenamiento de StorSimple](storsimple-8000-manage-storage-accounts.md).
* Obtenga más información acerca de cómo [usar el servicio StorSimple Device Manager para administrar cualquier dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
