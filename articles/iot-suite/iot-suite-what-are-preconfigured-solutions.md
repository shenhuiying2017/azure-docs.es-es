---
title: "Introducción a las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure | Microsoft Docs"
description: "Descripción de las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure y de sus arquitecturas con vínculos a recursos adicionales."
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
ms.date: 17/01/2018
ms.author: dobett
ms.openlocfilehash: 0ac6226b1e5773b1212b70172e2c13a0a5c925a8
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="what-is-azure-iot-suite"></a>¿Qué es el Conjunto de aplicaciones de IoT de Azure?

Conjunto de aplicaciones de IoT de Azure es un conjunto de *soluciones preconfiguradas* que:

* Se implementa en minutos
* Sirve de introducción rápida
* Se personaliza para adaptarse a sus requisitos específicos

Las soluciones preconfiguradas del *Conjunto de aplicaciones de IoT* están diseñadas según los mismos principios y objetivos.

En el siguiente vídeo se presenta información general de la solución preconfigurada de supervisión remota:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="preconfigured-solutions-overview"></a>Introducción a las soluciones preconfiguradas

Una solución preconfigurada es una implementación de patrones de soluciones de IoT comunes que puede implementar en Azure con su suscripción. Cada solución preconfigurada combina código personalizado con servicios de Azure para implementar escenarios de IoT específicos. Se puede personalizar cualquier escenario que se adapte a sus requisitos particulares. Entre los escenarios se incluyen los siguientes:

* Visualización de los datos detallados y el estado de la solución en un completo panel.
* Configuración de las reglas y las alarmas con la telemetría de dispositivos de IoT en vivo.
* Programación de los trabajos de administración de dispositivos, como las actualizaciones de software y la configuración.
* Abastecimiento de dispositivos físicos o simulados propios personalizados.
* Solución de problemas y corrección de errores en los grupos de dispositivos de IoT.

Cada solución preconfigurada es una implementación completa de un extremo a otro que usa dispositivos físicos o simulados para generar telemetría. Puede usar las soluciones preconfiguradas como aceleradores de soluciones para:

* Proporcionar un punto de partida para sus propias soluciones de IoT.
* Más información sobre los patrones comunes en el desarrollo y diseño de la solución de IoT.

Hoy existen tres soluciones preconfiguradas disponibles:

* [Supervisión remota](iot-suite-remote-monitoring-explore.md)
* [Mantenimiento predictivo](iot-suite-predictive-overview.md)
* [Factoría conectada](iot-suite-connected-factory-overview.md)

La tabla siguiente muestra cómo se asignan estas soluciones a las características específicas de IoT:

| Solución | Ingesta de datos | Identidad del dispositivo | Administración de dispositivos | Procesamiento perimetral | Comando y control | Reglas y acciones | Análisis predictivo |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [Supervisión remota](iot-suite-remote-monitoring-explore.md)  |Sí |Sí |Sí |-   |Sí |Sí |-   |
| [Mantenimiento predictivo](iot-suite-predictive-overview.md)   |Sí |Sí |-   |-   |Sí |Sí |Sí |
| [Factoría conectada](iot-suite-connected-factory-overview.md) |Sí |- |- |Sí |Sí |Sí |-   |

* *Ingesta de datos*: entrada de datos a escala en la nube.
* *Identidad del dispositivo*: administre identidades de dispositivos únicas y controle el acceso de los dispositivos a la solución.
* *Administración de dispositivos*: administre los metadatos de dispositivos y realice operaciones como reinicios de dispositivos y actualizaciones de firmware.
* *Comando y control*: envío de mensajes a un dispositivo desde la nube para que el dispositivo realice una acción.
* *Reglas y acciones*: el back-end de la solución usa reglas para actuar sobre los datos específicos de dispositivo a la nube.
* *Análisis predictivo*: el back-end de la solución analiza los datos del dispositivo a la nube para predecir cuándo se deben llevar a cabo acciones específicas. Por ejemplo, análisis de la telemetría del motor de un avión para determinar cuándo hay que realizar el mantenimiento del motor.

