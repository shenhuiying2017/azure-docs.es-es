---
title: "Transmisión de registros de Azure Diagnostics a Log Analytics | Microsoft Docs"
description: "Aprenda a transmitir registros de Azure Diagnostics a un área de trabajo de Log Analytics."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: johnkem
ms.openlocfilehash: 9440bd7f872914887c1f6e50f08a3c273536fcf8
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Transmisión de registros de Azure Diagnostics a Log Analytics
**[Los registros de Azure Diagnostics](monitoring-overview-of-diagnostic-logs.md)** se pueden transmitir casi en tiempo real a Azure Log Analytics mediante el portal, cmdlets de PowerShell o CLI de Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Qué se puede hacer con registros de diagnóstico en Log Analytics

Azure Log Analytics es una herramienta flexible de búsqueda y análisis de registros que permite obtener una visión general de los datos de registro sin procesar generados a partir de recursos de Azure. Entre estas funcionalidades, cabe destacar:

* **Búsqueda de registros**: escriba consultas avanzadas sobre los datos de registro, correlacione registros de varios orígenes e incluso genere gráficos que se pueden anclar al panel de Azure.
* **Generación de alertas**: detecte cuando uno o varios eventos coinciden con una consulta determinada y reciba notificaciones por correo electrónico o llamada de webhook.
* **Soluciones**: use vistas preintegradas y paneles que aportan información inmediata sobre los datos de registro.
* **Análisis avanzados**: aplique el aprendizaje automático y algoritmos de coincidencia de patrones para identificar posibles problemas revelados por los registros.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Habilitación de la transmisión de registros de diagnóstico a Log Analytics
Puede habilitar el streaming de registros de diagnóstico mediante programación, a través del portal o mediante la [API de REST de Azure Monitor](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). En cualquier caso, puede crear una configuración de diagnóstico en la que se especifica un área de trabajo de Log Analytics y las categorías de registro y métricas que desea enviar a dicha área de trabajo. Una **categoría de registro** de diagnóstico es un tipo de registro que puede proporcionar un recurso.

El área de trabajo de Log Analytics no tiene que estar en la misma suscripción que la del recurso que emite los registros, siempre que el usuario que configura la configuración tenga acceso RBAC adecuado a ambas suscripciones.

## <a name="stream-diagnostic-logs-using-the-portal"></a>Streaming de registros de diagnóstico mediante el portal
1. En el portal, desplácese a Azure Monitor y haga clic en **Configuración de diagnóstico**.

    ![Sección de supervisión de Azure Monitor](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. Si lo desea, filtre la lista por tipo de recurso o por grupo de recursos y, a continuación, haga clic en el recurso para el que desea establecer la configuración de diagnóstico.

3. Si no existe ninguna configuración en el recurso que ha seleccionado, se le pide que cree una. Haga clic en "Activar diagnóstico".

   ![Agregar configuración de diagnóstico: sin configuración actual](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   Si hay una configuración actual en el recurso, verá una lista de opciones ya configuradas en este recurso. Haga clic en "Agregar configuración de diagnóstico".

   ![Agregar configuración de diagnóstico: configuración actual](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Asigne un nombre de su configuración y active la casilla **Enviar a Log Analytics** y, después, seleccione un área de trabajo de Log Analytics.
   
   ![Agregar configuración de diagnóstico: configuración actual](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)

4. Haga clic en **Guardar**.

Transcurridos unos instantes, la nueva opción de configuración aparece en la lista de opciones para este recurso y los registros de diagnóstico se transmiten en esa área de trabajo en cuanto se generan nuevos datos de eventos. Tenga en cuenta que pueden pasar hasta quince minutos entre cuando se emite un evento y cuando aparece en Log Analytics.

### <a name="via-powershell-cmdlets"></a>Mediante cmdlets de PowerShell
Para habilitar el streaming mediante [cmdlets de Azure PowerShell](insights-powershell-samples.md), puede utilizar el cmdlet `Set-AzureRmDiagnosticSetting` con estos parámetros:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Tenga en cuenta que la propiedad workspaceID toma el identificador de recurso completo de Azure del área de trabajo, no el identificador/clave del área de trabajo que se muestra en el portal de Log Analytics.

### <a name="via-azure-cli"></a>Mediante la CLI de Azure
Para habilitar el streaming mediante la [CLI de Azure](insights-cli-samples.md), puede utilizar el comando `insights diagnostic set` del modo siguiente:

```azurecli
azure insights diagnostic set --resourceId <resourceID> --workspaceId <workspace resource ID> --categories <list of categories> --enabled true
```

Tenga en cuenta que la propiedad workspaceID toma el identificador de recurso completo de Azure del área de trabajo, no el identificador/clave del área de trabajo que se muestra en el portal de Log Analytics.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>¿Cómo se consultan los datos en Log Analytics?

En la hoja Búsqueda de registros del portal o en la experiencia de análisis avanzado como parte de Log Analytics, puede consultar los registros de diagnóstico como parte de la solución Log Management en la tabla AzureDiagnostics. También hay [varias soluciones para recursos de Azure](../log-analytics/log-analytics-add-solutions.md) que se pueden instalar para obtener información inmediata sobre los datos del registro que va a enviar a Log Analytics.


## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre los registros de Diagnósticos de Azure](monitoring-overview-of-diagnostic-logs.md)
