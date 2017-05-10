---
title: Uso de Azure Portal para crear un centro de IoT | Microsoft Docs
description: "Describe sobre cómo crear, administrar y eliminar los centros de IoT Hub de Azure a través de Azure Portal. Incluye información sobre los niveles de precios, el escalado, la seguridad y la configuración de la mensajería."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: ddf69b7256ae9665363ee0f491a6475c69d6d555
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017


---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Creación de una instancia de IoT Hub mediante Azure Portal
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introducción
En este artículo se describe cómo encontrar el servicio IoT Hub en Azure Portal y cómo crear y administrar centros de IoT.

## <a name="where-to-find-iot-hubs"></a>Dónde encontrar centros de IoT
Existen varios lugares donde encontrar los Centros de IoT.

1. **+ Nuevo**: **IoT Hub de Azure**b es un servicio de IoT que se puede encontrar en la categoría **Internet de las cosas** en **+ Nuevo**, al igual que otros servicios.
2. También se puede acceder a los Centros de IoT mediante Marketplace como servicio destacado en **Internet de las cosas**.

## <a name="create-an-iot-hub"></a>Crear un centro de IoT
Puede crear un IoT Hub con los métodos siguientes:

* Al crear un centro de IoT mediante la opción **+ Nuevo** , aparece la hoja que se muestra en la siguiente captura de pantalla. Los pasos para crear el Centro de IoT con este método y con Marketplace son idénticos.
* Para crear un IoT Hub mediante Marketplace: Al hacer clic en **Crear**, se abre una hoja igual a la hoja anterior de la experiencia **+ Nuevo**. Las secciones siguientes enumeran los diversos pasos para crear un Centro de IoT.

### <a name="choose-the-name-of-the-iot-hub"></a>Elección del nombre del Centro de IoT
Para crear un centro de IoT, debe asignar un nombre al centro. Tenga en cuenta que este nombre debe distinguirlo de todos los centros. La duplicación de centros no está permitida en el back-end de solución, por lo que se recomienda asignar a este centro un nombre lo más único posible.

### <a name="choose-the-pricing-tier"></a>Elección del plan de tarifa
Puede elegir entre cuatro niveles: **Gratis**, **Estándar 1**, **Estándar 2** y **Estándar S3**. El nivel Gratis permite solo la conexión de 500 dispositivos con el Centro de IoT y hasta 8000 mensajes al día.

**Estándar S1**: la edición S1 de Centro de IoT está diseñada para las soluciones de IoT que tienen un gran número de dispositivos que generan cantidades de datos relativamente pequeñas por cada dispositivo. Cada unidad de la edición S1 permite transmitir hasta 400.000 mensajes por día a través de todos los dispositivos conectados.

**Estándar S2**: la edición S2 de Centro de IoT está diseñada para las soluciones de IoT en las que los dispositivos generan grandes cantidades de datos. Cada unidad de la edición S2 permite transmitir hasta 6 millones de mensajes al día entre todos los dispositivos conectados.

**Estándar S3**: la edición S3 del Centro de IoT se ha diseñado para las soluciones de IoT que generan grandes cantidades de datos. Cada unidad de la edición S3 permite transmitir hasta 300 millones de mensajes al día entre todos los dispositivos conectados.

![][4]

> [!NOTE]
> IoT Hub solo permite un único centro gratuito por suscripción de Azure.
> 
> 

### <a name="iot-hub-units"></a>Unidades del Centro de IoT
El número de mensajes que se permiten por unidad al día depende del plan de tarifa del centro. Por ejemplo, si desea que el Centro de IoT admita la entrada de 700 000 mensajes, elija dos unidades del nivel de S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Dispositivo para particiones en la nube y grupo de recursos
Puede cambiar el número de particiones para un centro de IoT. Las particiones predeterminadas están establecidas en cuatro; sin embargo, puede elegir un número diferente de particiones en una lista desplegable.

Para los grupos de recursos, no es necesario crear explícitamente un grupo de recursos vacío. Al crear un recurso, puede elegir entre crear un nuevo grupo de recursos o usar un grupo de recursos existente.

