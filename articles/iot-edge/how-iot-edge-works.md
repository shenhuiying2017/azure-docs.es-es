---
title: "Introducción a Azure IoT Edge | Microsoft Docs"
description: "Introducción al servicio Azure IoT Edge"
services: iot-Edge
documentationcenter: 
author: kgremban
manager: timlt
editor: chipalost
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: kgremban
ms.custom: 
ms.openlocfilehash: 5f69041572729d1458a22a855128639056d61586
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-iot-edge---preview"></a>Introducción a Azure IoT Edge (versión preliminar)

Azure IoT Edge mueve el análisis en la nube y lógica de negocios personalizada a los dispositivos para que su organización pueda centrarse en la información empresarial en lugar de en la administración de los datos. Habilite el escalado real de la solución al configurar el software de IoT, implementarlo en dispositivos a través de contenedores estándares y supervisar todo desde la nube.

Los análisis son un valor añadido empresarial para las soluciones de IoT, pero no es necesario que todos los análisis estén en la nube. Si desea que un dispositivo responda a emergencias lo antes posible, puede realizar la detección de anomalías en el propio dispositivo. Análogamente, si desea reducir los costos de ancho de banda y evitar la transferencia de terabytes de datos sin procesar, puede realizar la limpieza y la agregación de datos localmente. A continuación, envíe la información a la nube. 

Azure IoT Edge está formado por tres componentes:
* Los módulos de IoT Edge son contenedores que ejecutan servicios de Azure, de terceros o código propio del usuario. Se implementan en los dispositivos de IoT Edge y se ejecutan en ellos. 
* El entorno de tiempo de ejecución de IoT Edge se ejecuta en todos los dispositivos de IoT Edge y administra los módulos que se implementan en cada dispositivo. 
* Una interfaz basada en la nube permite supervisar y administrar los dispositivos de IoT Edge de forma remota.

## <a name="iot-edge-modules"></a>Módulos de IoT Edge

Los módulos de IoT Edge son unidades de ejecución implementadas actualmente como contenedores compatibles con Docker, que ejecutan la lógica de negocios en los dispositivos perimetrales. Se pueden configurar varios módulos para que se comuniquen entre sí al crear una canalización de procesamiento de datos. Puede desarrollar módulos personalizados o empaquetar determinados servicios de Azure en módulos que proporcionen información sin conexión y en el dispositivo perimetral. 

### <a name="artificial-intelligence-on-the-edge"></a>Inteligencia artificial en los dispositivos perimetrales

Azure IoT Edge permite implementar el procesamiento de eventos complejos, el aprendizaje automático, el reconocimiento de imágenes y otros tipos de inteligencia artificial de gran valor sin necesidad de escribirla internamente. Los servicios de Azure como Azure Functions, Azure Stream Analytics y Azure Machine Learning se pueden ejecutar de manera local a través de Azure IoT Edge; sin embargo, esto no se limita únicamente a los servicios de Azure. Cualquier persona puede crear módulos de inteligencia artificial y ponerlos a disposición de la comunidad. 

### <a name="bring-your-own-code"></a>Traiga su propio código

Cuando desee implementar su propio código en los dispositivos, Azure IoT Edge también lo admite. Azure IoT Edge aplica el mismo modelo de programación que los demás servicios de IoT de Azure. El mismo código se puede ejecutar en un dispositivo o en la nube. Azure IoT Edge es compatible con Windows y Linux, por lo que podrá codificar para la plataforma de su elección. Admite Java, .NET Core 2.0, Node.js, C y Python, por lo que los desarrolladores pueden codificar en un lenguaje que ya conozcan y usar lógica de negocios existente, sin escribir desde cero.

## <a name="iot-edge-runtime"></a>Entorno de tiempo de ejecución de IoT Edge

El entorno de tiempo de ejecución de Azure IoT Edge permite lógica de la nube y personalizada en dispositivos de IoT Edge. Se encuentra en el dispositivo de IoT Edge y realiza operaciones de administración y comunicación. El entorno de tiempo de ejecución realiza varias funciones:

* Instala y actualiza las cargas de trabajo en el dispositivo.
* Mantiene los estándares de seguridad de Azure IoT Edge en el dispositivo.
* Garantiza que los módulos de IoT Edge están siempre en ejecución.
* Informa del estado del módulo a la nube para la supervisión remota.
* Facilita la comunicación entre los dispositivos de hoja de nivel inferior y el dispositivo de IoT Edge.
* Facilita la comunicación entre los módulos y el dispositivo de IoT Edge.
* Facilita la comunicación entre el dispositivo de IoT Edge y la nube.

![El entorno de tiempo de ejecución de IoT Edge envía información e informes a IoT Hub][1]

Cómo usar los dispositivos de Azure IoT Edge es decisión suya. El entorno de tiempo de ejecución a menudo se usa para implementar inteligencia artificial en puertas de enlace que agregan y procesan datos de otros dispositivos locales, pero esto es solo una de las opciones disponibles. Los dispositivos de hoja también pueden ser dispositivos de Azure IoT Edge, independientemente de si están conectados a una puerta de enlace o directamente a la nube.

El entorno de tiempo de ejecución de Azure IoT Edge se ejecuta en un gran conjunto de dispositivos de IoT para habilitar el su uso de numerosas maneras distintas. Admite los sistemas operativos Windows y Linux y resume los detalles del hardware. Use un dispositivo menor que un Raspberry Pi 3 si no procesa muchos datos o mucho escale verticalmente a un servidor industrializado para ejecutar cargas de trabajo de recursos intensivas.

## <a name="iot-edge-cloud-interface"></a>Interfaz en la nube de IoT Edge

Administrar el ciclo de vida del software para los dispositivos de la empresa es complicado. Administrar el ciclo de vida del software para millones de dispositivos de IoT heterogéneos es incluso más difícil. Las cargas de trabajo deben crearse y configurarse para un determinado tipo de dispositivo, implementarse a escala en los millones de dispositivos de la solución y supervisarse para detectar los comportamientos incorrectos de los dispositivos. Estas actividades no se pueden realizar en cada dispositivo y deben realizarse a escala.

Azure IoT Edge se integra perfectamente con Conjunto de aplicaciones de IoT de Azure para conseguir un plan de control que se adapta a las necesidades de la solución. Los servicios en la nube permiten a los usuarios:

* Crear y configurar una carga de trabajo que se ejecute en un tipo específico de dispositivo.
* Enviar una carga de trabajo a un conjunto de dispositivos.
* Supervisar las cargas de trabajo que se ejecutan en los dispositivos de campo.

![La telemetría, la información y las acciones de los dispositivos se coordinan con la nube][2]

## <a name="next-steps"></a>Pasos siguientes

Pruebe estos conceptos al [implementar IoT Edge en un dispositivo simulado][lnk-quickstart].

<!-- Images -->
[1]: ./media/how-iot-edge-works/runtime.png
[2]: ./media/how-iot-edge-works/cloud-interface.png

<!-- Links -->
[lnk-quickstart]: quickstart.md