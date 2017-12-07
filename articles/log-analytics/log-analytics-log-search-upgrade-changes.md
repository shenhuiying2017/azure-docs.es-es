---
title: "¿Qué ha cambiado en Azure Log Analytics? | Microsoft Docs"
description: "Este artículo ofrece respuesta a las preguntas más frecuentes sobre la actualización de Log Analytics al nuevo lenguaje de consulta."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bwren
ms.openlocfilehash: 017a1da233827f19489a99b234ee9009fd9f6fe3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="whats-changed-in-azure-log-analytics"></a>¿Qué ha cambiado en Azure Log Analytics?
Además del propio lenguaje de consulta, hay varias mejoras y cambios que debe tener en cuenta cuando el área de trabajo de Log Analytics se [actualiza al nuevo lenguaje de consulta](log-analytics-log-search-new.md).  En este artículo se describen brevemente los cambios entre un área de trabajo heredada y actualizada con vínculos a contenido detallado para cada una. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Whats-changed-in-Azure-Log-Analytics/player]

Consulte [Problemas conocidos y preguntas frecuentes sobre la nueva búsqueda de registros de Log Analytics](log-analytics-log-search-faq.md) para obtener una descripción de todos los problemas conocidos con la actualización y respuestas a preguntas habituales.  

## <a name="query-language"></a>Lenguaje de consulta
El cambio principal en la actualización de Log Analytics es el nuevo lenguaje de consulta, que tiene una mejora significativa respecto al lenguaje anterior.  

Puede obtener una comparación directa de las operaciones comunes entre el lenguaje heredado y el nuevo lenguaje en [Transición al nuevo lenguaje de consulta de Log Analytics de Azure](log-analytics-log-search-transition.md).  La documentación completa sobre el nuevo lenguaje está disponible en [Azure Log Analytics Query Language](https://docs.loganalytics.io) (Lenguaje de consulta de Azure Log Analytics).


## <a name="computer-groups"></a>Grupos de equipos
El método para crear un grupo de equipos no ha cambiado, aunque ahora debe proporcionar un alias único para cada uno.  Los grupos de equipos basados en búsquedas de registros deben utilizar la sintaxis del lenguaje nuevo.

Hay una nueva sintaxis para usar grupos de equipos en una búsqueda de registros.  En lugar de usar la función $ComputerGroups, ahora cada grupo de equipos se implementa como una función con un alias único.  Utilice el alias en la búsqueda de registros al igual que cualquier otra función.  

Hay información detallada disponibles en [Grupos de equipos en búsquedas de registros en Log Analytics](log-analytics-computer-groups.md).


## <a name="log-search-portals"></a>Portales de búsqueda de registros
Además del portal de Búsqueda de registros para crear y ejecutar búsquedas de registros, ahora puede usar el portal de Análisis avanzado, que proporciona características de edición con una mejora significativa.

Para ver una breve descripción de los dos portales, consulte [Portales para la creación y edición de consultas de registros en Azure Log Analytics](log-analytics-log-search-portals.md).  Puede ver un tutorial sobre el nuevo portal en [Getting Started with the Analytics Portal](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal) (Introducción al portal de Analytics).

## <a name="alerts"></a>Alertas
Las alertas funcionan igual en áreas de trabajo actualizadas, pero la consulta que se ejecuta debe escribirse en el nuevo lenguaje.  Todas las reglas de alerta existentes que tenía antes de la actualización se convertirán automáticamente en el nuevo lenguaje.  Puede obtener más detalles en [Información sobre alertas en Log Analytics](log-analytics-alerts.md).

El formato de la carga de runbooks y webhooks enviados desde las alertas ha cambiado.  Para obtener detalles acerca del nuevo formato de carga, consulte [Adición de acciones a reglas de alerta en Log Analytics](log-analytics-alerts-actions.md).

## <a name="dashboards"></a>Paneles
Los [paneles](log-analytics-dashboards.md) están en proceso de quedar en desuso.  Puede continuar usando todos los elementos agregados al panel antes de la actualización del área de trabajo, pero no puede editarlos ni agregar otros nuevos.  Utilice Diseñador de vistas para crear vistas personalizadas que tengan un conjunto de características más completo que los paneles.

Los detalles del Diseñador de vistas están disponibles en [Uso del Diseñador de vistas para crear vistas personalizadas en Log Analytics](log-analytics-view-designer.md).

## <a name="power-bi"></a>Power BI
El proceso de exportación de datos de Log Analytics a Power BI ha cambiado para las áreas de trabajo actualizadas, y se deshabilitarán las programaciones existentes creadas antes de la actualización.  

Los detalles están disponibles en [Exportación de datos de Log Analytics a Power BI](log-analytics-powerbi.md).

## <a name="powershell"></a>PowerShell
Get-AzureRmOperationalInsightsSearchResults, para ejecutar búsquedas de registros en PowerShell, no funcionará con un área de trabajo actualizada.  Use Invoke-LogAnalyticsQuery para esta funcionalidad con un área de trabajo actualizada.

Los detalles están disponibles en [Azure Log Analytics REST API - PowerShell Cmdlets](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) (API de REST de Azure Log Analytics: cmdlets de PowerShell).

## <a name="log-search-api"></a>API de búsqueda de registros
La API de REST de búsqueda de registros ha cambiado, y las soluciones que usan la versión heredada deben actualizarse para usar la nueva versión de la API.   

Para obtener más información sobre la nueva versión de la API, consulte [Azure Log Analytics REST API](https://dev.loganalytics.io/) (API de REST de Azure Log Analytics).

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Problemas conocidos y preguntas frecuentes sobre la nueva búsqueda de registros de Log Analytics](log-analytics-log-search-faq.md) para obtener una descripción de todos los problemas conocidos con la actualización y respuestas a preguntas habituales.