![][5]

### <a name="choose-subscriptions"></a>Selección de suscripciones
IoT Hub de Azure muestra automáticamente la lista de suscripciones a la que está vinculada la cuenta de usuario. Puede elegir una de estas opciones para asociar el IoT Hub a esa suscripción de Azure.

### <a name="choose-the-location"></a>Selección de la ubicación
La opción de ubicación proporciona una lista de las regiones en las que se ofrece Centro de IoT. Centro de IoT está disponible para su implementación en las siguientes ubicaciones: este de Australia, sudeste de Australia, este de Asia, sudeste de Asia, Norte de Europa, Oeste de Europa, Japón Oriental, Japón Occidental, Este de EE. UU., Oeste de EE. UU.

### <a name="create-the-iot-hub"></a>Creación del Centro de IoT
Cuando se completen todos los pasos anteriores, el centro de IoT estará listo para crearse. Haga clic en **Crear** para iniciar el proceso back-end de creación de este centro de IoT con las opciones específicas e implementarlo en la ubicación especificada.

La creación de IoT Hub puede tardar unos minutos, ya que se necesita tiempo para que se produzca la implementación back-end en los servidores de ubicación adecuados.

## <a name="change-the-settings-of-the-iot-hub"></a>Cambio de la configuración del Centro de IoT
Puede cambiar la configuración de un Centro de IoT después de crearlo desde la hoja Centro de IoT.

![][8]

**Directivas de acceso compartido**: estas directivas definen los permisos para que los dispositivos y servicios se conecten a IoT Hub. Para acceder a estas directivas, haga clic en **Directivas de acceso compartido** en **General**. En esta hoja puede modificar las directivas existentes o agregar una nueva.

### <a name="create-a-policy"></a>Para crear una directiva
* Haga clic en **Agregar** para abrir una hoja. Aquí puede escribir el nombre de la nueva directiva y los permisos que quiere asociar a esta directiva, tal como se muestra en la siguiente ilustración:
  
    Hay varios permisos que se pueden asociar a estas directivas compartidas. Las dos primeras directivas, **Lectura del Registro** y **Escritura del Registro**, conceden derechos de acceso de lectura y escritura para el almacén de identidades de dispositivo o el registro de identidades. Si selecciona la opción de escritura, se elegirá automáticamente también la opción de lectura.
  
     La directiva **Conexión del servicio** concede permiso para tener acceso a los puntos de conexión en la nube, como el grupo de consumidores de servicios que se conectan al Centro de IoT. La directiva **Conexión del dispositivo** concede permisos para enviar y recibir mensajes en los puntos de conexión del dispositivo del Centro de IoT.
* Haga clic en **Crear** para agregar la directiva recién creada a la lista existente.

![][10]

## <a name="endpoints"></a>Puntos de conexión
Haga clic en **Endpoints** (Puntos de conexión) para mostrar una lista de puntos de conexión para el centro de IoT que está modificando. Hay dos tipos de puntos de conexión: los integrados en el centro de IoT y los que agrega al centro de IoT después de crearlo.

![][11]

### <a name="built-in-endpoints"></a>Puntos de conexión integrados
Hay dos puntos de conexión integrados: **Cloud to device feedback** (Comentarios de la nube al dispositivo) y **Events** (Eventos).

