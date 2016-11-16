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
ms.date: 08/09/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 597043b17993ebddc9cf730ddce849e1d6ff3bc9


---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>¿Qué son las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure?
Las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure son implementaciones de patrones de soluciones de IoT comunes que se pueden implementar en Azure con su suscripción. Puede usar las soluciones preconfiguradas:

* Como punto de partida para sus propias soluciones IoT.
* Para más información sobre los patrones comunes en el desarrollo y diseño de la solución de IoT.

Cada solución preconfigurada es una implementación completa que usa dispositivos simulados para generar telemetría.

Además de implementar y ejecutar las soluciones en Azure, puede descargar el código fuente completo y, después, personalizar y ampliar la solución para satisfacer sus necesidades de IoT específicas.

> [!NOTE]
> Para implementar una de las soluciones preconfiguradas, visite [Microsoft Azure IoT Suite][lnk-azureiotsuite] (Conjunto de aplicaciones de IoT de Microsoft Azure). En el artículo [Introducción a las soluciones de IoT preconfiguradas][lnk-getstarted-preconfigured] se describe cómo implementar y ejecutar una de las soluciones.
> 
> 

La tabla siguiente muestra cómo se asignan estas soluciones a las características específicas de IoT:

| Solución | Ingesta de datos | Identidad de dispositivos | Comando y control | Reglas y acciones | Análisis predictivo |
| --- | --- | --- | --- | --- | --- |
| [Supervisión remota][lnk-getstarted-preconfigured] |Sí |Sí |Sí |Sí |- |
| [Mantenimiento predictivo][lnk-predictive-maintenance] |yes |Sí |Sí |Sí |Sí |

* *Ingesta de datos*: entrada de datos a escala en la nube.
* *Identidad de dispositivo*: administración de las identidades únicas de cada dispositivo conectado.
* *Comando y control*: envío de mensajes a un dispositivo desde la nube para que el dispositivo realice alguna acción.
* *Reglas y acciones*: el back-end de la solución usa reglas para actuar sobre los datos específicos de dispositivo a la nube.
* *Análisis predictivo*: el back-end de la solución analiza los datos del dispositivo a la nube para predecir cuándo se deben llevar a cabo acciones específicas. Por ejemplo, análisis de la telemetría del motor de un avión para determinar cuándo hay que realizar el mantenimiento del motor.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Información general sobre la solución preconfigurada de supervisión remota
En este artículo decidimos analizar la solución preconfigurada de supervisión remota porque muestra elementos de diseño comunes que comparte con las demás soluciones.

El siguiente diagrama ilustra los elementos clave de la solución de supervisión remota. Las secciones siguientes proporcionan más información sobre estos elementos.

![Arquitectura de la solución preconfigurada de supervisión remota][img-remote-monitoring-arch]

## <a name="devices"></a>Dispositivos
Al implementar la solución preconfigurada de supervisión remota, se aprovisionan previamente cuatro dispositivos simulados en la solución que simulan un dispositivo de refrigeración. Estos dispositivos simulados tienen un modelo de temperatura y humedad integrado que emite telemetría. Estos dispositivos simulados se incluyen para ilustrar el flujo completo de los datos a través de la solución, así como para proporcionar un origen de telemetría conveniente y un destino para los comandos si es un desarrollador de back-end que usa la solución como punto de partida para una implementación personalizada.

La primera vez que un dispositivo se conecta al Centro de IoT en la solución preconfigurada de supervisión remota, el mensaje de información de dispositivo que se envía al Centro de IoT enumera la lista de comandos a los que el dispositivo puede responder. En la solución preconfigurada de supervisión remota, los comandos son: 

* *Ping Device*: el dispositivo responde a este comando con una confirmación. Es útil para comprobar que el dispositivo sigue activo y a la escucha.
* *Start Telemetry*: indica al dispositivo que empiece a enviar telemetría.
* *Stop Telemetry*: indica al dispositivo que detenga el envío de telemetría.
* *Change Set Point Temperature*: controla los valores de telemetría de temperatura simulados que envía el dispositivo. Resulta útil para probar la lógica del back-end.
* *Diagnostic Telemetry*: controla si el dispositivo debe enviar la temperatura exterior como telemetría.
* *Change Device State*: establece la propiedad de metadatos de estado del dispositivo de la que el dispositivo informa. Resulta útil para probar la lógica del back-end.

Puede agregar más dispositivos simulados a la solución que emitan la misma telemetría y respondan a los mismos comandos. 

## <a name="iot-hub"></a>IoT Hub
En esta solución preconfigurada, la instancia de IoT Hub corresponde a la *puerta de enlace en la nube* de una [arquitectura de soluciones de IoT] típica[lnk-what-is-azure-iot].

