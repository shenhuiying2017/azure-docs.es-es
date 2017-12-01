---
title: Uso de Azure Portal para configurar la carga de archivos | Microsoft Docs
description: "Describe cómo usar Azure Portal para configurar el centro de IoT Hub con el fin de habilitar las cargas de archivo desde dispositivos conectados. Incluye información sobre cómo configurar la cuenta de Azure Storage."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: 2d875947297be5d47362369b96bc6ab0d90b3c93
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configuración de cargas de archivos de IoT Hub mediante Azure Portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Carga de archivos

Para utilizar la [funcionalidad de carga de archivos en el centro de IoT Hub][lnk-upload], primero debe asociar una cuenta de Azure Storage con su centro. Seleccione **Carga de archivos** para mostrar una lista de las propiedades de carga de archivos para el IoT Hub que se está modificando.

![Visualización de la configuración de carga de archivos de IoT Hub en el portal][13]

**Contenedor de almacenamiento:** Use Azure Portal para seleccionar un contenedor de blobs en una cuenta de Azure Storage de su suscripción actual de Azure con el fin de asociarlo a su IoT Hub. Si es necesario, puede crear una cuenta de Azure Storage en la hoja **Cuentas de almacenamiento** y el contenedor de blobs en la hoja **Contenedores**. IoT Hub genera automáticamente identificadores URI de SAS con permisos de escritura en este contenedor de blobs para los dispositivos que se utilizarán cuando se carguen archivos.

![Visualización de contenedores de almacenamiento para la carga de archivos en el portal][14]

**Receive notifications for uploaded files**(Recibir notificaciones para archivos cargados): habilite o deshabilite las notificaciones de carga de archivos mediante el botón de alternancia.


            **SAS TTL**(TTL SAS): este valor es el periodo de vida de los URI de SAS que Azure IoT Hub devuelve al dispositivo. Se establece en una hora de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.

**File notification settings default TTL**(TTL predeterminado de configuración de notificación de archivos): el periodo de vida de una notificación de carga de archivos antes de que caduque. Se establece en un día de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.


            **File notification maximum delivery count**(Número máximo de entregas de notificaciones de archivo): el número de veces que Azure IoT Hub tratará de entregar una notificación de carga de archivos. Se establece en 10 días de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.

![Configuración de la carga de archivos de IoT Hub en el portal][15]

## <a name="next-steps"></a>Pasos siguientes

Para información sobre las funcionalidades de carga archivos de IoT Hub, consulte [Upload files from a device][lnk-upload] (Carga de archivos desde un dispositivo) en la guía para desarrolladores.

Siga estos vínculos para más información sobre la administración de Azure IoT Hub:

* [Administración masiva de dispositivos de IoT][lnk-bulk]
* [Métricas de IoT Hub][lnk-metrics]
* [Supervisión de operaciones][lnk-monitor]

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Implementación de Azure IoT Edge en un dispositivo simulado en Linux: versión preliminar][lnk-iotedge]
* [Protección total de la solución de IoT][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
