---
title: Esquema de Azure Event Grid para IoT Hub | Microsoft Docs
description: "Página de referencia sobre el formato y las propiedades del esquema de eventos para IoT Hub"
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 29ad1233a344c3085286c27cb925b2dc9fb41f7e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Esquema de eventos de Azure Event Grid para IoT Hub

En este artículo se proporcionan las propiedades y los esquemas de los eventos de Azure IoT Hub. Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md). 

## <a name="available-event-types"></a>Tipos de eventos disponibles

Azure IoT Hub emite los siguientes tipos de eventos:

| Tipo de evento | DESCRIPCIÓN |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Se publica cuando se registra un dispositivo en una instancia de IoT Hub. |
| Microsoft.Devices.DeviceDeleted | Se publica cuando se elimina un dispositivo de una instancia de IoT Hub. | 

## <a name="example-event"></a>Evento de ejemplo

Los esquemas de los eventos DeviceCreated y DeviceDeleted tienen la misma estructura. Este evento de ejemplo muestra el esquema de un evento que se genera cuando se registra un dispositivo en una instancia de IoT Hub:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Propiedades de evento

Todos los eventos contienen los mismos datos de nivel superior: 

| Propiedad | type | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| id | cadena | Identificador único para el evento |
| topic | cadena | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor. |
| subject | cadena | Ruta al asunto del evento definida por el anunciante. |
| eventType | cadena | Uno de los tipos de eventos registrados para este origen de eventos. |
| eventTime | cadena | La hora de generación del evento en función de la hora UTC del proveedor. |
| data | objeto | Datos del evento de IoT Hub.  |
| dataVersion | cadena | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| metadataVersion | cadena | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |

El contenido del objeto de datos es diferente para cada publicador de eventos. Para los eventos de IoT Hub, el objeto de datos contiene las siguientes propiedades:

| Propiedad | type | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| hubName | cadena | Nombre de la instancia de IoT Hub en que se creó o eliminó el dispositivo. |
| deviceId | cadena | Identificador único del dispositivo. Una cadena que distingue mayúsculas de minúsculas puede tener una longitud de hasta 128 caracteres alfanuméricos ASCII de 7 bits más los siguientes caracteres especiales: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| operationTimestamp | cadena | La marca de tiempo ISO8601 de la operación. |
| opType | cadena | El tipo de evento especificado para esta operación en IoT Hub: ya sea `DeviceCreated` o `DeviceDeleted`.
| twin | objeto | Información sobre el dispositivo gemelo, que es la representación en la nube de los metadatos del dispositivo de la aplicación. | 
| deviceID | cadena | Identificador único del dispositivo gemelo. | 
| ETag | cadena | Un fragmento de información que describe el contenido del dispositivo gemelo. Se garantiza que cada etiqueta de entidad sea exclusiva de cada dispositivo gemelo. | 
| status | cadena | Si el dispositivo gemelo está habilitado o deshabilitado. | 
| statusUpdateTime | cadena | La marca de tiempo ISO8601 de la última actualización de estado del dispositivo gemelo. |
| connectionState | cadena | Si el dispositivo está conectado o desconectado. | 
| lastActivityTime | cadena | La marca de tiempo ISO8601 de la última actividad. | 
| cloudToDeviceMessageCount | integer | Número de mensajes de la nube al dispositivo enviados a este dispositivo. | 
| authenticationType | cadena | Tipo de autenticación usado para este dispositivo: `SAS`, `SelfSigned`o `CertificateAuthority`. |
| x509Thumbprint | cadena | La huella digital es un valor exclusivo del certificado x509, que suele usarse para buscar un certificado concreto en un almacén de certificados. La huella digital se genera de forma dinámica con el algoritmo SHA1 y no existe físicamente en el certificado. | 
| primaryThumbprint | cadena | Huella digital principal del certificado x509. |
| secondaryThumbprint | cadena | Huella digital secundaria del certificado x509. | 
| version | integer | Un entero que se incrementa en uno cada vez que se actualiza el dispositivo gemelo. |
| desired | objeto | Una parte de las propiedades que solo el back-end de la aplicación puede escribir y que el dispositivo puede leer. | 
| reported | objeto | Una parte de las propiedades que solo el dispositivo puede escribir y que el back-end de la aplicación puede leer. |
| lastUpdated | cadena | La marca de tiempo ISO8601 de la última actualización de propiedades del dispositivo gemelo. | 

## <a name="next-steps"></a>pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).
* Para más información sobre cómo IoT Hub y Event Grid funcionan de forma conjunta, vea [Reacción a eventos de IoT Hub mediante Event Grid para desencadenar acciones](../iot-hub/iot-hub-event-grid.md).