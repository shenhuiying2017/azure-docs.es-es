---
title: Soluciones preconfiguradas de IoT de Azure | Microsoft Docs
description: "Descripción de las soluciones preconfiguradas de IoT de Azure y de sus arquitecturas con vínculos a recursos adicionales."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 76df013e8e5868fcc9f5d95aa523a6a56dea7163
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>¿Qué son las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure?

Las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure son implementaciones de patrones de soluciones de IoT comunes que se pueden implementar en Azure con su suscripción. Puede usar las soluciones preconfiguradas:

* Como punto de partida para sus propias soluciones IoT.
* Para más información sobre los patrones comunes en el desarrollo y diseño de la solución de IoT.

Cada solución preconfigurada es una implementación completa que usa dispositivos simulados para generar telemetría.

Puede descargar el código fuente completo para personalizar y ampliar la solución para que satisfaga sus necesidades específicas de IoT.

> [!NOTE]
> Para implementar una de las soluciones preconfiguradas, visite [Microsoft Azure IoT Suite][lnk-azureiotsuite] (Conjunto de aplicaciones de IoT de Microsoft Azure). En el artículo de [Introducción a las soluciones de IoT preconfiguradas][lnk-getstarted-preconfigured] se describe cómo implementar y ejecutar una de las soluciones.

La tabla siguiente muestra cómo se asignan estas soluciones a las características específicas de IoT:

| Solución | Ingesta de datos | Identidad del dispositivo | Administración de dispositivos | Comando y control | Reglas y acciones | Análisis predictivo |
| --- | --- | --- | --- | --- | --- | --- |
| [Supervisión remota][lnk-getstarted-preconfigured] |Sí |Sí |Sí |Sí |Sí |- |
| [Mantenimiento predictivo][lnk-predictive-maintenance] |Sí |Sí |- |Sí |Sí |Sí |
| [Fábrica conectada][lnk-getstarted-factory] |Sí |Sí |Sí |Sí |Sí |- |

* *Ingesta de datos*: entrada de datos a escala en la nube.
* *Identidad del dispositivo*: administre identidades de dispositivos únicas y controle el acceso de los dispositivos a la solución.
* *Administración de dispositivos*: administre los metadatos de dispositivos y realice operaciones como reinicios de dispositivos y actualizaciones de firmware.
* *Comando y control*: envío de mensajes a un dispositivo desde la nube para que el dispositivo realice una acción.
* *Reglas y acciones*: el back-end de la solución usa reglas para actuar sobre los datos específicos de dispositivo a la nube.
* *Análisis predictivo*: el back-end de la solución analiza los datos del dispositivo a la nube para predecir cuándo se deben llevar a cabo acciones específicas. Por ejemplo, análisis de la telemetría del motor de un avión para determinar cuándo hay que realizar el mantenimiento del motor.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Información general sobre la solución preconfigurada de supervisión remota

En este artículo decidimos analizar la solución preconfigurada de supervisión remota porque muestra elementos de diseño comunes que comparte con las demás soluciones.

El siguiente diagrama ilustra los elementos clave de la solución de supervisión remota. Las secciones siguientes proporcionan más información sobre estos elementos.

![Arquitectura de la solución preconfigurada de supervisión remota][img-remote-monitoring-arch]

## <a name="devices"></a>Dispositivos

Al implementar la solución preconfigurada de supervisión remota, se aprovisionan previamente cuatro dispositivos simulados en la solución que simulan un dispositivo de refrigeración. Estos dispositivos simulados tienen un modelo de temperatura y humedad integrado que emite telemetría. Estos dispositivos simulados se incluyen para hacer lo siguiente:

- Ilustrar el flujo de datos completo a través de la solución.
- Proporcionar un origen de telemetría conveniente.
- Proporcionar un destino para los métodos o comandos si es un desarrollador de back-end que usa la solución como punto de partida para una implementación personalizada.

Los dispositivos simulados de la solución pueden responder a las siguientes comunicaciones de nube a dispositivo:

- *Métodos ([métodos directos][lnk-direct-methods])*: método de comunicación bidireccional en el que se espera que un dispositivo conectado responda de inmediato.
- *Comandos (mensajes de nube a dispositivo)*: método de comunicación unidireccional en el que un dispositivo recupera el comando de una cola duradera.

