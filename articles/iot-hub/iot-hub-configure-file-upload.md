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
ms.date: 01/05/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: aadbee1388e2c2aafaf0a5c50d2af195284d2bec


---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configuración de cargas de archivos de IoT Hub mediante Azure Portal
## <a name="file-upload"></a>Carga de archivos
Para utilizar la [funcionalidad de carga de archivos en el centro de IoT Hub][lnk-upload], primero debe asociar una cuenta de Azure Storage con su centro. Seleccione la configuración de **carga de archivos** para mostrar una lista de propiedades de carga de archivos para el Centro de IoT que se modifica.

![][13]

**Contenedor de almacenamiento:** Use Azure Portal para seleccionar un contenedor de blobs en una cuenta de Azure Storage de su suscripción actual de Azure con el fin de asociarlo a su IoT Hub. Si es necesario, puede crear una cuenta de Azure Storage en la hoja **Cuentas de almacenamiento** y el contenedor de blobs en la hoja **Contenedores**. El Centro de IoT genera automáticamente identificadores URI de SAS con permisos de escritura en este contenedor de blobs para los dispositivos que se utilizarán cuando se carguen archivos.

![][14]

**Receive notifications for uploaded files**(Recibir notificaciones para archivos cargados): habilite o deshabilite las notificaciones de carga de archivos mediante el botón de alternancia.

**SAS TTL**(TTL SAS): este valor es el periodo de vida de los URI de SAS que el Centro de IoT devuelve al dispositivo. Se establece en una hora de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.

**File notification settings default TTL**(TTL predeterminado de configuración de notificación de archivos): el periodo de vida de una notificación de carga de archivos antes de que caduque. Se establece en un día de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.

**File notification maximum delivery count**(Número máximo de entregas de notificaciones de archivo): el número de veces que el Centro de IoT tratará de entregar una notificación de carga de archivos. Se establece en 10 días de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.

![][15]

## <a name="next-steps"></a>Pasos siguientes
Para información sobre las funcionalidades de carga archivos de IoT Hub, consulte [Upload files from a device][lnk-upload] (Carga de archivos desde un dispositivo) en la guía para desarrolladores.

Siga estos vínculos para más información sobre la administración del Centro de IoT de Azure:

* [Administración masiva de dispositivos de IoT][lnk-bulk]
* [Métricas de IoT Hub][lnk-metrics]
* [Supervisión de operaciones][lnk-monitor]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Simulación de un dispositivo con el SDK de puerta de enlace de IoT][lnk-gateway]
* [Protección total de la solución de IoT][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md



<!--HONumber=Dec16_HO1-->


