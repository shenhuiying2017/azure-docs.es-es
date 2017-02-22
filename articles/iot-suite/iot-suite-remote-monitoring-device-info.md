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
ms.date: 12/15/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2e4220bedcb0091342fd9386669d523d4da04d1c
ms.openlocfilehash: 8aac22bed0b16c97faabf1e15c9fc9f40c34ca67


---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Metadatos de información de dispositivo en la solución preconfigurada de supervisión remota
La solución preconfigurada de supervisión remota del Conjunto de aplicaciones de IoT de Azure muestra un enfoque para administrar los metadatos de dispositivo. En este artículo se describe el enfoque que adopta esta solución para que comprenda:

* Qué metadatos de dispositivo almacena la solución.
* Cómo la solución administra los metadatos de dispositivo.

## <a name="context"></a>Context
La solución preconfigurada de supervisión remota usa [Azure IoT Hub][lnk-iot-hub] para que los dispositivos puedan enviar datos a la nube. IoT Hub incluye un [registro de identidad del dispositivo][lnk-identity-registry] para controlar el acceso a dicho IoT Hub. Este registro es diferente del *Registro de dispositivos* específico de la solución de supervisión remota que almacena los metadatos de información de dispositivo. La solución de supervisión remota emplea una base de datos de [DocumentDB][lnk-docdb] para implementar su registro de dispositivos y almacenar los metadatos de información de dispositivo. En el documento sobre la [arquitectura de referencia de IoT de Microsoft Azure][lnk-ref-arch] se describe la función del registro de dispositivos en una solución típica de IoT.

> [!NOTE]
> La solución preconfigurada de supervisión remota mantiene sincronizado el Registro de identidad del dispositivo con el Registro de dispositivos. Ambos utilizan el mismo id. de dispositivo para identificar de forma única cada dispositivo conectado a su centro de IoT.
> 
> 

La [administración de dispositivos con IoT Hub][lnk-dm-preview] agrega características a IoT Hub similares a las de administración de la información de los dispositivos que se describen en este artículo. En este momento, la solución de supervisión remota solo hace uso de las características de IoT Hub disponibles con carácter general (GA).

## <a name="device-information-metadata"></a>Metadatos de información de dispositivo
Un documento JSON de metadatos de información de dispositivo almacenado en la base de datos de DocumentDB del Registro de dispositivos tiene la siguiente estructura:

