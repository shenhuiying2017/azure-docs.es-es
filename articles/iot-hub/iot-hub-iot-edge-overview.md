---
title: "Información general de Azure IoT Edge | Microsoft Docs"
description: "Describe los conceptos clave de la arquitectura de Azure IoT Edge, como puertas de enlace, módulos y agentes."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: ecdd56c91a8fc2011b3d7abe93b9d27c1e1e0bef
ms.contentlocale: es-es
ms.lasthandoff: 06/08/2017


---
# <a name="azure-iot-edge-architectural-concepts"></a>Conceptos de arquitectura de Azure IoT Edge

Antes de examinar el código de ejemplo o de crear su propia puerta de enlace de campo mediante IoT Edge, debe comprender los conceptos clave que subyacen a la arquitectura de IoT Edge.

## <a name="iot-edge-modules"></a>Módulos de IoT Edge

Una puerta de enlace se crea con Azure IoT Edge mediante la creación y el ensamblado de *módulos de IoT Edge*. Los módulos utilizan *mensajes* para intercambiarse datos. Un módulo recibe un mensaje, realiza alguna acción en él, opcionalmente lo transforma en un nuevo mensaje y luego lo publica para que otros módulos lo procesen. Existe la posibilidad de que algunos módulos solo produzcan nuevos mensajes y nunca procesen los mensajes entrantes. Una cadena de módulos crea una canalización de procesamiento de datos donde cada módulo realiza una transformación en los datos en un punto de esa canalización.

![Cadena de módulos de puerta de enlace creada con Azure IoT Edge][1]

IoT Edge contiene los siguientes componentes:

* Módulos escritos previamente que realizan funciones comunes de puerta de enlace.
* Las interfaces que un desarrollador puede utilizar para escribir módulos personalizados.
* La infraestructura necesaria para implementar y ejecutar un conjunto de módulos.

El SDK proporciona una capa de abstracción que permite crear puertas de enlace para trabajar en varios sistemas operativos y plataformas.

![Capa de abstracción de Azure IoT Edge][2]

## <a name="messages"></a>error de Hadoop

Aunque una manera práctica de conceptualizar cómo funciona una puerta de enlace es pensar que los módulos se pasan mensajes entre ellos, no refleja realmente lo que sucede. Los módulos de IoT Edge usan un agente para comunicarte entre sí. Los módulos publican mensajes al agente (mediante patrones de mensajería como bus o de publicación y suscripción) y, a continuación, permiten al agente enrutar el mensaje a los módulos conectados a él.

Un módulo usa la función **Broker_Publish** para publicar un mensaje en el agente. El agente entrega los mensajes a un módulo mediante la invocación de una función de devolución de llamada. Un mensaje consta de un conjunto de propiedades de clave/valor y del contenido que se pasa como un bloque de memoria.

![Rol del agente en Azure IoT Edge][3]

## <a name="message-routing-and-filtering"></a>Enrutamiento y filtro de mensajes

Hay dos maneras de dirigir los mensajes a los módulos de IoT Edge correctos:

* Se puede pasar un conjunto de vínculos al agente para que este conozca tanto el origen como el receptor de cada módulo.
* Un módulo puede filtrar por las propiedades del mensaje.

Un módulo solo debe actuar en los mensajes destinados a ello. Los vínculos y el filtrado de mensajes crean de manera eficaz una canalización de mensajes.

## <a name="next-steps"></a>Pasos siguientes

Para ver estos conceptos aplicados en un ejemplo que se puede ejecutar, consulte [Explorar la arquitectura de Azure IoT Edge][lnk-hello-world].

<!-- Images -->
[1]: media/iot-hub-iot-edge-overview/modules.png
[2]: media/iot-hub-iot-edge-overview/modules_2.png
[3]: media/iot-hub-iot-edge-overview/messages_1.png

<!-- Links -->
[lnk-hello-world]: ./iot-hub-linux-iot-edge-get-started.md
