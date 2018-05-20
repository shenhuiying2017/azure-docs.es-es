---
title: 'Supervisión avanzada en la solución de supervisión remota: Azure | Microsoft Docs'
description: En este tutorial se muestra cómo supervisar los dispositivos con el panel de la solución de supervisión remota.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 63d5d8de82d97e7f8ca65ad04cdd4357cace0be1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Supervisión avanzada mediante la solución de supervisión remota

En este tutorial se muestran las funcionalidades del panel de supervisión remota. Para introducir estas funcionalidades, en el tutorial se usa un escenario de la aplicación IoT de Contoso.

En este tutorial, se usan dos dispositivos de camión simulados de Contoso para saber cómo supervisar los dispositivos desde el panel del acelerador de la solución. Como operador de Contoso, debe supervisar la ubicación y el comportamiento de los camiones en el campo.

En este tutorial, aprenderá a:

>[!div class="checklist"]
> * Filtrar los dispositivos en el panel
> * Vista de la telemetría en tiempo real
> * Vista de detalles de dispositivo
> * Vista de las alertas de los dispositivos
> * Ver los KPI del sistema

## <a name="prerequisites"></a>requisitos previos

Para seguir este tutorial, necesitará una instancia implementada de la solución de supervisión remota en la suscripción de Azure.

Si aún no ha implementado la solución de supervisión remota, debe completar el tutorial [Implementación del acelerador de la solución de supervisión remota](iot-suite-remote-monitoring-deploy.md).

## <a name="choose-the-devices-to-display"></a>Elección de los dispositivos que se van a mostrar

Para seleccionar los dispositivos que se muestran en la página **Panel**, use los filtros. Para mostrar solo los dispositivos **Camión**, elija el filtro **Camiones** integrado en el menú desplegable de filtros:

![Filtro de los camiones en el panel](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

Cuando aplica un filtro, solo los dispositivos que coinciden con las condiciones de filtro aparecen en el mapa de la página **Panel**:

![Los camiones se muestran en el mapa](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

El filtro también determina cuáles son los dispositivos que ve en el gráfico **Telemetría**:

![La telemetría del camión aparece en el panel](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

Para crear, editar y eliminar filtros, elija **Administrar filtros**.

## <a name="view-real-time-telemetry"></a>Vista de la telemetría en tiempo real

El acelerador de la solución traza los datos de telemetría en tiempo real en el gráfico de la página **Panel**. El gráfico de telemetría muestra información sobre la telemetría de los dispositivos seleccionados con el filtro actual:

![Trazado de la telemetría del camión](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

Para seleccionar los valores de la telemetría que se van a ver, elija el tipo de telemetría en la parte superior del gráfico:

![Trazado de la telemetría del camión](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

<!-- 05/01 - this features appears to have been removed
To pause the live telemetry display, choose **Flowing**. To re-enable the live display, choose **Pause**:

![Pause and restart telemetry display](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)-->

## <a name="use-the-map"></a>Uso del mapa

El mapa muestra información sobre los camiones simulados seleccionados con el filtro actual. Puede hacer zoom y desplazar el mapa para mostrar las ubicaciones con mayor o menor detalle. Los iconos de dispositivos que aparecen en el mapa indican cualquier **alerta** o **advertencia** activas para el dispositivo. A la izquierda del mapa, se muestra un resumen del número de **alertas** y **advertencias**.

<!-- 05/01 - cannot select a deice on the map
To view the device details, pan and zoom the map to locate the devices, then click the device on the map. The details include:

* Recent telemetry values
* Methods the device supports
* Device properties

![View device details on the dashboard](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)-->

## <a name="view-alerts-from-your-devices"></a>Vista de las alertas de los dispositivos

El mapa resalta los dispositivos en el filtro actual con **alertas** y **advertencias**. El panel de **alertas** muestra información detallada sobre las alertas más recientes de los dispositivos:

![Vista de las alertas del sistema en el panel](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

Puede usar el filtro **Panel** para ajustar el intervalo de tiempo de las alertas recientes. De manera predeterminada, el panel muestra las alertas de la última hora:

![Filtrar las alertas por hora](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>Ver los KPI del sistema

La página **Panel** muestra los KPI del sistema:

![KPI del panel](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

Puede usar el filtro **Panel** para ajustar el intervalo de tiempo de la agregación de KPI. De manera predeterminada, el panel muestra los KPI agregados durante la última hora.

## <a name="next-steps"></a>Pasos siguientes

Este tutorial muestra cómo usar la página **Panel** para filtrar y supervisar los camiones simulados aprovisionados en la solución de supervisión remota:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrar los dispositivos en el panel
> * Vista de la telemetría en tiempo real
> * Vista de detalles de dispositivo
> * Vista de las alertas de los dispositivos
> * Ver los KPI del sistema

Ahora que aprendió a supervisar los dispositivos, los siguientes pasos sugeridos son para aprender a:

* [Detectar problemas mediante reglas basadas en el umbral](./iot-suite-remote-monitoring-automate.md).
* [Administrar y configurar los dispositivos](./iot-suite-remote-monitoring-manage.md).
* [Solucionar problemas de los dispositivos](./iot-suite-remote-monitoring-maintain.md).
* [Probar la solución con dispositivos simulados](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->