```
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

* **DeviceProperties**: el propio dispositivo escribe estas propiedades y el dispositivo es la entidad emisora de estos datos. Otras propiedades de dispositivo de ejemplo incluyen el número de serie, el número de modelo y el fabricante. 
* **DeviceID**: el identificador único del dispositivo. Este valor es el mismo en el Registro de identidad del dispositivo del Centro de IoT.
* **HubEnabledState**: el estado del dispositivo en el Centro de IoT. Este valor se establece inicialmente en **null** hasta que el dispositivo se conecta por primera vez. En el portal de solución, un valor **null** se representa como el dispositivo que está "registrado pero no está presente".
* **CreatedTime**: la hora a la que se creó el dispositivo.
* **DeviceState**: el estado notificado por el dispositivo.
* **UpdatedTime**: la hora en que se actualizó por última vez el dispositivo mediante el portal de solución.
* **SystemProperties**: el portal de solución escribe las propiedades del sistema y el dispositivo no conoce estas propiedades. Un ejemplo de propiedad del sistema es el **ICCID** si la solución está autorizada y conectada con un servicio de administración de dispositivos habilitados para SIM.
* **Commands**: una lista de los comandos que admite el dispositivo. El dispositivo proporciona esta información a la solución.
* **CommandHistory**: una lista de los comandos enviados por la solución de supervisión remota al dispositivo y el estado de esos comandos.
* **IsSimulatedDevice**: una marca que identifica un dispositivo como un dispositivo simulado.
* **id**: el identificador único de DocumentDB para este documento de dispositivo.

> [!NOTE]
> La información de dispositivo también puede incluir metadatos para describir la telemetría que envía el dispositivo al Centro de IoT. La solución de supervisión remota utiliza estos metadatos de telemetría para personalizar cómo se muestra en el panel la [telemetría dinámica][lnk-dynamic-telemetry].
> 
> 

## <a name="lifecycle"></a>Ciclo de vida
La primera vez que se crea un dispositivo en el portal de solución, la solución crea una entrada en su Registro de dispositivos, como se mostró anteriormente. Inicialmente, gran parte de esta información está deshabilitada y **HubEnabledState** está establecido en **null**. En este punto, la solución también crea una entrada para el dispositivo en el Registro de identidad del dispositivo que genera las claves que usa el dispositivo para autenticarse en IoT Hub.

Cuando un dispositivo se conecta por primera vez a la solución, envía un mensaje de información de dispositivo. Este mensaje incluye propiedades del dispositivo, como el fabricante, el número de modelo y el número de serie. Un mensaje de información de dispositivo también incluye una lista de los comandos que admite el dispositivo, como información sobre los parámetros de comando. Cuando la solución recibe este mensaje, actualiza los metadatos de información de dispositivo en el Registro de dispositivos.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Visualización y edición de la información de dispositivo en el portal de solución
En la lista de dispositivos del portal de la solución, aparecen las siguientes propiedades de dispositivo en forma de columnas: **Estado**, **Id. de dispositivo**, **Fabricante**, **Número de modelo**, **Número de serie**, **Firmware**, **Plataforma**, **Procesador** y **RAM instalada**. Las propiedades **Latitud** y **Longitud** controlan la ubicación en el mapa de Bing incluido en el panel. 

![Lista de dispositivos][img-device-list]

Si hace clic en **Editar** en el panel **Detalles del dispositivo** del portal de la solución, puede editar todas estas propiedades. La edición de estas propiedades actualiza el registro del dispositivo en la base de datos DocumentDB. Sin embargo, si un dispositivo envía un mensaje de información de dispositivo actualizado, sobrescribirá los cambios realizados en el portal de solución. Las propiedades **Id. de dispositivo**, **Nombre de host**, **HubEnabledState**, **CreatedTime**, **DeviceState** y **UpdatedTime** no se pueden modificar en el portal de la solución, ya que el dispositivo es el único que tiene autoridad sobre estas propiedades.

![Edición de dispositivos][img-device-edit]

Puede usar el portal de solución para quitar un dispositivo de la solución. Cuando se quita un dispositivo, la solución elimina los metadatos de información de dispositivo del Registro de dispositivos de la solución y quita la entrada del dispositivo del Registro de identidad del dispositivo del Centro de IoT. Para poder quitar un dispositivo, debe deshabilitarlo.

![Quitar dispositivo][img-device-remove]

## <a name="device-information-message-processing"></a>Procesamiento de mensajes de información de dispositivo
Los mensajes de información de dispositivo enviados por un dispositivo son distintos de los mensajes de telemetría. Los mensajes de información de dispositivo incluyen información como las propiedades del dispositivo, los comandos a los que un dispositivo puede responder y el historial de comandos. El propio Centro de IoT no conoce los metadatos contenidos en un mensaje de información de dispositivo y procesa el mensaje de la misma manera que cualquier mensaje del dispositivo a la nube. En la solución de supervisión remota, un trabajo de [Azure Stream Analytics][lnk-stream-analytics] (ASA) lee los mensajes de IoT Hub. El trabajo **DeviceInfo** de Stream Analytics filtra los mensajes que contienen **"ObjectType": "DeviceInfo"** y los reenvía a la instancia del host **EventProcessorHost**, que se ejecuta en un trabajo web. La lógica de la instancia de **EventProcessorHost** utiliza el identificador de dispositivo para encontrar el registro de DocumentDB del dispositivo específico y actualizarlo. El Registro de dispositivos incluye ahora información como las propiedades, los comandos y el historial de comandos del dispositivo.

> [!NOTE]
> Un mensaje de información de dispositivo es un mensaje estándar del dispositivo a la nube. La solución distingue entre mensajes de información de dispositivo y mensajes de telemetría mediante consultas ASA.
> 
> 

## <a name="example-device-information-records"></a>Ejemplo de registros de información de dispositivo
La solución preconfigurada de supervisión remota emplea dos tipos de registros de información de dispositivo: registros para los dispositivos simulados implementados con la solución y registros para los dispositivos personalizados que se conectan a la solución.

### <a name="simulated-device"></a>Dispositivo simulado
En el ejemplo siguiente se muestra el registro de información de dispositivo JSON para un dispositivo simulado. Este registro tiene un valor establecido para **UpdatedTime** que indica que el dispositivo ha enviado un mensaje de **DeviceInfo** a IoT Hub. El registro incluye algunas propiedades de dispositivo comunes, define los seis comandos que admiten los dispositivos simulados y tiene la marca **IsSimulatedDevice** establecida en **1**.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### <a name="custom-device"></a>Dispositivo personalizado
En el ejemplo siguiente, se muestra el registro de información de dispositivo JSON de un dispositivo personalizado que tiene la marca **IsSimulatedDevice** establecida en **0**. Puede ver que este dispositivo personalizado admite dos comandos y que el portal de solución ha enviado un comando **SetTemperature** al dispositivo:

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

El siguiente ejemplo muestra el mensaje **DeviceInfo** de JSON que el dispositivo ha enviado para actualizar los metadatos de información de dispositivo:

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

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
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md



<!--HONumber=Dec16_HO3-->


