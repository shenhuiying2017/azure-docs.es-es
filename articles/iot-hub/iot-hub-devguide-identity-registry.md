---
title: "Información del registro de identidades de Azure IoT Hub | Microsoft Docs"
description: "Guía del desarrollador: descripción del registro de identidades de IoT Hub y cómo usarlo para administrar los dispositivos Incluye información sobre la importación y exportación de identidades de dispositivos de forma masiva."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 653c31fb1115c79216f882a52484cd37303e0322
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Descripción del registro de identidades de un centro de IoT

Cada centro de IoT tiene un registro de identidades que almacena información acerca de los dispositivos que pueden conectarse a IoT Hub. Antes de que un dispositivo pueda conectarse a un centro de IoT, debe haber una entrada para ese dispositivo en el registro de identidades del centro de IoT. También se debe autenticar un dispositivo en el centro de IoT basándose en las credenciales almacenadas en el registro de identidades.

El identificador de dispositivo que se almacena en el registro de identidad distingue mayúsculas de minúsculas.

En un nivel superior, el registro de identidad es un conjunto de recursos de identidad de dispositivos compatible con REST. Cuando agrega una entrada al registro de identidades, IoT Hub crea un conjunto de recursos por dispositivo, como una cola que contiene mensajes de nube al dispositivo en curso.

Utilice el registro de identidad cuando necesite:

* Aprovisionar los dispositivos que se conectan a la instancia de IoT Hub.
* Controlar el acceso por dispositivo a los puntos de conexión accesibles desde los dispositivos de la instancia de IoT Hub.

> [!NOTE]
> El registro de identidad no contiene metadatos específicos de la aplicación.

## <a name="identity-registry-operations"></a>Operaciones de registro de identidad

El registro de identidades de IoT Hub muestra las operaciones siguientes:

* Crear la identidad del dispositivo
* Actualizar la identidad del dispositivo
* Recuperar la identidad del dispositivo mediante el identificador
* Eliminar la identidad del dispositivo
* Enumerar hasta 1.000 identidades
* Exportar todas las identidades a Azure Blob Storage
* Importar todas las identidades desde Azure Blob Storage

