---
title: "Metadatos de información de dispositivo en la solución de supervisión remota | Microsoft Docs"
description: "Una descripción de la supervisión remota de la solución preconfigurada de IoT de Azure y su arquitectura."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: f8fd452806a0a0b98cf8e434c9bd55700083a6c5
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Metadatos de información de dispositivo en la solución preconfigurada de supervisión remota

La solución preconfigurada de supervisión remota del Conjunto de aplicaciones de IoT de Azure muestra un enfoque para administrar los metadatos de dispositivo. En este artículo se describe el enfoque que adopta esta solución para que comprenda:

* Qué metadatos de dispositivo almacena la solución.
* Cómo la solución administra los metadatos de dispositivo.

## <a name="context"></a>Context

La solución preconfigurada de supervisión remota usa [Azure IoT Hub][lnk-iot-hub] para que los dispositivos puedan enviar datos a la nube. La solución almacena información acerca de los dispositivos en tres ubicaciones diferentes:

| La ubicación | Información almacenada | Implementación |
| -------- | ------------------ | -------------- |
| Registro de identidad | Identificador de dispositivo, claves de autenticación, estado habilitado | Integrado en IoT Hub |
| Dispositivos gemelos | Metadatos: propiedades notificadas, propiedades deseadas, etiquetas | Integrado en IoT Hub |
| Cosmos DB | Historial de comandos y métodos | Personalizado para la solución |

IoT Hub incluye un [registro de identidad de dispositivo][lnk-identity-registry] para administrar el acceso a una instancia de IoT Hub y usa [dispositivos gemelos][lnk-device-twin] para administrar los metadatos del dispositivo. También hay un *registro de dispositivo* específico de la solución de supervisión remota que almacena el historial de comandos y métodos. La solución de supervisión remota usa una base de datos de [Cosmos DB][lnk-docdb] para implementar un almacén personalizado para el historial de comandos y métodos.

> [!NOTE]
> La solución preconfigurada de supervisión remota mantiene sincronizado el registro de identidad del dispositivo con la información de la base de datos de Cosmos DB. Ambos utilizan el mismo id. de dispositivo para identificar de forma única cada dispositivo conectado a su centro de IoT.

## <a name="device-metadata"></a>Metadatos del dispositivo

IoT Hub mantiene un [dispositivo gemelo][lnk-device-twin] para cada dispositivo simulado y físico conectado a una solución de supervisión remota. La solución utiliza dispositivos gemelos para administrar los metadatos asociados con los dispositivos. Un dispositivo gemelo es un documento JSON mantenido por IoT Hub. La solución utiliza la API de IoT Hub para interactuar con los dispositivos gemelos.

Un dispositivo gemelo almacena tres tipos de metadatos:

- Un dispositivo envía las *propiedades notificadas* a una instancia de IoT Hub. En la solución de supervisión remota, los dispositivos simulados envían las propiedades notificadas durante el inicio y en respuesta a comandos y métodos para **cambiar el estado del dispositivo**. Puede ver las propiedades notificadas en la **lista de dispositivos** y en **Detalles del dispositivo** en el portal de la solución. Las propiedades notificadas son de solo lectura.
- Los dispositivos recuperan *Las propiedades deseadas* desde IoT Hub. Es responsabilidad del dispositivo realizar cualquier cambio de configuración necesario en el mismo. También es responsabilidad del dispositivo notificar el cambio al concentrador como una propiedad notificada. Puede establecer un valor de propiedad deseada través del portal de la solución.
- Las *etiquetas* solo existen en el dispositivo gemelo y nunca se sincronizan con un dispositivo. Puede establecer los valores de etiqueta en el portal de la solución y usarlos al filtrar la lista de dispositivos. La solución también utiliza una etiqueta para identificar el icono para mostrar de un dispositivo en el portal de la solución.

Las propiedades notificadas de ejemplo de los dispositivos simulados incluyen fabricante, número de modelo, latitud y longitud. Los dispositivos simulados también devuelven la lista de métodos admitidos como una propiedad notificada.

> [!NOTE]
> El código del dispositivo simulado solo usa las propiedades deseadas **Desired.Config.TemperatureMeanValue** y **Desired.Config.TelemetryInterval** para actualizar las propiedades notificadas devueltas a IoT Hub. Todos los demás cambios de propiedades deseadas se omiten.