* Configuración de **Cloud to device feedback** (Comentarios de la nube al dispositivo): esta configuración tiene dos opciones secundarias: **Cloud to Device TTL** (TTL de nube a dispositivo) (período de vida) y **Retention time** (Tiempo de retención) (en horas) para los mensajes. Al crear por primera vez un centro de IoT, ambas configuraciones tienen el valor predeterminado de una hora. Para ajustar la configuración, use los controles deslizantes o escriba los valores.
* Configuración de **Events** (Eventos): esta configuración tiene distintas opciones secundarias, algunas de solo lectura. Estas opciones secundarias se describen en la lista siguiente:

    * **Partitions** (Particiones): se establece un valor predeterminado cuando se crea el centro de IoT. Esta opción permite cambiar el número de particiones.

    * **Punto de conexión y nombre compatible del Centro de eventos**: Cuando se crea IoT Hub, se crea internamente un Event Hub al que el usuario podría necesitar acceder en determinadas circunstancias. No se pueden personalizar los valores de nombre y punto de conexión compatibles con el centro de eventos, pero los puede copiar al hacer clic en **Copy** (Copiar).

    * **Retention Time** (Tiempo de retención): se establece en un día de forma predeterminada, pero se puede cambiar gracias a la lista desplegable. Este valor está en días para la configuración del dispositivo a la nube.

    * **Consumer Groups** (Grupos de consumidores): es una configuración similar a otros sistemas de mensajería que se puede usar para extraer datos de modos específicos a fin de conectar otras aplicaciones o servicios a IoT Hub. Cada Centro de IoT se crea con un grupo de consumidores predeterminado. Sin embargo, con esta configuración puede agregar o eliminar grupos de consumidores en los centros de IoT.

    > [!NOTE]
    > El grupo de consumidores predeterminado no se puede modificar ni eliminar.
    > 
    > 

### <a name="custom-endpoints"></a>Puntos de conexión personalizados
Puede agregar puntos de conexión personalizados al centro de IoT a través del portal. En la hoja **Endpoints** (Puntos de conexión), haga clic en **Add** (Agregar) de la parte superior para abrir la hoja **Add endpoint** (Agregar punto de conexión). Escriba la información necesaria y haga clic en **OK** (Aceptar). El punto de conexión personalizado aparecerá ahora en la hoja principal de **Endpoints** (Puntos de conexión).

![][13]

Puede leer más sobre los puntos de conexión personalizados en [Referencia: Puntos de conexión de IoT Hub][lnk-devguide-endpoints].

## <a name="routes"></a>Rutas
Haga clic en **Routes** (Rutas) para administrar el envío de mensajes del dispositivo a la nube de IoT Hub.

![][14]

Puede agregar rutas adicionales a su IoT Hub; para ello, haga clic en **Agregar** en la parte superior de la hoja **Rutas**, escriba la información necesaria y haga clic en **Aceptar**. La ruta aparecerá en la lista de la hoja principal de **Rutas**. Puede editar una ruta al hacer clic en ella en la lista de rutas. Para habilitar una ruta, haga clic en ella en la lista de rutas y establezca el control de alternancia de **Habilitar** en **Desactivado**. Haga clic en **Aceptar** en la parte inferior de la hoja para guardar los cambios.

![][15]

## <a name="pricing-and-scale"></a>Precios y escala
El precio de un Centro de IoT existente se puede cambiar mediante la configuración **Precios** , con las siguientes excepciones:

* En la implementación actual, un Centro de IoT con una SKU gratuita no puede cambiar de nivel a una de las SKU de pago o viceversa.
* Solo puede haber un único IoT Hub de nivel Gratis en la suscripción de Azure.

![][12]

Ir de un nivel alto (S2 o S3) a un nivel bajo (S1 o S2) solo está permitido cuando el número de mensajes enviados durante ese día no está en conflicto. Por ejemplo, si el número de mensajes por día supera los 400 000, se puede cambiar el nivel para IoT Hub. Sin embargo, si cambia al nivel S1, el centro de IoT está limitado durante ese día.

## <a name="delete-the-iot-hub"></a>Eliminación del Centro de IoT
Puede examinar el Centro de IoT que desea eliminar haciendo clic en **Examinar**y, a continuación, seleccionar el centro adecuado que desea eliminar. Haga clic en el botón **Eliminar** debajo del nombre del centro de IoT para eliminar el centro.

## <a name="next-steps"></a>Pasos siguientes
Siga estos vínculos para más información sobre la administración del Centro de IoT de Azure:

* [Administración masiva de dispositivos de IoT][lnk-bulk]
* [Métricas de IoT Hub][lnk-metrics]
* [Supervisión de operaciones][lnk-monitor]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Simulación de un dispositivo con el SDK de puerta de enlace de IoT][lnk-gateway]
* [Protección total de la solución de IoT][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md