Para ver una comparación de ambos enfoques distintos, consulte [Guía de comunicación de nube a dispositivo][lnk-c2d-guidance].

La primera vez que un dispositivo se conecta a IoT Hub en la solución preconfigurada, envía un mensaje de información de dispositivo al centro. Este mensaje enumera los métodos a los que el dispositivo puede responder. En la solución preconfigurada de supervisión remota, los dispositivos simulados admiten estos métodos:

* *Initiate Firmware Update*: este método inicia una tarea asincrónica en el dispositivo para realizar una actualización de firmware. La tarea asincrónica usa las propiedades notificadas para entregar actualizaciones de estado al panel de la solución.
* *Reboot*: este método hace que el dispositivo simulado se reinicie.
* *FactoryReset*: este método desencadena un restablecimiento de fábrica en el dispositivo simulado.

La primera vez que un dispositivo se conecta a IoT Hub en la solución preconfigurada, envía un mensaje de información de dispositivo al centro. Este mensaje enumera los comandos a los que el dispositivo puede responder. En la solución preconfigurada de supervisión remota, los dispositivos simulados admiten estos comandos:

* *Ping Device*: el dispositivo responde a este comando con una confirmación. Este comando es útil para comprobar que el dispositivo sigue activo y a la escucha.
* *Start Telemetry*: indica al dispositivo que empiece a enviar telemetría.
* *Stop Telemetry*: indica al dispositivo que detenga el envío de telemetría.
* *Change Set Point Temperature*: controla los valores de telemetría de temperatura simulados que envía el dispositivo. Este comando resulta útil para probar la lógica del back-end.
* *Diagnostic Telemetry*: controla si el dispositivo debe enviar la temperatura exterior como telemetría.
* *Change Device State*: establece la propiedad de metadatos de estado del dispositivo que el dispositivo notifica. Este comando resulta útil para probar la lógica del back-end.

Puede agregar más dispositivos simulados a la solución que emitan la misma telemetría y respondan a los mismos métodos y comandos.

Además de responder a comandos y métodos, la solución usa [dispositivos gemelos][lnk-device-twin]. Los dispositivos usan dispositivos gemelos para informar los valores de las propiedades al back-end de la solución. El panel de la solución usa los dispositivos gemelos para establecer los nuevos valores de propiedades deseados en los dispositivos. Por ejemplo, durante el proceso de actualización de firmware, el dispositivo simulado informa el estado de la actualización mediante las propiedades notificadas.

## <a name="iot-hub"></a>IoT Hub

En esta solución preconfigurada, la instancia de IoT Hub corresponde a la *puerta de enlace en la nube* de una típica [arquitectura de solución IoT][lnk-what-is-azure-iot].

Un Centro de IoT recibe la telemetría de los dispositivos en un único punto de conexión. Una instancia de IoT Hub también mantiene puntos de conexión específicos del dispositivo, con los cuales cada dispositivo puede recuperar los comandos que se le envían.

El Centro de IoT hace que la telemetría recibida esté disponible mediante el punto de conexión de lectura de telemetría del lado del servicio.

La funcionalidad de administración de dispositivos de IoT Hub le permite administrar las propiedades del dispositivo desde el portal de soluciones y programar los trabajos que realizan operaciones como las siguientes:

- Reinicio de los dispositivos
- Cambio de los estados de los dispositivos
- Actualizaciones de firmware

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

La solución preconfigurada usa tres trabajos de [Azure Stream Analytics][lnk-asa] (ASA) para filtrar la transmisión de la telemetría procedente de los dispositivos:

* *Trabajo DeviceInfo*: envía los datos a un centro de eventos que enruta los mensajes específicos del registro de dispositivos al registro de dispositivos de la solución. Este registro de dispositivos es una base de datos de Azure Cosmos DB. Estos mensajes se envían la primera vez que se conecta un dispositivo o en respuesta a un comando **Change device state**.
* *Trabajo Telemetría* : envía toda la telemetría sin procesar a Almacenamiento de blobs de Azure para su almacenamiento en frío y calcula las agregaciones de telemetría que se muestran en el panel de la solución.
* *Trabajo Reglas* : filtra del flujo de telemetría los valores que superan los umbrales de las reglas, y envía los datos a un Centro de eventos. Cuando se activa una regla, la vista de panel del portal de la solución muestra este evento como una fila nueva en la tabla del historial de alarmas. Estas reglas también pueden desencadenar una acción en función de la configuración definida en las vistas **Reglas** y **Acciones** en el portal de la solución.

