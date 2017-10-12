---
title: Soluciones de Azure para Internet de las cosas (IoT Suite) | Microsoft Docs
description: "Descripción general de IoT en Azure, lo que incluye la arquitectura de una solución de ejemplo y cómo se relaciona con el Conjunto de aplicaciones de IoT de Azure y las soluciones preconfiguradas."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 437d2655-896f-4a9e-a4a8-b864790d3ef8
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 320190488bb4c7b8192421f9dd50a5264f558584
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="azure-iot-suite"></a>Conjunto de aplicaciones de IoT de Azure
El Conjunto de aplicaciones de IoT de Microsoft Azure es una solución de clase empresarial que le permite comenzar a trabajar rápidamente mediante un conjunto de soluciones preconfiguradas extensibles. Estas soluciones abordan escenarios de IoT habituales, como la [supervisión remota][lnk-preconfigured-solutions], el [mantenimiento predictivo][lnk-predictive-maintenance], y la [fábrica conectada][lnk-connected-factory]. Estas soluciones son implementaciones de la arquitectura de la solución de IoT descrita en este artículo.

Las soluciones preconfiguradas son soluciones completas, integrales y funcionales que incluyen:

- Dispositivos simulados para que pueda empezar a usarla.
- Servicios de Azure configurados previamente como [Azure IoT Hub][Azure IoT Hub], [Azure Event Hubs][Azure Event Hubs], [Azure Stream Analytics][Azure Stream Analytics], [Azure Machine Learning][Azure Machine Learning] y [Azure Storage][Azure storage].
- Consolas de administración específicas de la solución.

Contienen código comprobado y listo para producción que puede personalizar y ampliar para implementar sus propios escenarios IoT específicos.

También es posible que le interese el servicio [Azure IoT Hub][Azure IoT Hub] que usan muchas de las soluciones preconfiguradas. [Azure IoT Hub][Azure IoT Hub] ofrece las comunicaciones bidireccionales entre dispositivos y la nube seguras y fiables que se utilizan en la arquitectura de la solución preconfigurada.

## <a name="next-steps"></a>Pasos siguientes
Explore estos recursos para más información acerca del Conjunto de aplicaciones de IoT y las soluciones preconfiguradas:

* [¿Qué es el Conjunto de aplicaciones de IoT de Azure?][lnk-whatissuite]
* [¿Qué son las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure?][lnk-whatarepreconfigured]

[lnk-whatissuite]: iot-suite-overview.md
[lnk-whatarepreconfigured]: iot-suite-what-are-preconfigured-solutions.md

[lnk-preconfigured-solutions]: iot-suite-getstarted-preconfigured-solutions.md
[Azure IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[Azure Machine Learning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-connected-factory]: iot-suite-connected-factory-overview.md