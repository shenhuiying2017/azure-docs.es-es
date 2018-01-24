---
title: "Reenvío de datos del trabajo de Azure Automation a Log Analytics de OMS | Microsoft Docs"
description: "Este artículo muestra cómo enviar el estado de un trabajo y transmisiones de trabajo del runbook a Log Analytics de Microsoft Operations Management Suite para ofrecer mayor información y administración."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: 0319a7b9248dec9d7cdabba9c18a25463d94284b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Reenvío del estado de un trabajo y de transmisiones de trabajos de Automation a Log Analytics (OMS)
Automation puede enviar el estado de un trabajo del runbook y de transmisiones de trabajos al área de trabajo devLog Analytics de Microsoft Operations Management Suite (OMS). Los registros de trabajo y flujos de trabajo están visibles en Azure Portal, o con PowerShell, para los trabajos individuales y esto permite llevar a cabo investigaciones simples. Ahora con Log Analytics puede:

* Obtener información sobre los trabajos de Automation.
* Desencadenar un correo electrónico o una alerta en función del estado de trabajo del runbook (por ejemplo, error o en suspensión).
* Escribir consultas avanzadas en las transmisiones de trabajos.
* Correlacionar trabajos en cuentas de Automation.
* Visualizar el historial de trabajos a lo largo del tiempo.

## <a name="prerequisites-and-deployment-considerations"></a>Requisitos previos y consideraciones de implementación
Para empezar a enviar los registros de Automation a Log Analytics, necesita:

