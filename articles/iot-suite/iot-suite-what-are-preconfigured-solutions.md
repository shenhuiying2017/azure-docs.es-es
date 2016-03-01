<properties
 pageTitle="Soluciones preconfiguradas de IoT de Azure | Microsoft Azure"
 description="Descripción de las soluciones preconfiguradas de IoT de Azure y de sus arquitecturas con vínculos a recursos adicionales."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/19/2016"
 ms.author="dobett"/>

# ¿Qué son las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure?

Las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure son implementaciones de patrones de soluciones de IoT comunes que se puede implementar en Microsoft Azure con su suscripción de Azure. Puede usar las soluciones preconfiguradas:

- Como punto de partida para sus propias soluciones IoT.
- Para más información sobre los patrones comunes en el desarrollo y diseño de la solución de IoT.

Cada solución preconfigurada implementa un escenario de IoT común y es una implementación completa y descentralizada que usa dispositivos simulados para generar telemetría.

Además de implementar y ejecutar las soluciones en Azure, puede descargar el código fuente completo y, después, personalizar y ampliar la solución para satisfacer sus necesidades de IoT específicas.

> [AZURE.NOTE] El artículo [Tutorial: Introducción a las soluciones de IoT preconfiguradas][lnk-preconf-get-started] describe cómo implementar y ejecutar una de las soluciones.

La tabla siguiente muestra cómo se asignan estas soluciones a las características específicas de IoT:

| Solución | Ingesta de datos | Identidad de dispositivos | Comando y control | Reglas y acciones | Análisis predictivo |
|------------------------|-----|-----|-----|-----|-----|
| [Supervisión remota][lnk-remote-monitoring] | Sí | Sí | Sí | Sí | - | | [Mantenimiento predictivo][lnk-predictive-maintenance] | Sí | Sí | Sí | Sí | Sí |

## Información general sobre la solución preconfigurada de supervisión remota

En este artículo se muestra la solución precofingurada de supervisión remota porque es la más sencilla de las soluciones y muestra elementos de diseño comunes que comparte con las demás soluciones.

El siguiente diagrama ilustra los elementos clave de la solución de supervisión remota. Las secciones siguientes proporcionan más información sobre estos elementos.

![Arquitectura de la solución preconfigurada de supervisión remota][img-remote-monitoring-arch]

## Dispositivos

Al implementar la solución preconfigurada de supervisión remota, se aprovisionan previamente cuatro dispositivos simulados en la solución que simulan un dispositivo de refrigeración. Estos dispositivos simulados tienen un modelo de temperatura y humedad integrado que emite telemetría.

La primera vez que un dispositivo se conecta al Centro de IoT en la solución preconfigurada de supervisión remota, el mensaje de información de dispositivo que se envía al Centro de IoT enumera la lista de comandos a los que el dispositivo puede responder. En la solución preconfigurada de supervisión remota, los comandos son:

- *Ping Device* El dispositivo responde a este comando con una confirmación. Es útil para comprobar que el dispositivo sigue activo y a la escucha.
- *Start Telemetry* Indica al dispositivo que empiece a enviar telemetría.
- *Stop Telemetry* Indica al dispositivo que detenga el envío de telemetría.
- *Change Set Point Temperature* Controla los valores de telemetría de temperatura simulados que envía el dispositivo. Es útil para realizar pruebas.
- *Diagnostic Telemetry* Controla si el dispositivo debe enviar la temperatura exterior como telemetría.
- *Change Device State* Establece la propiedad de metadatos del estado del dispositivo de la que informa el dispositivo. Es útil para realizar pruebas.

Puede agregar dispositivos simulados adicionales a la solución que emitan la misma telemetría y respondan a los mismos comandos.

## Centro de IoT

Un centro de IoT recibe la telemetría de los dispositivos de refrigeración en un único punto de conexión. Un Centro de IoT también mantiene puntos de conexión específicos de dispositivo donde cada dispositivo puede recuperar los comandos que se le envían.

El Centro de IoT hace que la telemetría que recibe esté disponible mediante un punto de conexión del grupo de consumidores.

En esta solución preconfigurada, la instancia del Centro de IoT corresponde a la *puerta de enlace en la nube* de una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

## Análisis de transmisiones de Azure

La solución preconfigurada usa tres trabajos de [Análisis de transmisiones de Azure][lnk-asa] para filtrar la transmisión de la telemetría procedente de los dispositivos de refrigeración.


- *Trabajo DeviceInfo*: envía mensajes específicos de registro del dispositivo al registro de dispositivos de la solución (una base de datos de DocumentDB).
- *Trabajo Telemetría*: envía toda la telemetría sin procesar al Almacenamiento de blobs de Azure para su almacenamiento en frío y calcula las agregaciones de telemetría que se muestran en el panel de la solución.
- *Trabajo Reglas*: filtra el flujo de telemetría para los valores que superan los umbrales de las reglas. Cuando se activa una regla, la vista de panel del portal de solución muestra este evento como una nueva fila en la tabla de historial de alarma y desencadena una acción basada en la configuración definida en las vistas Reglas y Acciones del portal de solución.

En esta solución preconfigurada, los trabajos ASA forman parte del *back-end de la solución de IoT* en una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

## Procesador de eventos

Una instancia de [EventPocessorHost][lnk-event-processor], que se ejecuta en un [WebJob][lnk-web-job], procesa los resultados de los trabajos DeviceInfo y Reglas.

En esta solución preconfigurada, el procesador de eventos forma parte de *back-end de la solución de IoT* en una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

## Portal de solución

![Panel de soluciones][img-dashboard]

El portal de solución es una interfaz de usuario basada en web que se implementa en la nube como parte de la solución preconfigurada. Le permite:

- Ver la telemetría y el historial de alarmas en un panel.
- Aprovisionar dispositivos nuevos.
- Administrar y supervisar los dispositivos.
- Enviar comandos a dispositivos específicos.
- Administrar reglas y acciones.

> [AZURE.NOTE] La solución preconfigurada mantiene la sincronización entre el [registro de identidad del dispositivo][lnk-identity-registry] del Centro de IoT y el registro de dispositivos de la solución (base de datos de DocumentDB), que almacena más metadatos del dispositivo.

En esta solución preconfigurada, el portal de solución forma parte tanto del *back-end de la solución de IoT* como de la *conectividad de procesamiento y empresarial* en una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

## Pasos siguientes

Examine estos recursos para obtener más información sobre las soluciones IoT preconfiguradas:

- [Introducción a las soluciones de IoT preconfiguradas][lnk-preconf-get-started]
- [Información general de la solución preconfigurada de mantenimiento predictivo][lnk-predictive-maintenance]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: web-sites-create-web-jobs.md
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-identity-registry]: iot-hub-devguide.md#device-identity-registry
[lnk-suite-overview]: iot-suite-overview.md
[lnk-preconf-get-started]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_0224_2016-->