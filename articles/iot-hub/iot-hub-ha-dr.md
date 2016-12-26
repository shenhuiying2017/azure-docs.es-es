---
title: "Alta disponibilidad y recuperación ante desastres de IoT Hub | Microsoft Docs"
description: "Describe características que ayudan a crear soluciones de IoT con alta disponibilidad y con funcionalidad de recuperación ante desastres."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: ae320e58-aa20-45b9-abdc-fa4faae8e6dd
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: ce514e19370d2b42fb16b4e96b66f212d5fa999c
ms.openlocfilehash: 84bd5042ee1e795347f10eb251b1e0f1522e27bf


---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Alta disponibilidad y recuperación ante desastres del Centro de IoT
Como un servicio de Azure, el Centro de IoT proporciona alta disponibilidad (HA) usando redundancias en la región de Azure, sin que la solución tenga que realizar ningún trabajo adicional. La plataforma Microsoft Azure también incluye características para ayudarle a crear soluciones con funcionalidades de recuperación ante desastres o disponibilidad entre regiones. Si desea proporcionar alta disponibilidad global entre regiones para dispositivos o usuarios, debe diseñar y preparar las soluciones para que aprovechen estas características de recuperación ante desastres de Azure. En el artículo [Guía técnica sobre la continuidad empresarial de Azure](../resiliency/resiliency-technical-guidance.md) , se describen las características integradas en Azure para la continuidad empresarial y la recuperación ante desastres. El documento [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure][Disaster recovery and high availability for Azure applications] proporciona una guía de arquitectura enfocada a estrategias para que las aplicaciones de Azure logren alta disponibilidad y recuperación ante desastres.

## <a name="azure-iot-hub-dr"></a>Recuperación ante desastres de Centro de IoT de Azure
Además de la alta disponibilidad dentro de una región, Centro de IoT implementa mecanismos de conmutación por error para recuperación ante desastres que no requieren intervención del usuario. La recuperación ante desastres del Centro de IoT se inicia automáticamente y tiene un objetivo de tiempo de recuperación (RTO) de 2 a 26 horas y los siguientes objetivos de punto de recuperación (RPO).

| Funcionalidad | RPO |
| --- | --- |
| Disponibilidad de servicio para las operaciones de registro y comunicación |Posible pérdida de CName |
| Datos de identidad en el registro de identidad |Pérdida de datos de 0-5 minutos |
| Mensajes de dispositivo a nube |Se pierden todos los mensajes no leídos |
| Mensajes de supervisión de operaciones |Se pierden todos los mensajes no leídos |
| Mensajes de nube a dispositivo |Pérdida de datos de 0-5 minutos |
| Cola de comentarios de nube a dispositivo |Se pierden todos los mensajes no leídos |

## <a name="regional-failover-with-iot-hub"></a>Conmutación por error regional con el Centro de IoT
El objetivo de este artículo no es proporcionar una descripción completa de las topologías de implementación de soluciones de IoT. Este artículo explica el modelo de implementación de *conmutación por error regional* para la alta disponibilidad y la recuperación ante desastres.

En un modelo de conmutación por error regional, el back-end de solución se ejecuta principalmente en la ubicación de un centro de datos y un centro de IoT secundario y back-end se implementan otra ubicación de centro de datos. Si el centro de IoT del centro de datos principal sufre una interrupción o se interrumpe la conectividad de red del dispositivo al centro de datos principal, los dispositivos se conectan a puntos de conexión del centro de datos secundario. Con la capacidad de conmutación por error entre regiones, la disponibilidad de la solución puede mejorar más allá de la alta disponibilidad de una única región.

A grandes rasgos, para implementar un modelo de conmutación por error regional con IoT Hub, necesitará la siguiente:

* **Un Centro de IoT secundario y lógica de enrutamiento de dispositivo**: si se produce una interrupción del servicio en la región primaria, los dispositivos deben empezar a conectarse a la región secundaria. Como se conoce el estado de la mayoría de los servicios implicados, es habitual que los administradores de la solución desencadenen el proceso de conmutación por error entre regiones. La mejor forma de comunicar el nuevo punto de conexión con los dispositivos, sin perder el control del proceso, es hacer que comprueben periódicamente un servicio de *conserje* para el punto de conexión activo actual. El servicio de conserje puede ser una aplicación web simple que se replica y se mantiene accesible mediante técnicas de redirección de DNS (por ejemplo, con el [Azure Traffic Manager][Azure Traffic Manager]).
* **Replicación del registro de identidad**: para que el centro de IoT secundario pueda usarse, debe contener todas las identidades de dispositivo que se pueden conectar a la solución. La solución debe conservar copias de seguridad de replicación geográfica de las identidades de dispositivo y cargarlas en el Centro de IoT secundario antes de cambiar el punto de conexión activo de los dispositivos. La funcionalidad de exportación de identidades de dispositivo de IoT Hub resulta muy útil en este contexto. Para más información, consulte la [guía para desarrolladores de IoT Hub: registro de identidad][IoT Hub Developer Guide - identity registry].
* **Lógica de combinación** : cuando la región primaria vuelve a estar disponible, todos los estados y datos que se crearon en el sitio secundario deben volver a migrarse a la región primaria. Este estado y estos datos tienen que ver principalmente con las identidades de dispositivo y los metadatos de aplicación, que deben combinarse con el centro de IoT principal y con otros almacenes específicos de la aplicación en la región primaria. Para simplificar este paso, debe usar las operaciones de idempotente. Estas minimizan los efectos secundarios de la posible distribución uniforme de eventos y de los duplicados o la entrega desordenada de eventos. Además, la lógica de aplicación debe diseñarse para que tolere posibles incoherencias o un estado "algo" desactualizado. Esta situación se puede producir debido al tiempo adicional que tarda el sistema en "repararse" según los objetivos de punto de recuperación (RPO).

## <a name="next-steps"></a>Pasos siguientes
Siga estos vínculos para obtener más información sobre el Centro de IoT de Azure:

* [Introducción a los centros de IoT (tutorial)][lnk-get-started]
* [¿Qué es Azure IoT Hub?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub Developer Guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md



<!--HONumber=Nov16_HO5-->