Un Centro de IoT recibe la telemetría de los dispositivos en un único punto de conexión. Un Centro de IoT también mantiene puntos de conexión específicos de dispositivo donde cada dispositivo puede recuperar los comandos que se le envían.

El Centro de IoT hace que la telemetría recibida esté disponible mediante el punto de conexión de lectura de telemetría del lado del servicio.

## <a name="azure-stream-analytics"></a>Análisis de transmisiones de Azure
La solución preconfigurada usa tres trabajos de [Azure Stream Analytics][lnk-asa] (ASA) para filtrar la transmisión de la telemetría procedente de los dispositivos:

* *Trabajo DeviceInfo* : envía los datos a un Centro de eventos que enruta los mensajes específicos del registro del dispositivo que se envían cuando un dispositivo se conecta por primera vez o en respuesta a un comando **Change device state** , al registro de dispositivos de la solución (una base de datos de DocumentDB). 
* *Trabajo Telemetría* : envía toda la telemetría sin procesar a Almacenamiento de blobs de Azure para su almacenamiento en frío y calcula las agregaciones de telemetría que se muestran en el panel de la solución.
* *Trabajo Reglas* : filtra del flujo de telemetría los valores que superan los umbrales de las reglas, y envía los datos a un Centro de eventos. Cuando se activa una regla, la vista de panel del portal de solución muestra este evento como una nueva fila en la tabla de historial de alarma y desencadena una acción basada en la configuración definida en las vistas Reglas y Acciones del portal de solución.

En esta solución preconfigurada, los trabajos de ASA forman parte del **back-end de la solución de IoT** en una [arquitectura de soluciones de IoT] típica[lnk-what-is-azure-iot].

## <a name="event-processor"></a>Procesador de eventos
En esta solución preconfigurada, el procesador de eventos forman parte del **back-end de la solución de IoT** en una [arquitectura de soluciones de IoT] típica[lnk-what-is-azure-iot].

Los trabajos de ASA **DeviceInfo** y **Rules** envían su salida a Event Hubs para entregarlos a otros servicios de back-end. La solución usa una instancia de [EventPocessorHost][lnk-event-processor], que se ejecuta en un [WebJob][lnk-web-job], para leer los mensajes de estos Event Hubs. **EventProcessorHost** usa los datos de **DeviceInfo** para actualizar los datos del dispositivo en la base de datos de DocumentDB y los datos de **Rules** para invocar la aplicación lógica y actualizar las alertas que se muestran en el portal de la solución.

## <a name="device-identity-registry-and-documentdb"></a>Registro de identidades de dispositivo y DocumentDB
Cada IoT Hub incluye un [registro de identidades de dispositivo][lnk-identity-registry] que almacena las claves de dispositivo. El Centro de IoT usa esta información para autenticar dispositivos; un dispositivo debe estar registrado y tener una clave válida para poder conectarse al centro.

Esta solución almacena información adicional acerca de los dispositivos, como su estado, los comandos que admiten y otros metadatos. La solución usa una base de datos de DocumentDB para almacenar estos datos de dispositivo específicos de la solución, y el portal de la solución recupera los datos de esta base de datos DocumentDB para su presentación y edición.

La solución también debe mantener sincronizada la información del registro de identidades de dispositivo con el contenido de la base de datos de DocumentDB. **EventProcessorHost** usa los datos del trabajo de Stream Analytics **DeviceInfo** para administrar la sincronización.

## <a name="solution-portal"></a>Portal de solución
![Panel de soluciones][img-dashboard]

El portal de solución es una interfaz de usuario basada en web que se implementa en la nube como parte de la solución preconfigurada. Le permite:

* Ver la telemetría y el historial de alarmas en un panel.
* Aprovisionar dispositivos nuevos.
* Administrar y supervisar los dispositivos.
* Enviar comandos a dispositivos específicos.
* Administrar reglas y acciones.

En esta solución preconfigurada, el portal de la solución forma parte tanto del **back-end de la solución de IoT** como de la **conectividad de procesamiento y empresarial** en la [arquitectura de soluciones de IoT] típica[lnk-what-is-azure-iot].

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de las arquitecturas de soluciones de IoT, consulte [Microsoft Azure IoT services: Reference Architecture] (Servicios IoT de Microsoft Azure: arquitectura de referencia)[lnk-refarch].

Ahora que sabe qué es una solución preconfigurada, puede comenzar por implementar la solución preconfigurada de *supervisión remota*: [Introducción a las soluciones preconfiguradas][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md



<!--HONumber=Nov16_HO2-->


