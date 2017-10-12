---
title: "Información general de Conjunto de aplicaciones de IoT de Microsoft Azure | Microsoft Docs"
description: "Información general sobre cómo el Conjunto de aplicaciones de IoT de Azure ofrece soluciones preconfiguradas de Internet de las cosas para recopilar, analizar y almacenar datos, proporcionar visualizaciones e integrarlos con otros sistemas."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfa8dbbd0b1d943a9eb7a042df0bac25189d9ac9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-iot-suite"></a>Información general de Azure IoT Suite

Los servicios de Internet de las cosas (IoT) de Azure ofrecen una amplia gama de funcionalidades. Estos servicios de nivel empresarial le permiten:

* Recopilar datos de dispositivos
* Analizar flujos de datos en movimiento
* Almacenar y consultar grandes conjuntos de datos
* Visualizar datos tanto históricos como en tiempo real
* Integración con sistemas del área de operaciones
* Administración de los dispositivos

Con el fin de ofrecer estas funcionalidades, el Conjunto de aplicaciones de IoT de Azure ofrece paquetes de múltiples servicios de Azure con extensiones personalizadas como *soluciones preconfiguradas*. Estas soluciones preconfiguradas son implementaciones base de patrones comunes de soluciones de IoT que le ayudan a reducir el tiempo que dedica a entregar sus soluciones de IoT. Con los [kits de desarrollo de software de IoT][lnk-sdks], puede personalizar y extender estas soluciones para satisfacer sus requisitos. También puede usar estas soluciones como ejemplos o plantillas al desarrollar nuevas soluciones de IoT.

El vídeo siguiente proporciona una introducción al conjunto de aplicaciones de IoT de Azure:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## <a name="azure-iot-services-in-azure-iot-suite"></a>Servicios IoT de Azure en Azure IoT Suite
Normalmente, las soluciones preconfiguradas usan los siguientes servicios:

* El componente esencial del Conjunto de aplicaciones de IoT de Azure es el servicio [Azure IoT Hub][lnk-iot-hub]. Este servicio ofrece las capacidades de mensajería de dispositivo a la nube y de la nube al dispositivo y actúa como la puerta de enlace para la nube y los demás servicios del Conjunto de aplicaciones de IoT clave. El servicio le permite recibir mensajes de los dispositivos, a escala, y enviar comandos a los dispositivos. El servicio también le permite [administrar los dispositivos][lnk-device-management]. Por ejemplo, puede configurar, reiniciar o realizar un restablecimiento de fábrica de uno o varios dispositivos conectados al centro.
* [Azure Stream Analytics][lnk-asa] ofrece análisis de datos en movimiento. El Conjunto de aplicaciones de IoT usa este servicio para procesar la telemetría entrante, realizar la agregación y detectar eventos. Las soluciones preconfiguradas también usan el análisis de transmisiones para procesar los mensajes informativos que contienen datos como los metadatos y las respuestas de comandos de dispositivos. Las soluciones usan Análisis de transmisiones para procesar los mensajes de los dispositivos y entregarlos a otros servicios.
* [Azure Storage][lnk-azure-storage] y [Azure Cosmos DB][lnk-document-db] ofrecen las funcionalidades de almacenamiento de datos. Las soluciones preconfiguradas usan el almacenamiento de blobs para almacenar la telemetría y que esté disponible para análisis. Las soluciones usan Cosmos DB para almacenar los metadatos de dispositivo y habilitar la funcionalidad de administración de dispositivos de las soluciones.
* [Azure Web Apps][lnk-web-apps] y [Microsoft Power BI][lnk-power-bi] ofrecen las funcionalidades de visualización de datos. La flexibilidad de Power BI le permite compilar rápidamente sus propios paneles interactivos que usan los datos del conjunto de aplicaciones de IoT.

Para información general de la arquitectura de una solución de IoT típica, consulte [Microsoft Azure e Internet de las cosas (IoT)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>soluciones preconfiguradas

El Conjunto de aplicaciones de IoT incluye soluciones preconfiguradas que le permiten empezar a trabajar rápidamente con los escenarios comunes de IoT, como:

* Supervisión remota
* Mantenimiento predictivo
* Fábrica conectada

Puede implementar estas soluciones en su suscripción de Azure y luego ejecutar un escenario de IoT completo e integral.

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una visión general de lo que puede hacer el Conjunto de aplicaciones de IoT y cuáles son sus componentes principales, puede obtener más información acerca de las soluciones preconfiguradas que incluye. Para más información, consulte [¿Cuáles son las soluciones preconfiguradas de IoT de Azure?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