Un documento JSON de metadatos de información de dispositivo almacenado en la base de datos de Cosmos DB del Registro de dispositivos tiene la siguiente estructura:

```json
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

> [!NOTE]
> La información de dispositivo también puede incluir metadatos para describir la telemetría que envía el dispositivo al Centro de IoT. La solución de supervisión remota utiliza estos metadatos de telemetría para personalizar cómo se muestra en el panel la [telemetría dinámica][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Ciclo de vida

Cuando se crea por primera vez un dispositivo en el portal de la solución, esta crea una entrada en la base de datos de Cosmos DB para almacenar el historial de comandos y métodos. En este punto, la solución también crea una entrada para el dispositivo en el Registro de identidad del dispositivo que genera las claves que usa el dispositivo para autenticarse en IoT Hub. También crea un dispositivo gemelo.

Cuando un dispositivo se conecta por primera vez a la solución, envía las propiedades notificadas y un mensaje de información de dispositivo. Los valores de las propiedades notificadas se guardan automáticamente en el dispositivo gemelo. Las propiedades notificadas incluyen el fabricante del dispositivo, el número de modelo, el número de serie y una lista de los métodos admitidos. El mensaje de información del dispositivo incluye la lista de los comandos que admite el dispositivo e información sobre los parámetros de comando. Cuando la solución recibe este mensaje, actualiza la información de dispositivo en la base de datos de Cosmos DB.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Visualización y edición de la información de dispositivo en el portal de solución

En la lista de dispositivos del portal de la solución, aparecen, de forma predeterminada, las siguientes propiedades de dispositivo en forma de columnas: **Estado**, **Id. de dispositivo**, **Fabricante**, **Número de modelo**, **Número de serie**, **Firmware**, **Plataforma**, **Procesador** y **RAM instalada**. Puede personalizar las columnas haciendo clic en **Editor de columnas**. Las propiedades **Latitud** y **Longitud** controlan la ubicación en el mapa de Bing incluido en el panel.

![Editor de columnas en la lista de dispositivos][img-device-list]

En el panel **Detalles del dispositivo** del portal de la solución, puede editar las propiedades deseadas y las etiquetas (las propiedades notificadas son de solo lectura).

![Panel de detalles del dispositivo][img-device-edit]

Puede usar el portal de solución para quitar un dispositivo de la solución. Cuando se quita un dispositivo, la solución elimina la entrada de dispositivo del registro de identidad y, a continuación, elimina el dispositivo gemelo. La solución también elimina la información relacionada con el dispositivo de la base de datos de Cosmos DB. Para poder quitar un dispositivo, debe deshabilitarlo.

![Quitar dispositivo][img-device-remove]

## <a name="device-information-message-processing"></a>Procesamiento de mensajes de información de dispositivo

Los mensajes de información de dispositivo enviados por un dispositivo son distintos de los mensajes de telemetría. Los mensajes de información de dispositivo incluyen los comandos a los que un dispositivo puede responder y el historial de comandos. El propio Centro de IoT no conoce los metadatos contenidos en un mensaje de información de dispositivo y procesa el mensaje de la misma manera que cualquier mensaje del dispositivo a la nube. En la solución de supervisión remota, un trabajo de [Azure Stream Analytics][lnk-stream-analytics] (ASA) lee los mensajes de IoT Hub. El trabajo **DeviceInfo** de Stream Analytics filtra los mensajes que contienen **"ObjectType": "DeviceInfo"** y los reenvía a la instancia del host **EventProcessorHost**, que se ejecuta en un trabajo web. La lógica de la instancia de **EventProcessorHost** utiliza el identificador de dispositivo para encontrar el registro de Cosmos DB del dispositivo específico y actualizarlo.

> [!NOTE]
> Un mensaje de información de dispositivo es un mensaje estándar del dispositivo a la nube. La solución distingue entre mensajes de información de dispositivo y mensajes de telemetría mediante consultas ASA.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya ha terminado de aprender cómo personalizar las soluciones preconfiguradas, puede explorar algunas de las características y funcionalidades de las soluciones preconfiguradas del conjunto de aplicaciones de IoT:

* [Información general de la solución preconfigurada de mantenimiento predictivo][lnk-predictive-overview]
* [Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT][lnk-faq]
* [Seguridad de Internet de las cosas desde el principio][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
