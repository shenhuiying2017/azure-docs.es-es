---
title: Tutorial de mantenimiento predictivo | Microsoft Docs
description: "Tutorial de la solución preconfigurada de mantenimiento predictivo de IoT de Azure."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3c48a716-b805-4c99-8177-414cc4bec3de
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: a68a8fdc3976ade0d1036d5ed58c8b2eb6d32a5d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Tutorial de la solución preconfigurada de mantenimiento predictivo

La solución preconfigurada de mantenimiento predictivo es una solución completa para un escenario empresarial, que predice el punto en el que es probable que se produzca un error. Puede utilizar esta solución preconfigurada de forma proactiva para actividades como, por ejemplo, la optimización del mantenimiento. La solución combina servicios esenciales del Conjunto de aplicaciones de IoT de Azure, como IoT Hub, Stream Analytics y un área de trabajo de [Azure Machine Learning][lnk-machine-learning]. Esta área de trabajo contiene un modelo, basado en un conjunto público de datos de ejemplo para predecir la vida útil restante (RUL) de un motor de avión. La solución proporciona una implementación completa del escenario empresarial como punto de partida para planear e implementar una solución que satisfaga sus propios requisitos empresariales específicos.

## <a name="logical-architecture"></a>Arquitectura lógica

El diagrama siguiente describe los componentes lógicos de la solución preconfigurada:

![][img-architecture]

Los elementos de color azul son servicios de Azure que se aprovisionan en la región en la que se implementó la solución preconfigurada. La lista de regiones donde puede implementar la solución preconfigurada se muestra en la [página de aprovisionamiento][lnk-azureiotsuite].

El elemento verde es un dispositivo simulado que representa un motor de avión. Puede obtener más información acerca de estos dispositivos simulados en la sección siguiente.

Los elementos de color gris representan los componentes que implementan funcionalidades de *administración de dispositivos*. La versión actual de la solución preconfigurada de mantenimiento predictivo no aprovisiona estos recursos. Para más información sobre la administración de dispositivos, consulte la [solución preconfigurada de supervisión remota][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Dispositivos simulados

En la solución preconfigurada, un dispositivo simulado representa un motor de avión. La solución se aprovisiona con dos motores que se asignan a un avión único. Cada motor emite cuatro tipos de telemetría: Sensor 9, Sensor 11, Sensor 14 y Sensor 15 proporcionan los datos necesarios para el modelo de Machine Learning a fin de calcular la vida útil restante (RUL) del motor. Cada dispositivo simulado envía los siguientes mensajes de telemetría al Centro de IoT:

*Recuento de ciclos*. Un ciclo representa un vuelo completo con una duración de entre dos y diez horas. Durante el vuelo, se capturan datos de telemetría cada media hora.

*Telemetría*. Existen cuatro sensores que representan atributos de motor. Los sensores se etiquetan genéricamente Sensor 9, Sensor 11, Sensor 14 y Sensor 15. Estos cuatro sensores representan la telemetría suficiente para obtener resultados útiles en el modelo de RUL. El modelo usado en la solución preconfigurada se crea a partir de un conjunto de datos público que incluye datos reales del sensor del motor. Para más información sobre cómo se creó el modelo del conjunto de datos original, consulte [Predictive Maintenance Template (Plantilla de mantenimiento predictivo) en la Galería de Cortana Intelligence][lnk-cortana-analytics].

Los dispositivos simulados también pueden controlar los siguientes comandos enviados desde el centro de IoT en la solución:

| Comando | Description |
| --- | --- |
| StartTelemetry |Controla el estado de la simulación.<br/>Inicia el envío de telemetría del dispositivo. |
| StopTelemetry |Controla el estado de la simulación.<br/>Detiene el envío de telemetría del dispositivo. |

Centro de IoT proporciona la confirmación de los comandos del dispositivo.

## <a name="azure-stream-analytics-job"></a>Trabajo de Análisis de transmisiones de Azure

**Trabajo: telemetría** opera en la transmisión entrante de la telemetría del dispositivo mediante dos instrucciones:

* La primera selecciona todos los datos de telemetría de los dispositivos y los envía al almacenamiento de blobs. A partir de aquí, se visualizan en la aplicación web.
* La segunda calcula los valores medios del sensor con una ventana deslizante de dos minutos y envía estos datos a través del centro de eventos a un **procesador de eventos**.

## <a name="event-processor"></a>procesador de eventos
El **host de procesador de eventos** se ejecuta en un trabajo web de Azure. El **procesador de eventos** toma los valores promedio de los sensores para un ciclo completado. Luego, pasa esos valores a una API que expone el modelo entrenado para calcular la vida útil restante (RUL) de un motor. La API se expone un área de trabajo de Machine Learning que se aprovisiona como parte de la solución.

## <a name="machine-learning"></a>Aprendizaje automático
El componente de Machine Learning usa un modelo que se deriva de los datos recopilados de los motores de avión reales. Puede navegar al área de trabajo de Machine Learning desde el icono de la página [azureiotsuite.com][lnk-azureiotsuite] de la solución de aprovisionamiento. El icono está disponible cuando la solución se encuentra en el estado **Listo**.


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha visto los componentes clave de la solución preconfigurada de mantenimiento predictivo, puede personalizarla si así lo desea. Consulte [Personalizar una solución preconfigurada][lnk-customize].

También puede explorar algunas de las demás características y funcionalidades de las soluciones preconfiguradas del conjunto de aplicaciones de IoT:

* [Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT][lnk-faq]
* [Seguridad de Internet de las cosas desde el principio][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/