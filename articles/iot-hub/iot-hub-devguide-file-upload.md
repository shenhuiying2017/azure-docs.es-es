---
title: "Información sobre la carga de archivos de Azure IoT Hub | Microsoft Docs"
description: "Guía del desarrollador: uso de la característica de carga de archivos de IoT Hub para administrar la carga de archivos desde un dispositivo a un contenedor de blobs de Azure Storage."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a0427925-3e40-4fcd-96c1-2a31d1ddc14b
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 75a6b9bc3ecfe6d6901bb38e312d62333f38daf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="upload-files-with-iot-hub"></a>Carga de archivos con IoT Hub

Tal como se detalla en el artículo [Puntos de conexión de IoT Hub][lnk-endpoints], un dispositivo puede iniciar cargas de archivos mediante el envío de una notificación a través de un punto de conexión accesible desde el dispositivo (**/devices/{deviceId}/files**). Cuando un dispositivo notifica a IoT Hub que se ha completado una carga, este envía un mensaje de notificación de carga a través del punto de conexión accesible desde el servicio (**/messages/servicebound/filenotifications**).

En lugar de servir de intermediario de los mensajes, IoT Hub actúa como distribuidor a una cuenta de Azure Storage asociada. Un dispositivo solicita un token de almacenamiento de IoT Hub que es específico del archivo que el dispositivo quiere cargar. El dispositivo usa el URI de SAS para cargar el archivo en el almacenamiento y, cuando la carga ha finalizado, envía una notificación de finalización a IoT Hub. IoT Hub comprueba que la carga del archivo está completa y agrega después un mensaje de notificación de carga de archivo al nuevo punto de conexión de notificaciones de archivos accesible desde el servicio.

Antes de cargar un archivo en IoT Hub desde un dispositivo, tiene que configurar su centro mediante la [asociación de una cuenta de Azure Storage][lnk-associate-storage] al mismo.

El dispositivo puede entonces [inicializar una carga][lnk-initialize] y, a continuación, [notificar a IoT Hub][lnk-notify] cuando se complete la carga. Opcionalmente, cuando un dispositivo notifica a IoT Hub que la carga está completa, el servicio puede generar un [mensaje de notificación][lnk-service-notification].

### <a name="when-to-use"></a>Cuándo se deben usar

Cargas de archivos, para archivos multimedia y grandes lotes de telemetría cargados por dispositivos conectados de manera intermitente o comprimidos para ahorrar ancho de banda.

Si duda entre el uso de propiedades notificadas, mensajes de dispositivo a nube o carga de archivos, consulte [Device-to-cloud communication guidance][lnk-d2c-guidance] (Guía de comunicación de dispositivo a nube).

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Asociar una cuenta de Azure Storage con IoT Hub

Para utilizar la funcionalidad de carga de archivos, primero debe vincular una cuenta de Azure Storage a IoT Hub. Puede completar esta tarea a través de [Azure Portal][lnk-management-portal] o mediante programación, con las [API de REST del proveedor de recursos de IoT Hub][lnk-resource-provider-apis]. Una vez que ha asociado una cuenta de Azure Storage a IoT Hub, el servicio devuelve un URI de SAS a un dispositivo cuando este inicie una solicitud de carga de archivos.

> [!NOTE]
> Los [SDK de IoT de Azure][lnk-sdks] administran automáticamente la recuperación del URI de SAS: cargan el archivo y notifican a IoT Hub que la carga se ha completado.


## <a name="initialize-a-file-upload"></a>Inicialización de una carga de archivos
IoT Hub tiene un punto de conexión concreto para dispositivos para solicitar un URI de SAS para el almacenamiento para cargar un archivo. Para iniciar el proceso de carga de archivos, el dispositivo envía una solicitud POST a `{iot hub}.azure-devices.net/devices/{deviceId}/files` con el cuerpo JSON siguiente:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub devuelve los datos siguientes, que usa el dispositivo para cargar el archivo:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>En desuso: inicializar una carga de archivo con un comando GET

> [!NOTE]
> Esta sección describe la funcionalidad en desuso para la recepción de un URI de SAS de IoT Hub. Use el método POST que se ha descrito anteriormente.

