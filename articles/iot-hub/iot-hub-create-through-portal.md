---
title: Uso de Azure Portal para crear un centro de IoT | Microsoft Docs
description: "Información general sobre cómo crear y administrar los centros de IoT de Azure a través del portal de Azure"
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
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 53f14e6fe115ed5f96d25b9ec5ab04abe23712d5
ms.openlocfilehash: ee072749e080264b4fa2b6cd0305071ed33b665e


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
Para crear un centro de IoT, debe asignar un nombre al centro. Tenga en cuenta que este nombre debe distinguirlo de todos los centros. La duplicación de centros no está permitida en el back-end, por lo que se recomienda asignar a este centro un nombre lo más único posible.

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
Una unidad del Centro de IoT incluye un número determinado de mensajes por día. El número total de mensajes compatibles para este centro es el número de unidades multiplicado por el número de mensajes al día para ese nivel. Por ejemplo, si desea que el Centro de IoT admita la entrada de 700 000 mensajes, elija dos unidades del nivel de S1.

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
* Haga clic en **Agregar** para abrir una hoja. Aquí puede escribir el nombre de la nueva directiva y los permisos que quiere asociar a esta directiva, tal como se muestra en la siguiente ilustración.
  
    Hay varios permisos que se pueden asociar a estas directivas compartidas. Las dos primeras directivas, **Lectura del Registro** y **Escritura del Registro**, conceden derechos de acceso de lectura y escritura para el almacén de identidades de dispositivo o el registro de identidades. Si selecciona la opción de escritura, se elegirá automáticamente también la opción de lectura.
  
     La directiva **Conexión del servicio** concede permiso para tener acceso a los puntos de conexión en la nube, como el grupo de consumidores de servicios que se conectan al Centro de IoT. La directiva **Conexión del dispositivo** concede permisos para enviar y recibir mensajes en los puntos de conexión del dispositivo del Centro de IoT.
* Haga clic en **Crear** para agregar la directiva recién creada a la lista existente.

![][10]

## <a name="messaging"></a>Mensajería
Haga clic en **Mensajería** para mostrar una lista de propiedades de mensajería para el Centro de IoT que se está modificando. Hay dos tipos principales de propiedades que puede modificar o copiar: **De nube a dispositivo** y **De dispositivo a nube**.

* **Configuración de la nube al dispositivo**: Esta configuración tiene dos opciones secundarias: **TTL de nube a dispositivo** (período de vida) y **Tiempo de retención** para los mensajes. Cuando se crea el Centro de IoT por primera vez, estas dos opciones se crean con un valor predeterminado de una hora. Para ajustar los valores, use los controles deslizantes o escriba los valores.
* **Configuración de dispositivo a nube** : esta configuración tiene varias opciones secundarias, algunas de las cuales se denominan/asignan al crear el Centro de IoT y solo se pueden copiar en otras opciones secundarias personalizables. Esta configuración se muestra en la siguiente sección.

**Particiones**: este valor se establece cuando se crea el Centro de IoT y se puede cambiar en esta configuración.

**Punto de conexión y nombre compatible del Centro de eventos**: Cuando se crea IoT Hub, se crea internamente un Event Hub al que el usuario podría necesitar acceder en determinadas circunstancias. Este nombre y punto de conexión compatible con Event Hub no se pueden personalizar pero están disponible para su uso mediante el botón **Copiar** .

**Tiempo de retención**: establecido en un día de forma predeterminada, pero se puede personalizar con otros valores en la lista desplegable. Este valor es en días para la configuración Dispositivo a nube y no en horas, como en el caso de la configuración Nube a dispositivo.

**Grupos de consumidores**: Grupos de consumidores es una configuración similar a otros sistemas de mensajería que se puede usar para extraer datos de modos específicos a fin de conectar otras aplicaciones o servicios a un Centro de IoT. Cada Centro de IoT se crea con un grupo de consumidores predeterminado. Sin embargo, puede agregar o eliminar grupos de consumidores en los Centros de IoT.

> [!NOTE]
> El grupo de consumidores predeterminado no se puede modificar ni eliminar.
> 
> 

![][11]

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
* [Métricas de uso][lnk-metrics]
* [Supervisión de operaciones][lnk-monitor]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Guía del desarrollador][lnk-devguide]
* [Simulación de un dispositivo con el SDK de puerta de enlace de IoT][lnk-gateway]
* [Protección total de la solución de IoT][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md



<!--HONumber=Nov16_HO5-->