Todas estas operaciones pueden usar la simultaneidad optimista, tal como se especifica en [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> La única manera de recuperar todas las identidades en un registro de identidades de un centro de IoT es usar la funcionalidad [Exportar][lnk-export].

Un registro de identidades de un centro de IoT:

* No contiene los metadatos de la aplicación.
* Es accesible como un diccionario utilizando **deviceId** como clave.
* No admite consultas expresivas.

Una solución de IoT normalmente tiene un almacén independiente específico de la solución que contiene los metadatos específicos de la aplicación. Por ejemplo, el almacén específico de la solución en una solución de un edificio inteligente puede registrar la sala en la que se implementa un sensor de temperatura.

> [!IMPORTANT]
> Solo se debe utilizar el registro de identidad para las operaciones de aprovisionamiento y administración de dispositivos. Las operaciones de alto rendimiento en tiempo de ejecución no deben depender de la realización de operaciones en el registro de identidad. Por ejemplo, comprobar el estado de conexión de un dispositivo antes de enviar un comando no es un modelo compatible. Asegúrese de comprobar las [tasas de limitación][lnk-quotas] para el registro de identidad y el patrón de [latido de dispositivo][lnk-guidance-heartbeat].

## <a name="disable-devices"></a>Deshabilitar dispositivos

Puede deshabilitar los dispositivos actualizando la propiedad **status** de una identidad en el registro de identidad. Normalmente, esta propiedad se usa en dos escenarios:

* Durante el proceso de orquestación de aprovisionamiento. Para más información, consulte [Aprovisionamiento de dispositivos][lnk-guidance-provisioning].
* Si por cualquier motivo cree que un dispositivo está en peligro o no está autorizado.

## <a name="import-and-export-device-identities"></a>Importar y exportar identidades de dispositivo

Puede exportar identidades de dispositivo de forma masiva desde el registro de identidades de un centro de IoT mediante operaciones asincrónicas en el [punto de conexión del proveedor de recursos de IoT Hub][lnk-endpoints]. Las exportaciones son trabajos de ejecución prolongada que usan un contenedor de blobs proporcionado por el cliente para guardar los datos de identidad del dispositivo que se leen en el registro de identidad.

Puede importar identidades de dispositivo de forma masiva a un registro de identidades de un centro de IoT mediante operaciones asincrónicas en el [punto de conexión del proveedor de recursos de IoT Hub][lnk-endpoints]. Las importaciones son trabajos de ejecución prolongada que usan los datos de un contenedor de blobs proporcionado por el cliente para escribir datos de identidad del dispositivo en el registro de identidad.

Para más información sobre la importación y exportación de API, consulte [API de REST del proveedor de recursos de IoT Hub][lnk-resource-provider-apis]. Para más información sobre la ejecución de trabajos de importación y exportación, consulte [Administración de identidades de dispositivos de IoT Hub de forma masiva][lnk-bulk-identity].

## <a name="device-provisioning"></a>Aprovisionamiento de dispositivos

Los datos del dispositivo que almacena una solución de IoT determinada dependen de los requisitos específicos de la solución. Sin embargo, como mínimo, una solución debe almacenar identidades del dispositivo y claves de autenticación. El IoT Hub incluye un registro de identidades que puede almacenar valores para cada dispositivo como identificadores, claves de autenticación y códigos de estado. Una posible solución consiste en usar otros servicios de Azure como Table Storage, Blob Storage o Cosmos DB para almacenar datos de dispositivos adicionales.

*Aprovisionamiento de dispositivos* es el proceso de agregar los datos iniciales del dispositivo a los almacenes de la solución. Para habilitar un nuevo dispositivo y que se conecte al centro, debe agregar un nuevo identificador de dispositivo y claves al registro de identidades del centro de IoT. Como parte del proceso de aprovisionamiento, es posible que tenga que inicializar datos específicos del dispositivo en otros almacenes de la solución. También puede usar el servicio Azure IoT Hub Device Provisioning para habilitar el aprovisionamiento Just-In-Time sin intervención del usuario de uno o varios centros de IoT sin necesidad de ninguna intervención humana. Para más información, vea la [documentación del servicio de aprovisionamiento][lnk-dps].

## <a name="device-heartbeat"></a>Latido de dispositivo

El registro de identidades de IoT Hub contiene un campo llamado **connectionState**. Utilice únicamente el campo **connectionState** durante el desarrollo y la depuración. Las soluciones de IoT no deben consultar el campo en tiempo de ejecución. Por ejemplo, no hay que consultar el campo **connectionState** para comprobar si el dispositivo está conectado antes de enviar un mensaje de la nube al dispositivo o un SMS.

Si la solución de IoT necesita saber si un dispositivo está conectado, debe implementar el *patrón de latido*.

En el patrón de latido, el dispositivo envía mensajes de dispositivo a la nube al menos una vez en un período de tiempo predeterminado (por ejemplo, al menos una vez cada hora). Por lo tanto, incluso si un dispositivo no tiene ningún dato que enviar, seguirá enviando un mensaje de dispositivo a la nube vacío (normalmente con una propiedad que lo identifica como un latido). En el lado del servicio, la solución mantiene un mapa con el último latido recibido para cada dispositivo. Si solución si no recibe del dispositivo un mensaje de latido en el tiempo esperado, da por supuesto que hay un problema con un dispositivo.

Una implementación más compleja podría incluir la información de [supervisión de operaciones][lnk-devguide-opmon] para identificar los dispositivos que están intentando conectarse o comunicarse sin éxito. Al implementar el patrón de latido, asegúrese de echar un vistazo a las [cuotas y limitaciones de IoT Hub][lnk-quotas].

> [!NOTE]
> Si una solución de IoT utiliza el estado de conexión de los dispositivos únicamente para determinar si enviar mensajes de la nube a los dispositivos y los mensajes no se difunden a grandes conjuntos de dispositivos, considere usar el patrón más sencillo de un *tiempo de expiración breve*. Con este patrón se consigue el mismo resultado que con el mantenimiento de un registro del estado de la conexión de los dispositivos con el patrón de latido, a la vez que resulta más eficiente. Cuando solicita confirmaciones de mensajes, IoT Hub puede notificarle sobre qué dispositivos pueden recibir mensajes y cuáles no.

## <a name="device-lifecycle-notifications"></a>Notificaciones de ciclo de vida de dispositivo

IoT Hub puede notificar la solución de IoT al crearse o eliminarse la identidad de un dispositivo mediante el envío de notificaciones de ciclo de vida de dispositivo. Para ello, la solución de IoT debe crear una ruta y establecer el origen de datos igual a *DeviceLifecycleEvents*. De forma predeterminada, no se envían notificaciones de ciclo de vida, es decir, no existen previamente tales rutas. El mensaje de notificaciones incluye propiedades y el cuerpo.

Propiedades: las propiedades del sistema de mensajes tienen como prefijo el símbolo `'$'`.

| NOMBRE | Valor |
| --- | --- |
$content-type | application/json |
$iothub-enqueuedtime |  Hora de envío de la notificación |
$iothub-message-source | deviceLifecycleEvents |
$content-encoding | utf-8 |
opType | **createDeviceIdentity** o **deleteDeviceIdentity** |
hubName | Nombre de IoT Hub |
deviceId | Id. del dispositivo |
operationTimestamp | Marca de tiempo ISO8601 de operación |
iothub-message-schema | deviceLifecycleNotification |

Cuerpo: esta sección está en formato JSON y representa el dispositivo gemelo de la identidad del dispositivo creada. Por ejemplo,

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Propiedades de identidad del dispositivo

Las identidades de dispositivos se representan como documentos JSON con las propiedades siguientes:

| Propiedad | Opciones | DESCRIPCIÓN |
| --- | --- | --- |
| deviceId |necesarias, de solo lectura en actualizaciones |Una cadena que distingue entre mayúsculas y minúsculas (de hasta 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits más determinados caracteres especiales: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| generationId |requerido, de solo lectura |Una cadena de hasta 128 caracteres que distingue mayúsculas y minúsculas generada por el centro de IoT. Este valor se usa para distinguir dispositivos con el mismo **deviceId**, cuando se han eliminado y vuelto a crear. |
| ETag |requerido, de solo lectura |Una cadena que representa un valor de ETag débil para la identidad del dispositivo, como [RFC7232][lnk-rfc7232]. |
| auth |opcional |Un objeto compuesto que contiene material de seguridad e información de autenticación. |
| auth.symkey |opcional |Un objeto compuesto que contiene una clave principal y una secundaria, almacenadas en formato base64. |
| status |requerido |Indicador de acceso Puede ser **Habilitado** o **Deshabilitado**. Si está **Habilitado**, el dispositivo se puede conectar. Si está **Dishabilitado**, este dispositivo no puede obtener acceso a ningún punto de conexión accesible desde el dispositivo. |
| statusReason |opcional |Una cadena de 128 caracteres que almacena el motivo del estado de identidad del dispositivo. Se permiten todos los caracteres UTF-8. |
| statusUpdateTime |solo lectura |Un indicador temporal, que muestra la fecha y hora de la última actualización de estado. |
| connectionState |solo lectura |Un campo que indica el estado de la conexión: **Conectado** o **Desconectado**. Este campo representa la vista de IoT Hub del estado de conexión del dispositivo. **Importante**: Este campo debe usarse solo para fines de desarrollo o depuración. El estado de conexión se actualiza solo para dispositivos que usen AMQP o MQTT. Además, se basa en pings de nivel de protocolo (pings MQTT o pings AMQP) y puede tener un retraso de 5 minutos como máximo. Por estos motivos es posible que haya falsos positivos, como dispositivos que se notifican como conectados pero que están desconectados. |
| connectionStateUpdatedTime |solo lectura |Un indicador temporal que muestra la fecha y hora de la última vez que se actualizó el estado de conexión. |
| lastActivityTime |solo lectura |Un indicador temporal que muestra la fecha y hora de la última vez que el dispositivo se conectó, recibió o envió un mensaje. |

> [!NOTE]
> El estado de la conexión solo puede representar la vista del IoT Hub del estado de la conexión. Las actualizaciones de este estado se pueden retrasar, dependiendo de las configuraciones y las condiciones de red.

## <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la guía del desarrollador de IoT Hub son los siguientes:

* En [Puntos de conexión de IoT Hub][lnk-endpoints], se describen los diferentes puntos de conexión que expone cada centro de IoT Hub para las operaciones en tiempo de ejecución y de administración.
* En [Cuotas y limitación][lnk-quotas], se describen las cuotas y el comportamiento de limitación que se aplican al servicio IoT Hub.
* En [SDK de dispositivo y servicio IoT de Azure][lnk-sdks] se muestran los diversos SDK de lenguaje que puede usar para desarrollar aplicaciones para dispositivo y de servicio que interactúen con IoT Hub.
* En [Lenguaje de consulta de IoT Hub][lnk-query], se describe el lenguaje de consulta que se puede usar para recuperar información de IoT Hub sobre los trabajos y dispositivos gemelos.
* En [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt], se proporciona más información sobre la compatibilidad de IoT Hub con el protocolo MQTT.

## <a name="next-steps"></a>pasos siguientes

Ahora que ha aprendido a usar el registro de identidad de IoT Hub, pueden interesarle los siguientes temas de la Guía para desarrolladores de IoT Hub:

* [Control del acceso a IoT Hub][lnk-devguide-security]
* [Uso de dispositivos gemelos para sincronizar el estado y las configuraciones][lnk-devguide-device-twins]
* [Invocación de un método directo en un dispositivo][lnk-devguide-directmethods]
* [Programación de trabajos en varios dispositivos][lnk-devguide-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede interesarle el siguiente tutorial de IoT Hub:

* [Introducción a Azure IoT Hub][lnk-getstarted-tutorial]

Para explorar el uso del servicio IoT Hub Device Provisioning para habilitar el aprovisionamiento Just-In-Time sin intervención del usuario, vea: 

* [Servicio Azure IoT Hub Device Provisioning][lnk-dps]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