IoT Hub tiene dos puntos de conexión de REST que permiten la carga de archivos: uno para obtener el URI de SAS para el almacenamiento y el otro para notificar al centro de IoT que la carga se ha completado. El dispositivo inicia el proceso de carga de archivos mediante el envío de una operación GET al centro de IoT en `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. IoT Hub devuelve:

* Un URI de SAS específico del archivo que se va a cargar.
* Un identificador de correlación que se usará cuando se haya completado la carga.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notificación a IoT Hub de una carga de archivos completada

El dispositivo es responsable de cargar el archivo en el almacenamiento mediante los SDK de Azure Storage. Cuando la carga se haya completado, el dispositivo envía una solicitud POST a `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` con el siguiente cuerpo JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

El valor de `isSuccess` es un valor booleano que indica si se cargó correctamente el archivo. El código de estado para `statusCode` es el estado de la carga del archivo al almacenamiento y `statusDescription` corresponde a `statusCode`.

## <a name="reference-topics"></a>Temas de referencia:

Los siguientes temas de referencia proporcionan más información sobre la carga de archivos desde un dispositivo.

## <a name="file-upload-notifications"></a>Notificaciones de carga de archivos

Opcionalmente, cuando el dispositivo notifica a IoT Hub que la carga ha finalizado, el servicio puede generar un mensaje de notificación que contiene el nombre y la ubicación de almacenamiento del archivo.

Como se ha explicado en [Puntos de conexión][lnk-endpoints], IoT Hub entrega notificaciones de carga de archivos a través de un punto de conexión accesible desde el servicio (**/messages/servicebound/fileuploadnotifications**) en forma de mensajes. La semántica de recepción de las notificaciones de carga de archivos es la misma que para los mensajes de nube a dispositivo y tiene el mismo [ciclo de vida del mensaje][lnk-lifecycle]. Cada mensaje recuperado del punto de conexión de notificación de carga de archivos es un registro JSON con las siguientes propiedades:

| Propiedad | Description |
| --- | --- |
| EnqueuedTimeUtc |Marca de tiempo que indica cuándo se creó la notificación. |
| deviceId |**DeviceId** del dispositivo que ha cargado el archivo. |
| BlobUri |URI del archivo cargado. |
| BlobName |Nombre del archivo cargado. |
| LastUpdatedTime |Marca de tiempo que indica cuándo se actualizó por última vez el archivo. |
| BlobSizeInBytes |Tamaño del archivo cargado. |

**Ejemplo**. Este ejemplo muestra el cuerpo de ejemplo de un mensaje de notificación de carga de archivos.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Opciones de configuración de notificaciones de carga de archivos

Cada centro de IoT expone las siguientes opciones de configuración para las notificaciones de carga de archivos:

| Propiedad | Description | Intervalo y valor predeterminado |
| --- | --- | --- |
| **enableFileUploadNotifications** |Controla si las notificaciones de carga de archivos se escriben en el punto de conexión de notificaciones de archivo. |Bool. Valor predeterminado: True. |
| **fileNotifications.ttlAsIso8601** |TTL predeterminado para las notificaciones de carga de archivos. |Intervalo ISO_8601 hasta 48H (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| **fileNotifications.lockDuration** |Duración del bloqueo de la cola de notificaciones de carga de archivos. |De 5 a 300 segundos (5 segundos como mínimo). Valor predeterminado: 60 segundos. |
| **fileNotifications.maxDeliveryCount** |Número máximo de entregas en la cola de notificaciones de carga de archivos. |De 1 a 100. Valor predeterminado: 100. |

## <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la guía del desarrollador de IoT Hub son los siguientes:

* En [Puntos de conexión de IoT Hub][lnk-endpoints], se describen los diferentes puntos de conexión que expone cada centro de IoT Hub para las operaciones en tiempo de ejecución y de administración.
* En [Cuotas y limitación][lnk-quotas], se describen las cuotas y el comportamiento de limitación que se aplican al servicio IoT Hub.
* En [SDK de dispositivo y servicio IoT de Azure][lnk-sdks] se muestran los diversos SDK de lenguaje que puede usar para desarrollar aplicaciones para dispositivo y de servicio que interactúen con IoT Hub.
* En [Lenguaje de consulta de IoT Hub][lnk-query], se describe el lenguaje de consulta que se puede usar para recuperar información de IoT Hub sobre los trabajos y dispositivos gemelos.
* En [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt], se proporciona más información sobre la compatibilidad de IoT Hub con el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a cargar los archivos desde dispositivos con IoT Hub, puede interesarle el siguiente tema de la Guía del desarrollador de IoT Hub:

* [Administrar identidades del dispositivo en IoT Hub][lnk-devguide-identities]
* [Control del acceso a IoT Hub][lnk-devguide-security]
* [Uso de dispositivos gemelos para sincronizar el estado y las configuraciones][lnk-devguide-device-twins]
* [Invocación de un método directo en un dispositivo][lnk-devguide-directmethods]
* [Programación de trabajos en varios dispositivos][lnk-devguide-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede interesarle el siguiente tutorial de IoT Hub:

* [Cómo cargar archivos desde dispositivos a la nube con IoT Hub][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
