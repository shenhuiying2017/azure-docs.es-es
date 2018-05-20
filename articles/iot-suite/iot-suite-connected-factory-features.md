---
title: 'Características de la solución Connected Factory: Azure | Microsoft Docs'
description: Introducción a las características de la solución preconfigurada de Connected Factory.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: e0bd023e73088897303fe0f9de47641cd6ea0389
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-iot-suite-connected-factory"></a>¿Qué es Connected Factory de Azure IoT Suite?

Connected Factory es una implementación de la arquitectura de referencia de IoT industrial de Azure de Microsoft, empaquetada como solución de código abierto. Puede utilizarla como punto inicial para un producto comercial. Puede implementar una versión pregenerada de la solución Connected Factory en la suscripción de Azure de [Azure IoT Suite](https://www.azureiotsuite.com/#solutions/types/CF).

![Panel de la solución Connected Factory](media/iot-suite-connected-factory-features/dashboard.png)

Connected Factory incluye las siguientes características:

## <a name="industrial-device-interoperability"></a>Interoperabilidad con dispositivos industriales

- Conexión con recursos industriales gracias a la interfaz de OPC UA.
- Líneas de producción simuladas (que ejecutan servidores de OPC UA en contenedores de Docker) para ver la telemetría en vivo.
- Examen del modelo de información de OPC UA de los servidores de OPC UA desde un panel en la nube.

## <a name="remote-management"></a>Administración remota

- Configuración de los recursos de OPC UA desde el panel en la nube (métodos de llamada, lectura y escritura de datos).
- Publicación y anulación de la publicación de datos de telemetría de los recursos de OPC UA desde un panel en la nube.

## <a name="cloud-dashboard"></a>Panel en la nube

- Visualización de vistas previas de telemetría directamente en un panel en la nube.
- Visualización de las tendencias en los datos de telemetría y creación de correlaciones con el panel del Explorador de Time Series Insights.
- Visualización del valor de la eficiencia general del equipo (OEE) calculado y los indicadores clave de rendimiento (KPI) desde un panel en la nube.
- Visualización de las jerarquías de los recursos industriales en topología de árbol y en mapa interactivo.
- Visualización, confirmación y cierre de alertas desde un panel en la nube.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) se ha compilado para almacenar, visualizar y consultar grandes cantidades de datos de series temporales. Connected Factory aprovecha este servicio.
- Factoría conectada se integra con este servicio para permitir los análisis profundos y en tiempo real de los datos del dispositivo.

## <a name="rules-and-alerts"></a>Reglas y alertas

[Configuración de las reglas basadas en umbrales para las alertas](iot-suite-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Seguridad de un extremo a otro

- Configuración de permisos de seguridad para los usuarios con el control de acceso basado en rol (RBAC).
- El cifrado de un extremo a otro se implementa mediante la autenticación OPC UA (con certificados X.509), así como con tokens de seguridad.

## <a name="customizability"></a>Personalización

- [Personalización](iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md) de la solución para satisfacer requisitos empresariales específicos.
- Código fuente de la solución completo disponible en GitHub. Consulte el repositorio de la [solución preconfigurada Factoría conectada](https://github.com/Azure/azure-iot-connected-factory).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la solución preconfigurada Factoría conectada, lea los siguientes artículos:

* [Tutorial de la solución preconfigurada Factoría conectada](iot-suite-connected-factory-sample-walkthrough.md)
* [Implementación de una puerta de enlace para Factoría conectada]( iot-suite-connected-factory-gateway-deployment.md)
