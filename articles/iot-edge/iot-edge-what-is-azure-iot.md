---
title: Soluciones de Azure para Internet de las cosas (IoT Edge) | Microsoft Docs
description: "Información general de una arquitectura de la solución de IoT de ejemplo y cómo se relaciona con dispositivos, el servicio IoT Hub de Azure, SDK de dispositivo IoT de Azure, SDK del servicio IoT de Azure y otros servicios de Azure."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 587b733106d511ec63d71f67a06e520324a3e594
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Pasos siguientes

Azure IoT Edge es un servicio de Azure que permite el análisis y el procesamiento de datos en dispositivos perimetrales. Con IoT Edge puede permitir que los dispositivos con código de contenedor incluyan cualquier lógica extraída directamente desde los servicios de Azure que ya utilice o desde su propio código específico de la solución. Permite a los dispositivos:

* Actuar como dispositivos de puerta de enlace, y agregar y procesar datos desde varios dispositivos de hoja.
* Realizar la detección de anomalías y reaccionar a los cambios en el entorno sin tener que esperar instrucciones de la nube.
* Minimizar los costos de ancho de banda y almacenamiento mediante el procesamiento de los datos y el envío de los resultados. 

IoT Edge también incluye una interfaz en la nube que permite la administración remota de dispositivos. Sin tener que acceder físicamente a los dispositivos, puede implementar código, y supervisar el estado y actualizarlo. Puede administrar dispositivos únicos o crear implementaciones que afecten a grandes conjuntos de dispositivos que defina de manera remota. Para más información, consulte el artículo de [información sobre las implementaciones de IoT Edge para dispositivos únicos o a escala][lnk-deployment].

Para información acerca de los componentes que habilitan IoT Edge, consulte el artículo sobre el [funcionamiento de Azure IoT Edge][lnk-overview].

Si no ha utilizado antes Azure IoT Hub, puede comenzar con [Introducción al servicio Azure IoT Hub][lnk-iot-hub].

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
