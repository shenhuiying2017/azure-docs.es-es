---
title: Escalado de Azure IoT Hub | Microsoft Docs
description: Cómo escalar el centro de IoT Hub para respaldar el rendimiento de mensajes previsto y características deseadas. Incluye un resumen del rendimiento admitido para cada nivel y opciones de particionamiento.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f3237ee41f8596ed3ce508857adf7dc29cee1ada
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Elección del nivel adecuado de IoT Hub para la solución

Cada solución de IoT es diferente, por tanto Azure IoT Hub ofrece varias opciones en función del precio y la escala. El objetivo de este artículo es ayudarle a evaluar sus necesidades de IoT Hub. Para más información sobre los niveles de IoT Hub, consulte [Precios de IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub). 

Para decidir qué nivel de IoT Hub es el adecuado para la solución, hágase dos preguntas:

**¿Qué características voy a utilizar?**
Azure IoT Hub ofrece dos niveles, Basic y Estándar, que se diferencian en el número de características que admiten. Si la solución de IoT se basa en la recopilación de datos de los dispositivos y en su análisis de forma centralizada, el nivel Basic es probablemente el más adecuado. Si desea utilizar configuraciones más avanzadas para controlar dispositivos IoT de forma remota o distribuir algunas de las cargas de trabajo en los mismos dispositivos, debería considerar el nivel Estándar. Para obtener un análisis detallado de las características que se incluyen en cada nivel, continúe a la sección sobre los [niveles Basic y Estándar](#basic-and-standard-tiers).

**¿Qué cantidad de datos tiene previsto mover cada día?**
Cada nivel del IoT Hub está disponible en tres tamaños, basados en la cantidad de datos que pueden tratar en un día determinado. Estos tamaños se identifican numéricamente como 1, 2 y 3. Por ejemplo, cada unidad de un centro de IoT de nivel 1 puede controlar 400 000 mensajes al día, mientras que una unidad de nivel 3 puede controlar 300 millones. Para más información acerca de las directrices de datos, vaya a la sección [Rendimiento de mensajes](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Niveles Basic y Estándar

El nivel estándar de IoT Hub permite todas las características y es necesario para cualquier solución de IoT que desee hacer uso de las funcionalidades de comunicación bidireccional. El nivel Basic permite un subconjunto de las características y está pensado para las soluciones de IoT que solo necesitan comunicación unidireccional de los dispositivos a la nube. Ambos niveles ofrecen las mismas características de seguridad y autenticación.

Una vez creado el centro de IoT, puede actualizarlo desde el nivel Basic al nivel Estándar sin interrumpir las operaciones existentes. Para más información, consulte [How to upgrade your IoT hub](iot-hub-upgrade.md) (Actualización de IoT Hub).

| Capacidad | Nivel Basic | Nivel Standard |
| ---------- | ---------- | ------------- |
| [Telemetría del dispositivo a la nube](iot-hub-devguide-messaging.md) | Sí | Sí |
| [Identidad por dispositivo](iot-hub-devguide-identity-registry.md) | Sí | Sí |
| [Enrutamiento de mensajes](iot-hub-devguide-messages-read-custom.md) e [integración con Event Grid](iot-hub-event-grid.md) | Sí | Sí |
| [Protocolos HTTP, AMQP y MQTT](iot-hub-devguide-protocols.md) | Sí | Sí |
| [Servicio Device Provisioning](../iot-dps/about-iot-dps.md) | Sí | Sí |
| [Supervisión y diagnóstico](iot-hub-monitor-resource-health.md) | Sí | Sí |
| [Mensajería de la nube a un dispositivo](iot-hub-devguide-c2d-guidance.md) |   | Sí |
| [Dispositivos gemelos](iot-hub-devguide-device-twins.md) y [Administración de dispositivos](iot-hub-device-management-overview.md) |   | Sí |
| [Azure IoT Edge](../iot-edge/how-iot-edge-works.md) |   | Sí |

IoT Hub también ofrece un nivel gratis que está diseñado para pruebas y evaluación. Tiene todas las capacidades del nivel estándar, pero las concesiones de mensajería son limitadas. No puede actualizar desde el nivel gratis al plan Básico o Estándar. 

### <a name="iot-hub-rest-apis"></a>API REST de IoT Hub

La diferencia de funcionalidades admitidas entre los niveles Basic y Estándar de IoT Hub significa que algunas llamadas a la API no funcionan con centros de nivel Basic. En la tabla siguiente se muestran las API que están disponibles: 

| API | Nivel Basic | Nivel Standard |
| --- | ---------- | ------------- |
| [Eliminar un dispositivo](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice) | Sí | Sí |
| [Obtener dispositivo](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice) | Sí | Sí |
| [Obtener estadísticas del registro](https://docs.microsoft.com/rest/api/iothub/deviceapi/getregistrystatistics) | Sí | Sí |
| [Obtener estadísticas de servicios](https://docs.microsoft.com/rest/api/iothub/deviceapi/getservicestatistics) | Sí | Sí |
| [Colocar dispositivo](https://docs.microsoft.com/rest/api/iothub/deviceapi/putdevice) | Sí | Sí |
| [Consultar dispositivos](https://docs.microsoft.com/rest/api/iothub/deviceapi/querydevices) | Sí | Sí |
| [Crear el URI de SAS de carga de archivos](https://docs.microsoft.com/rest/api/iothub/httpruntime/createfileuploadsasuri) | Sí | Sí |
| [Recibir notificación de dispositivo enlazado](https://docs.microsoft.com/rest/api/iothub/httpruntime/receivedeviceboundnotification) | Sí | Sí |
| [Enviar evento de dispositivo](https://docs.microsoft.com/rest/api/iothub/httpruntime/senddeviceevent) | Sí | Sí |
| [Actualizar estado de la carga de archivo](https://docs.microsoft.com/rest/api/iothub/httpruntime/updatefileuploadstatus) | Sí | Sí |
| [Operación de dispositivos en bloque](https://docs.microsoft.com/rest/api/iothub/deviceapi/bulkdeviceoperation) | Sí, excepto las funcionalidades de IoT Edge | Sí | 
| [Purgar cola de comandos](https://docs.microsoft.com/rest/api/iothub/deviceapi/purgecommandqueue) |   | Sí |
| [Obtener dispositivo gemelo](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/getdevicetwin) |   | Sí |
| [Invocar método de dispositivo](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/invokedevicemethod) |   | Sí |
| [Actualizar dispositivo gemelo](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/updatedevicetwin) |   | Sí | 
| [Abandonar notificación de dispositivo enlazado](https://docs.microsoft.com/rest/api/iothub/httpruntime/abandondeviceboundnotification) |   | Sí |
| [Completar notificación de dispositivo enlazado](https://docs.microsoft.com/rest/api/iothub/httpruntime/completedeviceboundnotification) |   | Sí |
| [Cancelar trabajo](https://docs.microsoft.com/rest/api/iothub/jobapi/canceljob) |   | Sí |
| [Crear trabajo](https://docs.microsoft.com/rest/api/iothub/jobapi/createjob) |   | Sí |
| [Obtener trabajo](https://docs.microsoft.com/rest/api/iothub/jobapi/getjob) |   | Sí |
| [Consultar trabajos](https://docs.microsoft.com/rest/api/iothub/jobapi/queryjobs) |   | Sí |

## <a name="message-throughput"></a>Rendimiento de mensajes

La mejor forma de dimensionar una solución de IoT Hub es evaluar el tráfico en cada dispositivo. En concreto, tenga en cuenta la capacidad de procesamiento máxima requerida para las siguientes categorías de operaciones:

* Mensajes de dispositivo a nube
* Mensajes de nube a dispositivo
* Operaciones de registro de identidad

El tráfico se mide por unidad, no por centro. Una instancia de IoT Hub de nivel 1 o 2 puede tener hasta 200 unidades asociadas. Una instancia de IoT Hub de nivel 3 puede tener hasta 10 unidades. Una vez que creado el IoT Hub, puede cambiar el número de unidades o moverse entre los tamaños 1, 2 y 3 dentro de un nivel específico sin interrumpir las operaciones existentes. Para más información, consulte [How to upgrade your IoT hub](iot-hub-upgrade.md) (Actualización de IoT Hub).

Como ejemplo de las funcionalidades de tráfico de cada nivel, los mensajes del dispositivo a la nube siguen estas directrices de rendimiento sostenidas:

| Nivel: | Capacidad de procesamiento sostenida | Velocidad de envío sostenida |
| --- | --- | --- |
| B1, S1 |Hasta 1111 KB/minuto por unidad<br/>(1,5 GB/día/unidad) |Promedio de 278 mensajes/minuto por unidad<br/>(400 000 mensajes/día por unidad) |
| B2, S2 |Hasta 16 MB/minuto por unidad<br/>(22,8 GB/día/unidad) |Promedio de 4167 mensajes/minuto por unidad<br/>(6 millones de mensajes/día por unidad) |
| B3, S3 |Hasta 814 MB/minuto por unidad<br/>(1144,4 GB/día/unidad) |Promedio de 208.333 mensajes/minuto por unidad<br/>(300 millones de mensajes/día por unidad) |

Además de esta información sobre la capacidad de procesamiento, vea [Cuotas y limitaciones de IoT Hub][IoT Hub quotas and throttles] y diseñe su solución en consecuencia.

### <a name="identity-registry-operation-throughput"></a>Capacidad de procesamiento para las operaciones de registro de identidad
Las operaciones de registro de identidad de IoT Hub no deberían ser operaciones en tiempo de ejecución porque tienen que ver principalmente con el aprovisionamiento de dispositivos.

Vea las cifras de rendimiento de ráfaga específicas en [Cuotas y limitaciones de IoT Hub][IoT Hub quotas and throttles].

## <a name="sharding"></a>Clave de particionamiento
Aunque un único Centro de IoT puede escalarse a millones de dispositivos, a veces la solución requiere características de rendimiento específicas que un único Centro de IoT no puede garantizar. En ese caso puede repartir los dispositivos en varios centros de IoT. Varios centros de IoT suavizan las ráfagas de tráfico y obtienen el rendimiento necesario o las velocidades de funcionamiento necesarias.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre las funcionalidades de IoT Hub y detalles de rendimiento, consulte [Precios de IoT Hub] [vínculo a precios] o [Cuotas y limitación de IoT Hub][IoT Hub quotas and throttles].
* Para cambiar el nivel de IoT Hub, siga los pasos descritos en el artículo sobre cómo [actualizar IoT Hub](iot-hub-upgrade.md).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
