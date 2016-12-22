---
title: "Métricas de diagnóstico del Centro de IoT"
description: "Información general de las métricas de Azure IoT Hub que permite evaluar el estado general de los recursos"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: ce514e19370d2b42fb16b4e96b66f212d5fa999c
ms.openlocfilehash: e2739f9f47495c4502af3aff95d2d3f3975a881b


---
# <a name="introduction-to-diagnostic-metrics"></a>Introducción a las métricas de diagnóstico
Las métricas de diagnóstico le ofrecen mejores datos sobre el estado de los recursos de Azure en la suscripción de Azure. Las métricas permiten evaluar el estado general del servicio y de los dispositivos conectados a él. Las estadísticas orientadas al usuario son importantes porque ayudan a ver lo que está ocurriendo con su Centro de IoT y ayudan a determinar la causa raíz de los problemas sin necesidad de ponerse en contacto con el soporte técnico de Azure.

Puede habilitar las métricas de diagnóstico desde el Portal de Azure.

## <a name="how-to-enable-diagnostic-metrics"></a>Habilitación de las métricas de diagnóstico
1. Cree un Centro de IoT. Puede encontrar instrucciones sobre cómo crear un centro de IoT en la guía [Introducción][lnk-get-started].
2. Abra la hoja de su Centro de IoT. Desde allí, haga clic en **Diagnósticos**.
   
    ![][1]
3. Configure el diagnóstico estableciendo el estado en **Activado** y seleccionando una cuenta de Azure Storage para almacenar los datos de diagnóstico. Active **Métricas** y presione **Guardar**. Recuerde que la cuenta de Azure Storage se debe crear con antelación y que se le cobrará el almacenamiento por separado. También puede elegir enviar los datos de diagnóstico a un punto de conexión de centros de eventos.
   
    ![][2]
4. Una vez haya configurado los diagnósticos, vuelva a la **Información general** del Centro de IoT. La información de las métricas se rellena en la sección **Supervisión** de la hoja. Al hacer clic en el gráfico, se abre el panel de métricas, donde puede ver un resumen de la información de las métricas para el centro de IoT. Puede modificar la selección de métricas que se muestran en el gráfico y configurar alertas basadas en valores de métrica.
   
    ![][3]

## <a name="metrics-and-how-to-use-them"></a>Las métricas y su uso
IoT Hub proporciona varias métricas para ofrecerle una visión general del estado de su centro y el número total de dispositivos conectados. Puede combinar información de varias métricas para conseguir una imagen más amplia del estado del Centro de IoT. La tabla siguiente describe las métricas de las que cada Centro de IoT realiza un seguimiento y cómo se relaciona cada métrica con el estado general del Centro de IoT.

| Métrica | Descripción de la métrica | Para qué se usa la métrica |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol |Número de mensajes enviados en todos los dispositivos |Datos de información general sobre el envío de mensajes |
| d2c.telemetry.ingress.success |Número de mensajes completados correctamente en el centro de IoT |Información general de las recepciones de mensajes completadas correctamente en el centro de IoT |
| c2d.commands.egress.complete.success |Número de todos los mensajes de comando completados por el dispositivo receptor en todos los dispositivos |Junto con las métricas de abandono y rechazo, ofrece una visión general de la tasa de éxito global de comandos de nube a dispositivo |
| c2d.commands.egress.abandon.success |Número de todos los mensajes abandonados correctamente por el dispositivo receptor en todos los dispositivos |Destaca los posibles problemas si se están abandonando mensajes con una frecuencia superior a la esperada |
| c2d.commands.egress.reject.success |Número de todos los mensajes rechazados correctamente por el dispositivo receptor en todos los dispositivos |Destaca los posibles problemas si se están rechazando mensajes con una frecuencia superior a la esperada |
| devices.totalDevices |Promedio, mínimo y máximo de dispositivos registrados en el Centro de IoT |Número de dispositivos registrados en el centro de IoT |
| devices.connectedDevices.allProtocol |Promedio, mínimo y máximo de dispositivos conectados simultáneamente |Información general sobre el número de dispositivos conectados al centro de IoT |

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha visto la información general sobre las métricas de diagnóstico, siga este vínculo para más información acerca de la administración de IoT Hub de Azure:

* [Supervisión de operaciones][lnk-monitor]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Guía del desarrollador][lnk-devguide]
* [Simulación de un dispositivo con el SDK de puerta de enlace de IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO5-->