* La versión de noviembre de 2016, o una posterior, de [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
* Un área de trabajo de Log Analytics. Para más información, consulte [Introducción a Log Analytics](../log-analytics/log-analytics-get-started.md). 
* El valor de ResourceId de su cuenta de Azure Automation.


Para encontrar el valor de ResourceId de su cuenta de Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Para encontrar el valor de ResourceId del área de trabajo de Log Analytics, ejecute el código PowerShell siguiente:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Si tiene varias cuentas de Automation, o áreas de trabajo, en la salida de los comandos anteriores, busque el *nombre* que tiene que configurar y copie el valor para *ResourceId*.

Si necesita encontrar el *nombre* de la cuenta de Automation, en Azure Portal seleccione su cuenta de Automation en la hoja **Cuenta de Automation** y seleccione **Toda la configuración**. En la hoja **Toda la configuración**, en **Configuración de la cuenta**, seleccione **Propiedades**.  En la hoja **Propiedades**, puede anotar estos valores.<br> ![Propiedades de una cuenta de Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Configuración de la integración con Log Analytics

1. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio**.
2. Ejecute el siguiente código PowerShell y edite el valor de `[your resource id]` y `[resource id of the log analytics workspace]` con los valores del paso anterior.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

Después de ejecutar este script, verá los registros de Log Analytics a los 10 minutos de escribir los nuevos JobLogs o JobStreams.

Para ver los registros, ejecute la siguiente consulta en la búsqueda de registros de Log Analytics: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION""`

### <a name="verify-configuration"></a>Comprobación de la configuración
Para confirmar que la cuenta de Automation está enviando registros a su área de trabajo de Log Analytics, compruebe que los diagnósticos están configurados correctamente en la cuenta de Automation con el código PowerShell siguiente:

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

En la salida asegúrese de que:
+ En *Registros*, el valor de *Habilitado* es *True*.
+ El valor de *WorkspaceId* se establece en el valor de ResourceId de su área de trabajo de Log Analytics.

## <a name="log-analytics-records"></a>Registros de Log Analytics

Diagnósticos de Azure Automation crea dos tipos de registros en Log Analytics y se etiquetan como **AzureDiagnostics**. Las consultas siguientes utilizan el lenguaje de consulta actualizado en Log Analytics. Para más información acerca de las consultas comunes entre lenguaje de consulta heredado y el nuevo lenguaje de consulta de Azure Log Analytics, visite [Legacy to new Azure Log Analytics Query Language cheat sheet](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language) (Hoja de referencia de lenguaje de consulta heredado a lenguaje de consulta nuevo de Azure Log Analytics).

### <a name="job-logs"></a>Registros de trabajo
| Propiedad | DESCRIPCIÓN |
| --- | --- |
| TimeGenerated |Fecha y hora en que se ejecuta el trabajo de Runbook. |
| RunbookName_s |El nombre del Runbook. |
| Caller_s |La persona que inicia la operación. Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados. |
| Tenant_g | GUID que identifica al inquilino para el llamador. |
| JobId_g |GUID que es el identificador del trabajo de Runbook. |
| ResultType |El estado del trabajo de Runbook. Los valores posibles son:<br>- New<br>Started<br>Stopped<br>Suspended<br>Con error<br>Completado |
| Categoría | Clasificación del tipo de datos. Para Automation, el valor será JobLogs. |
| nombreOperación | Especifica el tipo de operación realizada en Azure. En Automation, el valor es Job. |
| Recurso | Nombre de la cuenta de Automation |
| SourceSystem | Cómo Log Analytics ha recopilado los datos. Siempre *Azure* para Diagnósticos de Azure. |
| ResultDescription |Describe el estado de resultado del trabajo de Runbook. Los valores posibles son:<br>- Se inicia el trabajo<br>- Error del trabajo<br>- Trabajo completado |
| CorrelationId |GUID que es el identificador de correlación del trabajo de Runbook. |
| ResourceId |Especifica el identificador de recursos de la cuenta de Azure Automation del runbook. |
| SubscriptionId | El identificador de la suscripción de Azure (GUID) para la cuenta de Automation. |
| ResourceGroup | Nombre del grupo de recursos de la cuenta de Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Flujos de trabajo
| Propiedad | DESCRIPCIÓN |
| --- | --- |
| TimeGenerated |Fecha y hora en que se ejecuta el trabajo de Runbook. |
| RunbookName_s |El nombre del Runbook. |
| Caller_s |La persona que inicia la operación. Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados. |
| StreamType_s |El tipo de flujo de trabajo. Los valores posibles son:<br>progreso<br>- Salida<br>Warning (Advertencia)<br>Error<br>DEBUG<br>- Detallado |
| Tenant_g | GUID que identifica al inquilino para el llamador. |
| JobId_g |GUID que es el identificador del trabajo de Runbook. |
| ResultType |El estado del trabajo de Runbook. Los valores posibles son:<br>- In Progress |
| Categoría | Clasificación del tipo de datos. Para Automation, el valor es JobStreams. |
| nombreOperación | Especifica el tipo de operación realizada en Azure. En Automation, el valor es Job. |
| Recurso | Nombre de la cuenta de Automation |
| SourceSystem | Cómo Log Analytics ha recopilado los datos. Siempre *Azure* para Diagnósticos de Azure. |
| ResultDescription |Incluye la secuencia de salida del Runbook. |
| CorrelationId |GUID que es el identificador de correlación del trabajo de Runbook. |
| ResourceId |Especifica el identificador de recursos de la cuenta de Azure Automation del runbook. |
| SubscriptionId | El identificador de la suscripción de Azure (GUID) para la cuenta de Automation. |
| ResourceGroup | Nombre del grupo de recursos de la cuenta de Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Visualización de registros de Automation en Log Analytics
Ahora que ha iniciado el envío de los registros de los trabajos de Automation a Log Analytics, veamos lo que puede hacer con dichos registros en Log Analytics.

Para ver los registros, ejecute la consulta siguiente: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Envío de un correo electrónico cuando se produce un error en un trabajo del runbook o se suspende
Uno de los principales clientes pregunta por la capacidad para enviar un correo electrónico o un mensaje de texto cuando aparece algún problema en un trabajo del runbook.   

Para crear una regla de alertas, lo primero es crear una búsqueda de registros de los registros del trabajo del runbook que deben invocar la alerta. Haga clic en el botón **Alerta** para crear y configurar la regla de alerta.

1. En la página de información general de Log Analytics, haga clic en **Búsqueda de registros**.
2. Cree una consulta de búsqueda de registros para la alerta escribiendo la siguiente búsqueda en el campo de consulta: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`. También puede agrupar por el valor de RunbookName mediante: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`.

   Si configura registros de más de una cuenta de Automation o suscripción a su área de trabajo, puede agrupar las alertas por suscripción o cuenta de Automation. El nombre de la cuenta de Automation puede encontrarse en el campo Recurso en la búsqueda de JobLogs.
1. Para abrir la pantalla **Agregar regla de alerta**, haga clic en **Alerta** en la parte superior de la página. Para más información acerca de las opciones para configurar la alerta, consulte [Información sobre alertas en Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Búsqueda de todos los trabajos que se han completado con errores
Además de las alertas al producirse errores, puede saber cuando un trabajo de runbook tiene un error de no terminación. En estos casos, PowerShell genera un flujo de errores, pero los errores de no terminación no causan la suspensión ni producirán un error en el trabajo.    

1. En el área de trabajo de Log Analytics, haga clic en **Búsqueda de registros**.
2. En el campo de consulta, escriba `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` y luego haga clic en el botón **Buscar**.

### <a name="view-job-streams-for-a-job"></a>Visualización de las transmisiones de un trabajo
Cuando se depura un trabajo, también se pueden examinar sus transmisiones. La siguiente consulta muestra todas las transmisiones de un solo trabajo con GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visualización del estado del historial de trabajos
Por último, puede desear visualizar el historial de trabajos con el paso del tiempo. Esta consulta se puede usar para buscar el estado de los trabajos con el paso del tiempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Gráfico del historial de trabajos de OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Resumen
Al enviar el estado de trabajo y los datos de flujo de Automation a Log Analytics, puede obtener más información del estado de los trabajos de Automation mediante lo siguiente:
+ Establecimiento de alertas que le notificarán cuando haya un problema.
+ Uso de vistas personalizadas y de consultas de búsqueda para visualizar los resultados de runbook, el estado del trabajo de runbook y otras métricas o indicadores clave relacionados.  

Log Analytics proporciona mayor visibilidad operativa para los trabajos de Automation y pueden ayudar a resolver incidentes con más rapidez.  

## <a name="next-steps"></a>pasos siguientes
* Para aprender a crear diferentes consultas de búsqueda y a revisar los registros de trabajos de Automation con Log Analytics, consulte [Descripción de las búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-searches.md).
* Para aprender a crear y recuperar los mensajes de salida y de error de los runbooks, consulte [Salidas de runbook y mensajes](automation-runbook-output-and-messages.md).
* Para más información acerca de la ejecución de un runbook, cómo supervisar trabajos del runbook y otros detalles técnicos, consulte [Ejecución de un runbook en Automatización de Azure](automation-runbook-execution.md).
* Para más información acerca de Log Analytics de OMS y de los orígenes de recopilación de datos, consulte la [introducción a la recopilación de datos de Azure Storage en Log Analytics](../log-analytics/log-analytics-azure-storage.md).
