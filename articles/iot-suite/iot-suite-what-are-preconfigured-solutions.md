<properties
 pageTitle="¿Qué son las soluciones preconfiguradas de IoT de Azure? | Microsoft Azure"
 description="Descripción de las soluciones preconfiguradas de IoT de Azure y de sus arquitecturas con vínculos a recursos adicionales."
 services=""
 documentationCenter=".net"
 authors="aguilaaj"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/22/2015"
 ms.author="araguila"/>

# ¿Cuáles son las soluciones preconfiguradas de IoT de Azure?
Es posible implementar soluciones preconfiguradas que implementen escenarios comunes de Internet de las cosas (IoT) en Microsoft Azure con su suscripción de Azure. Puede usar las soluciones preconfiguradas como punto de partida para sus propias soluciones de IoT, o bien para obtener información sobre los patrones más comunes abordados en el desarrollo y el diseño de soluciones de IoT.

Cada solución preconfigurada implementa un escenario común de IoT y es una implementación completa e integral.

Además de implementar y ejecutar soluciones preconfiguradas en Azure, puede descargar el código fuente completo para personalizar y ampliar la solución para satisfacer sus necesidades específicas.

Las soluciones preconfiguradas disponibles son: supervisión remota y mantenimiento predictivo (próximamente disponible).

La tabla siguiente muestra cómo se asignan estas soluciones preconfiguradas a las características específicas de IoT:

| Solución | Ingesta de datos | Identidad de dispositivos | Comando y control | Reglas y acciones | Análisis predictivo |
|------------------------|----------------|-----------------|---------------------|-------------------|----------------------|
| Supervisión remota | Sí | Sí | Sí | Sí | - | | Mantenimiento predictivo | Sí | Sí | Sí | Sí | Sí |

## Información general de la muestra de supervisión remota

La supervisión remota es la solución preconfigurada más sencilla con funcionalidad completa. Esta sección describe algunas de las características claves de la solución preconfigurada de supervisión remota por medio de una introducción a la gama completa de soluciones preconfiguradas.

El siguiente diagrama muestra las características clave de la solución y las secciones siguientes. Las secciones siguientes proporcionan más información sobre los elementos que se muestran en este diagrama.

![Arquitectura de la solución preconfigurada de supervisión remota][img-remote-monitoring-arch]

### Dispositivo
El dispositivo que se suministra previamente aprovisionado con la solución preconfigurada de supervisión remota es una simulación de software de un refrigerador que envía datos de telemetría de temperatura y humedad. El dispositivo también puede responder a un conjunto de comandos que se envían desde el portal de la solución a través del centro de IoT. Los comandos ya implementados en el simulador son: Ping Device, Start Telemetry, Stop Telemetry, Change Set Point Temp, Diagnostic Telemetry y Change Device State.

Los refrigeradores de esta solución preconfigurada corresponden a los **dispositivos y orígenes de datos** de una arquitectura de solución de IoT típica.

### Centro de IoT
Un centro de IoT recibe los datos de telemetría de los refrigeradores situados en un único extremo y mantiene los extremos específicos en los que los dispositivos pueden recuperar comandos como, por ejemplo, el comando PingDevice.

El centro de IoT expone los datos de telemetría que recibe a través de un extremo de grupo del consumidor.

La instancia del centro de IoT de esta solución preconfigurada corresponde a la **aplicación de back-end de IoT** de una arquitectura de solución de IoT típica.

### Análisis de transmisiones de Azure
La solución preconfigurada usa los trabajos de análisis de secuencias de Azure para filtrar la secuencia de eventos procedentes de los refrigeradores. Un trabajo envía todos los datos de telemetría a los blobs de almacenamiento de Azure para almacenamiento inactivo. El segundo trabajo filtra la secuencia de eventos para los mensajes de respuesta de comandos y los mensajes de actualización de estado de dispositivo y envía dichos mensajes específicos a un extremo del centro de eventos de Azure. La tercera tarea filtra las alarmas desencadenadas y muestra dichas alarmas en la tabla del historial de alarmas en la vista de panel del portal de la solución.


### Procesador de eventos
Una instancia de procesador de eventos, que se ejecuta en una tarea web, procesa los datos de estado de dispositivos y respuestas de comandos y almacena dicha información en la base de datos de documentos de Azure.


### Portal de solución
El Portal de solución es una interfaz de usuario basada en la Web que permite: ver la telemetría y el historial en el panel de alarmas, aprovisionar dispositivos nuevos, administrar y supervisar los dispositivos, enviar comandos a dispositivos específicos y administrar reglas y acciones.

Nota: la vista de dispositivos del portal de solución también mantiene el registro de identidad de dispositivos del centro de IoT sincronizado con el almacén de información de estado de dispositivo enriquecido en la base de datos de documentos.


## Pasos siguientes
Para comenzar a usar las soluciones preconfiguradas de IoT de Azure, examine los recursos siguientes: -[Información general de las soluciones preconfiguradas de IoT de Azure](iot-suite-overview.md) -[Introducción a las soluciones preconfiguradas de IoT](iot-suite-getstarted-preconfigured-solutions.md)

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png

<!---HONumber=Oct15_HO1-->