> [!NOTE]
> Para implementar una solución preconfigurada y obtener más información sobre cómo personalizarla, visite [Conjunto de aplicaciones de IoT de Microsoft Azure](https://www.azureiotsuite.com/).

## <a name="azure-services"></a>Servicios de Azure

Al implementar una solución preconfigurada, el proceso de aprovisionamiento configura distintos servicios de Azure. En la siguiente tabla se muestran los servicios usados en las soluciones preconfiguradas:

|                      | Supervisión remota  | Mantenimiento predictivo | Fábrica conectada |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT Hub              | Sí                |                        | Sí               |
| Event Hubs           |                    | Sí                    |                   |
| Time Series Insights |                    |                        | Sí               |
| Servicios de contenedor   | Sí                |                        |                   |
| Stream Analytics     |                    | Sí                    |                   |
| Web Apps             | Sí                | Sí                    | Sí               |
| Cosmos DB            | Sí                | Sí                    |                    |
| Azure Storage         |                    | Sí                    | Sí               |

> [!NOTE]
> Para más información sobre los recursos implementados en la solución preconfigurada de supervisión remota, consulte este [artículo](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments) en GitHub.

* [Azure IoT Hub](../iot-hub/index.md). Este servicio ofrece las capacidades de mensajería de dispositivo a la nube y de la nube al dispositivo y actúa como la puerta de enlace para la nube y los demás servicios del Conjunto de aplicaciones de IoT clave. El servicio le permite recibir mensajes de los dispositivos, a escala, y enviar comandos a los dispositivos. El servicio también le permite [administrar los dispositivos](../iot-hub/iot-hub-device-management-overview.md). Por ejemplo, puede configurar, reiniciar o realizar un restablecimiento de fábrica de uno o varios dispositivos conectados al centro.
* [Azure Event Hubs](../event-hubs/index.md). Este servicio proporciona la ingesta de eventos de gran volumen en la nube. Consulte [Comparación entre Azure IoT Hub y Azure Event Hubs](../iot-hub/iot-hub-compare-event-hubs.md).
* [Azure Time Series Insights](../time-series-insights/index.yml). Las soluciones preconfiguradas utilizan este servicio para analizar y mostrar los datos de telemetría de los dispositivos.
* [Azure Container Service](../container-service/index.yml). Este servicio hospeda y administra los microservicios en las soluciones preconfiguradas.
* [Azure Cosmos DB](../cosmos-db/index.yml) y [Azure Storage](../storage/index.yml) para el almacenamiento de datos.
* [Azure Stream Analytics](../stream-analytics/index.md). La solución preconfigurada de mantenimiento predictivo utiliza este servicio para procesar la telemetría entrante, realizar la agregación y detectar eventos. Esta solución preconfigurada también usa Stream Analytics para procesar los mensajes informativos que contienen datos como los metadatos y las respuestas de los comandos de los dispositivos.
* [Azure Web Apps](../app-service/index.yml) para hospedar el código de aplicación personalizado en las soluciones preconfiguradas.

Para una introducción a la arquitectura de una solución de IoT típica, consulte [Microsoft Azure e Internet de las cosas](iot-suite-what-is-azure-iot.md).

## <a name="whats-new-in-preconfigured-solutions"></a>Novedades de las soluciones preconfiguradas

Microsoft está actualizando las soluciones preconfiguradas a una nueva arquitectura basada en microservicios. En la siguiente tabla se muestra el estado actual de las soluciones preconfiguradas:

| Solución preconfigurada | Architecture  | Idiomas     |
| ---------------------- | ------------- | ------------- |
| Supervisión remota      | Microservicios | Java y .NET |
| Mantenimiento predictivo | MVC           | .NET          |
| Fábrica conectada      | MVC           | .NET          |

En las siguientes secciones se describen las novedades de las soluciones preconfiguradas basadas en microservicios:

### <a name="microservices"></a>Microservicios

La nueva versión de la solución preconfigurada de supervisión remota utiliza una arquitectura de microservicios. Esta solución preconfigurada se compone de varios microservicios, como un *administrador de IoT Hub* y un *administrador de Storage*. Las versiones de Java y .NET de cada microservicio están disponibles para descargar junto con la documentación para desarrolladores asociada. Para más información sobre la microservicios, consulte el artículo sobre la [arquitectura de supervisión remota](iot-suite-remote-monitoring-sample-walkthrough.md).

Esta arquitectura de microservicios es un patrón comprobado para las soluciones de nube que:

* Es escalable.
* Permite la extensibilidad.
* Es fácil de entender.
* Permite que los servicios individuales puedan intercambiarse como alternativas.

> [!TIP]
> Para más información sobre las arquitecturas de microservicios, consulte [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Arquitectura de la aplicación .NET) y [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservicios: una revolución de aplicaciones con la tecnología de la nube).

Al implementar la nueva versión de la supervisión remota, debe seleccionar una de las siguientes opciones de implementación:

* **Básico:** versión de menor costo para ver una demostración o probar una implementación. Todos los microservicios se implementan en una máquina virtual de Azure.
* **Standard:** implementación ampliada de la infraestructura para desarrollar una implementación de producción. Azure Container Service implementa los microservicios en varias máquinas virtuales de Azure. Kubernetes orquesta los contenedores de Docker que hospedan los microservicios individuales.

### <a name="language-choices-java-and-net"></a>Opciones de idioma: Java y .NET

Las implementaciones de los microservicios están disponibles en Java y .NET. Al igual que el código .NET, el código fuente de Java es código abierto y está disponible para que lo personalice según sus necesidades:

* [Repositorio de GitHub de .NET para la supervisión remota](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Repositorio de GitHub de Java para la supervisión remota](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Si desea ver otras implementaciones de idioma, agregue una solicitud a [UserVoice de IoT de Azure](https://feedback.azure.com/forums/321918-azure-iot).

### <a name="react-user-interface-framework"></a>Marco de la interfaz de usuario de React

La interfaz de usuario se ha creado mediante la biblioteca [React](https://facebook.github.io/react/) de javascript. El código fuente es de código abierto y está disponible para descargar y personalizar.

## <a name="next-steps"></a>pasos siguientes

Ahora que tiene información general de las soluciones preconfiguradas del Conjunto de aplicaciones de IoT, estos son los pasos siguientes sugeridos para cada una de ellas:

* [Exploración del Conjunto de aplicaciones de IoT de Azure para la solución de supervisión remota: modelo de implementación de Resource Manager](iot-suite-remote-monitoring-explore.md).
* [Información general de la solución preconfigurada de mantenimiento predictivo](iot-suite-predictive-overview.md).
* [Introducción a la solución preconfigurada de fábrica conectada](iot-suite-connected-factory-overview.md).

Para más información sobre las arquitecturas de solución de IoT, consulte el documento sobre los [servicios de IoT de Microsoft Azure: arquitectura de referencia](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf).
