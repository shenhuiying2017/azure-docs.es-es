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
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/30/2015"
 ms.author="dobett"/>

# ¿Qué son las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure?

Las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure son implementaciones de patrones de soluciones de IoT comunes que se puede implementar en Microsoft Azure con su suscripción de Azure. Puede usar las soluciones preconfiguradas:

- Como punto de partida para sus propias soluciones IoT.
- Para más información sobre los patrones comunes en el desarrollo y diseño de la solución de IoT.

Cada solución preconfigurada implementa un escenario de IoT común y es una implementación completa y descentralizada que usa dispositivos simulados para generar telemetría.

Además de implementar y ejecutar las soluciones preconfiguradas en Azure, puede descargar el código fuente completo y, después, personalizar y ampliar la solución para satisfacer sus necesidades de IoT específicas.

Las soluciones preconfiguradas disponibles son:

- [Supervisión remota][lnk-remote-monitoring]
- [Mantenimiento predictivo][lnk-predictive-maintenance]

La tabla siguiente muestra cómo se asignan estas soluciones preconfiguradas a las características específicas de IoT:

| Solución | Ingesta de datos | Identidad de dispositivos | Comando y control | Reglas y acciones | Análisis predictivo |
|------------------------|-----|-----|-----|-----|-----|
| Supervisión remota | Sí | Sí | Sí | Sí | - |
| Mantenimiento predictivo | Sí | Sí | Sí | Sí | Sí |

## Información general sobre la solución preconfigurada de supervisión remota

Esta sección describe algunos de los elementos clave de la solución preconfigurada de supervisión remota. Supervisión remota es la más sencilla de las soluciones preconfiguradas, y muestra elementos de diseño comunes que comparten las otras soluciones preconfiguradas.

El siguiente diagrama ilustra los elementos clave de la solución de supervisión remota. Las secciones siguientes proporcionan más información sobre estos elementos.

![Arquitectura de la solución preconfigurada de supervisión remota][img-remote-monitoring-arch]

## Dispositivos

Al implementar la solución preconfigurada de supervisión remota, la implementación incluye instancias de un simulador de dispositivos de software que simula un dispositivo refrigerador físico. Los dispositivos simulados envían telemetría de la temperatura y la humedad a un punto de conexión del Centro de IoT. Los dispositivos simulados también responden a los siguientes comandos enviados desde el portal de solución mediante el Centro de IoT:

- Ping Device
- Start Telemetry
- Stop Telemetry
- Change Set Point Temperature
- Diagnostic Telemetry
- Change Device State

## Centro de IoT

Un centro de IoT recibe la telemetría de los dispositivos de refrigeración en un único punto de conexión. Un Centro de IoT también mantiene puntos de conexión específicos del dispositivo donde cada dispositivo puede recuperar los comandos, como el comando **Ping Device**, que se le envían.

El Centro de IoT hace que la telemetría que recibe esté disponible mediante un punto de conexión del grupo de consumidores.

En esta solución preconfigurada, la instancia del Centro de IoT corresponde a la *puerta de enlace en la nube* de una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

## Análisis de transmisiones de Azure

La solución preconfigurada usa tres trabajos de [Análisis de transmisiones de Azure][lnk-asa] para filtrar la transmisión de la telemetría procedente de los dispositivos de refrigeración.

- El trabajo nº 1 envía toda la telemetría a Almacenamiento de blobs de Azure para un almacenamiento frío
- El trabajo nº 2 filtra la transmisión de la telemetría para identificar los mensajes de respuesta a los comandos y los mensajes de actualización del estado de los dispositivos y envía estos mensajes específicos a un punto de conexión del Centro de eventos de Azure.
- El trabajo nº 3 filtra la transmisión de la telemetría para los valores que desencadenan alarmas. Cuando un valor desencadena una alarma, la solución muestra la notificación en la tabla de historial de alarmas en la vista de panel del portal de solución.

En esta solución preconfigurada, los trabajos ASA forman parte del *back-end de solución de IoT* en una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

## Procesador de eventos

Una instancia de [EventPocessorHost][lnk-event-processor], que se ejecuta en un [WebJob][lnk-web-job], procesa los mensajes de respuestas a comandos y del estado de los dispositivos identificados el trabajo nº 2 de ASA, y almacena dicha información en una base de datos de [Azure DocumentDB][lnk-document-db].

En esta solución preconfigurada, el procesador de eventos forma parte del *back-end de solución de IoT* en una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

## Portal de solución

El portal de solución es una interfaz de usuario basada en web que se implementa en la nube como parte de la solución preconfigurada. Le permite:

- Ver la telemetría y el historial de alarmas en un panel.
- Aprovisionar dispositivos nuevos.
- Administrar y supervisar los dispositivos.
- Enviar comandos a dispositivos específicos.
- Administrar reglas y acciones.

> [AZURE.NOTE] El portal de solución también mantiene sincronizado el [registro de identidades de dispositivos][lnk-identity-registry] del Centro de IoT con el almacén de información de estado de dispositivo más sofisticada de la base de datos de DocumentDB.

En esta solución preconfigurada, el portal de solución forma parte del *back-end de solución de IoT* y de la *conectividad de procesamiento y empresarial* en una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

## Pasos siguientes

Examine estos recursos para obtener más información sobre las soluciones IoT preconfiguradas:

- [Información general sobre las soluciones preconfiguradas IoT de Azure][lnk-suite-overview]
- [Introducción a las soluciones de IoT preconfiguradas][lnk-preconf-get-started]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
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

<!---HONumber=AcomDC_0128_2016-->