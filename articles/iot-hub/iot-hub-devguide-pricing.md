---
title: "Información sobre los precios de IoT Hub de Azure | Microsoft Docs"
description: "Guía del desarrollador: información sobre cómo funcionan los precios y la medición con IoT Hub (se incluyen ejemplos)."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: elioda
ms.openlocfilehash: 05006a78cc7d82bc048ec5706465f7140eb40e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-hub-pricing-information"></a>Información de precios de IoT Hub de Azure

[Precios de Azure IoT Hub][lnk-pricing] proporciona información general sobre diferentes SKU y precios de IoT Hub. Este artículo contiene información adicional acerca de cómo se mide el uso de las distintas funcionalidades de IoT Hub como mensajes de IoT Hub.

## <a name="charges-per-operation"></a>Cargos por operación

| Operación | Información de facturación | 
| --------- | ------------------- |
| Operaciones de registro de identidad <br/> (crear, recuperar, enumerar, actualizar, eliminar) | No se aplicará ningún cargo. |
| Mensajes de dispositivo a nube | Los mensajes enviados correctamente se cobran en fragmentos de 4 KB por cada ingreso en IoT Hub; por ejemplo, un mensaje de 6 KB se cobra como dos mensajes. |
| Mensajes de nube a dispositivo | Los mensajes enviados correctamente se cobran en fragmentos de 4 KB; por ejemplo, un mensaje de 6 KB se cobra como dos mensajes. |
| Cargas de archivos | IoT Hub no mide el uso de la transferencia de archivos a Azure Storage. Los mensajes de inicio y finalización de transferencia de archivos se cobran como mensajes medidos en incrementos de 4 KB. Por ejemplo, por transferir un archivo de 10 MB se cobra el precio de dos mensajes, además del costo de Azure Storage. |
| Métodos directos | Las solicitudes de métodos correctas se cobrarán en fragmentos de 4 KB; las respuestas con cuerpos no vacíos se cobran en fragmentos de 4 KB como mensajes adicionales. Las solicitudes a dispositivos desconectados se cobran como mensajes en fragmentos de 4 KB. Por ejemplo, un método con un cuerpo de 6 KB que da como resultado una respuesta sin cuerpo desde el dispositivo se cobra como dos mensajes; un método con un cuerpo de 6 KB que da como resultado una respuesta de 1 KB desde el dispositivo se cobra como dos mensajes para la solicitud más otro mensaje para la respuesta. |
| Lecturas de dispositivos gemelos | Las lecturas de dispositivos gemelos desde el dispositivo y desde el back-end de solución se cobran como mensajes en fragmentos de 512 bytes. Por ejemplo, la lectura de un dispositivo gemelo de 6 KB se cobra como 12 mensajes. |
| Actualizaciones de dispositivos gemelos (etiquetas y propiedades) | Las actualizaciones de dispositivos gemelos desde el dispositivo y desde el back-end de solución se cobran como mensajes en fragmentos de 512 bytes. Por ejemplo, la lectura de un dispositivo gemelo de 6 KB se cobra como 12 mensajes. |
| Consultas de dispositivos gemelos | Las consultas se cobran como mensajes según el tamaño de los resultados en fragmentos de 512 bytes. |
| Operaciones de trabajos <br/> (crear, actualizar, enumerar, eliminar) | No se aplicará ningún cargo. |
| Operaciones por dispositivo de trabajos | Las operaciones de trabajos (como actualizaciones de dispositivos gemelos y métodos) se cobran del modo habitual. Por ejemplo, un trabajo que dé como resultado 1000 llamadas de método con solicitudes de 1 KB y respuestas con cuerpo vacío se cobra como 1000 mensajes. |

> [!NOTE]
> Todos los tamaños se calculan teniendo en cuenta el tamaño de carga en bytes (se omiten las tramas de protocolo). En el caso de los mensajes (que tienen propiedades y cuerpo), el tamaño se calcula de una manera independiente del protocolo, tal como se describe en la sección sobre el [tamaño de los mensajes de IoT Hub de la guía del desarrollador][lnk-message-size].

## <a name="example-1"></a>Ejemplo 1

Un dispositivo envía un mensaje de dispositivo a nube de 1 KB por minuto a IoT Hub, que Azure Stream Analytics lee después. El back-end de solución invoca un método (con una carga útil de 512 bytes) en el dispositivo cada diez minutos para desencadenar una acción específica. El dispositivo responde al método con un resultado de 200 bytes.

El dispositivo consume 1 mensaje * 60 minutos * 24 horas = 1440 mensajes por día para los mensajes del dispositivo a la nube y 2 solicitudes más la respuesta * 6 veces por hora * 24 horas = 288 mensajes para los métodos, con un total de mensajes de 1728 al día.

## <a name="example-2"></a>Ejemplo 2

Un dispositivo envía un mensaje del dispositivo a la nube de 100 KB cada hora. También actualiza su dispositivo gemelo con cargas útiles de 1 KB cada cuatro horas. El back-end de solución, una vez al día, lee el dispositivo gemelo de 14 KB y lo actualiza con cargas útiles de 512 bytes para cambiar las configuraciones.

El dispositivo consume 25 mensajes (100 KB / 4 KB) * 24 horas para los mensajes de dispositivos en la nube, además de 2 mensajes (1 KB / 0,5 KB) * 6 veces al día para las actualizaciones de dispositivos gemelos, con un total de 612 mensajes al día.
El back-end de solución consume 28 mensajes (14 kB/0,5 kB) para leer el dispositivo gemelo, además de 1 mensaje para actualizarlo, con un total de 29 mensajes.

En total, el dispositivo y el back-end de solución consumen 641 mensajes al día.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md