En esta solución preconfigurada, los trabajos ASA forman parte del **back-end de la solución IoT** en una típica [arquitectura de solución IoT][lnk-what-is-azure-iot].

## <a name="event-processor"></a>procesador de eventos

En esta solución preconfigurada, el procesador de eventos forma parte del **back-end de la solución IoT** en una típica [arquitectura de solución IoT][lnk-what-is-azure-iot].

Los trabajos de ASA **DeviceInfo** y **Rules** envían su salida a Event Hubs para que se entregue a otros servicios de back-end. La solución usa una instancia de [EventPocessorHost][lnk-event-processor], que se ejecuta en un [trabajo web][lnk-web-job], para leer los mensajes de estos centros de eventos. **EventProcessorHost** usa:
- Los datos de **DeviceInfo** para actualizar los datos del dispositivo en la base de datos de Cosmos DB.
- Los datos de **Rules** para invocar la aplicación lógica y actualizar las alertas que se muestran en el portal de la solución.

## <a name="device-identity-registry-device-twin-and-cosmos-db"></a>Registro de identidades de dispositivo, dispositivo gemelo y Cosmos DB

Cada centro de IoT incluye un [registro de identidades de dispositivo][lnk-identity-registry] que almacena las claves de dispositivo. IoT Hub usa esta información para autenticar dispositivos; un dispositivo debe estar registrado y tener una clave válida para poder conectarse al centro.

Un [dispositivo gemelo][lnk-device-twin] es un documento JSON administrado por IoT Hub. El dispositivo gemelo de un dispositivo contiene lo siguiente:

- Las propiedades notificadas que el dispositivo envía al centro. Puede ver estas propiedades en el portal de la solución.
- Las propiedades deseadas que quiere enviar al dispositivo. Puede establecer estas propiedades en el portal de la solución.
- Las etiquetas que solo existen en el dispositivo gemelo, no en el dispositivo. Puede usar estas etiquetas para filtrar las listas de dispositivos en el portal de la solución.

Esta solución usa los dispositivos gemelos para administrar los metadatos de los dispositivos. La solución también usa una base de datos de Azure Cosmos DB para almacenar datos adicionales de dispositivo específicos para la solución, como los comandos que admite cada dispositivo y el historial de comandos.

La solución también debe mantener sincronizada la información del registro de identidades de dispositivo con el contenido de la base de datos de Azure Cosmos DB. **EventProcessorHost** usa los datos del trabajo de Stream Analytics **DeviceInfo** para administrar la sincronización.

## <a name="solution-portal"></a>Portal de solución

![Portal de solución][img-dashboard]

El portal de solución es una interfaz de usuario basada en web que se implementa en la nube como parte de la solución preconfigurada. Le permite:

* Ver la telemetría y el historial de alarmas en un panel.
* Aprovisionar dispositivos nuevos.
* Administrar y supervisar los dispositivos.
* Enviar comandos a dispositivos específicos.
* Invocar métodos en dispositivos específicos.
* Administrar reglas y acciones.
* Programar los trabajos para que se ejecuten en uno o varios dispositivos.

En esta solución preconfigurada, el portal de la solución forma parte tanto del **back-end de la solución de IoT** como de la **conectividad de procesamiento y empresarial** en la típica [arquitectura de solución][lnk-what-is-azure-iot].

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las arquitecturas de solución de IoT, consulte el documento de [Servicios de IoT de Microsoft Azure: Arquitectura de referencia][lnk-refarch].

Ahora que ya sabe qué es una solución preconfigurada, puede comenzar por implementar la solución preconfigurada de *supervisión remota*: [Introducción a las soluciones preconfiguradas][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-v1-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-v1-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-getstarted-factory]: iot-suite-connected-factory-overview.md
