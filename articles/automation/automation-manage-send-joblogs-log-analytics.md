---
title: "Reenvío de datos del trabajo de Azure Automation a Log Analytics de OMS | Microsoft Docs"
description: "Este artículo muestra cómo enviar el estado de un trabajo y transmisiones de trabajo del runbook a Log Analytics de Microsoft Operations Management Suite para ofrecer mayor información y administración."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: d7cba9126c11418f8ccafb1ace0816a9a9cc3b6d
ms.openlocfilehash: babfda8735699b9f9991bffd22a8d2d9847ae759


---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Reenvío del estado de un trabajo y de transmisiones de trabajos de Automatización a Log Analytics (OMS)
Automatización puede enviar el estado de un trabajo del runbook y de transmisiones de trabajos al área de trabajo devLog Analytics de Microsoft Operations Management Suite (OMS).  Los registros de trabajo y flujos de trabajo están visibles en Azure Portal, o con PowerShell, para los trabajos individuales y esto permite llevar a cabo investigaciones simples. Ahora con Log Analytics puede:

* Obtener información sobre los trabajos de Automatización
* Desencadenador de un correo electrónico o una alerta en función del estado de trabajo del runbook (por ejemplo, error o en suspensión)
* Escribir consultas avanzadas en las transmisiones de trabajos
* Correlacionar trabajos en cuentas de Automatización
* Visualizar el historial de trabajos a lo largo del tiempo     

## <a name="prerequisites-and-deployment-considerations"></a>Requisitos previos y consideraciones de implementación
Para empezar a enviar los registros de Automation a Log Analytics, necesita:

1. La versión de noviembre de 2016, o una posterior, de [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
2. Un área de trabajo de Log Analytics. Para más información, consulte [Introducción a Log Analytics](../log-analytics/log-analytics-get-started.md). 
3. El valor de ResourceId de su cuenta de Azure Automation

Para encontrar el valor de ResourceId de la cuenta de Azure Automation y el área de trabajo de Log Analytics, ejecute el PowerShell siguiente:

```powershell
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"

# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Si tiene varias cuentas de Automation, o áreas de trabajo, en la salida de los comandos anteriores, busque el *nombre* que tiene que configurar y copie el valor para *ResourceId*.

Si necesita encontrar el *nombre* de la cuenta de Automation, en Azure Portal seleccione su cuenta de Automation en la hoja **Cuenta de Automation** y seleccione **Toda la configuración**.  En la hoja **Toda la configuración**, en **Configuración de la cuenta**, seleccione **Propiedades**.  En la hoja **Propiedades**, puede anotar estos valores.<br> ![Propiedades de una cuenta de Automatización](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)(Introducción a Log Analytics).

## <a name="set-up-integration-with-log-analytics"></a>Configuración de la integración con Log Analytics
1. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio**.  
2. Copie y pegue el código PowerShell siguiente, y edite el valor para `$workspaceId` y `$automationAccountId`.  Si está trabajando en la nube de Azure Government, debe establecer el valor booleano de `$GovCloud` en *$True* o *1* cuando lo ejecute.     

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$true)]
        [bool]$GovCloud = $False
    )

#Check to see if we need to log into Commercial or Government cloud.
If ($GovCloud -eq $False) {
    Login-AzureRmAccount
}Else{
    Login-AzureRmAccount -EnvironmentName AzureUSGovernment 
}
# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true

```

Después de ejecutar este script, verá los registros de Log Analytics a los 10 minutos de escribir los nuevos JobLogs o JobStreams.

Para ver los registros, ejecute la consulta siguiente: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Comprobación de la configuración
Para confirmar que la cuenta de Automation está enviando registros a su área de trabajo de Log Analytics, compruebe que los diagnósticos están establecidos correctamente en la cuenta de Automation con el código PowerShell siguiente:

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$true)]
        [bool]$GovCloud = $False
    )

#Check to see if we need to log into Commercial or Government cloud.
If ($GovCloud -eq $False) {
    Login-AzureRmAccount
}Else{
    Login-AzureRmAccount -EnvironmentName AzureUSGovernment 
}
# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

En la salida asegúrese de que:
+ En *Registros*, el valor de *Habilitado* es *True*.
+ El valor de *WorkspaceId* se establece en el valor de ResourceId de su área de trabajo de Log Analytics.


## <a name="log-analytics-records"></a>Registros de Log Analytics
Diagnósticos de Azure Automation crea dos tipos de registros en Log Analytics. 

### <a name="job-logs"></a>Registros de trabajo
| Propiedad | Descripción |
| --- | --- |
| TimeGenerated |Fecha y hora en que se ejecuta el trabajo de Runbook. |
| RunbookName_s |El nombre del Runbook. |
| Caller_s |La persona que inicia la operación.  Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados. |
| Tenant_g | GUID que identifica al inquilino para el llamador. |
| JobId_g |GUID que es el identificador del trabajo de Runbook. |
| ResultType |El estado del trabajo de Runbook.  Los valores posibles son:<br>Started<br>Stopped<br>Suspended<br>Con error<br>- Correcto |
| Categoría | Clasificación del tipo de datos.  Para Automation, el valor será JobLogs. |
| nombreOperación | Especifica el tipo de operación realizada en Azure.  En Automation, el valor es Job. |
| Recurso | Nombre de la cuenta de Automation |
| SourceSystem | Cómo Log Analytics ha recopilado los datos. Siempre *Azure* para Diagnósticos de Azure. |
| ResultDescription |Describe el estado de resultado del trabajo de Runbook.  Los valores posibles son:<br>- Se inicia el trabajo<br>- Error del trabajo<br>- Trabajo completado |
| CorrelationId |GUID que es el identificador de correlación del trabajo de Runbook. |
| ResourceId |Especifica el identificador de recursos de la cuenta de Azure Automation del runbook. |
| SubscriptionId | El identificador de la suscripción de Azure (GUID) para la cuenta de Automation. |
| ResourceGroup | Nombre del grupo de recursos de la cuenta de Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Flujos de trabajo
| Propiedad | Descripción |
| --- | --- |
| TimeGenerated |Fecha y hora en que se ejecuta el trabajo de Runbook. |
| RunbookName_s |El nombre del Runbook. |
| Caller_s |La persona que inicia la operación.  Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados. |
| StreamType_s |El tipo de flujo de trabajo. Los valores posibles son:<br>progreso<br>- Salida<br>Warning (Advertencia)<br>Error<br>DEBUG<br>- Detallado |
| Tenant_g | GUID que identifica al inquilino para el llamador. |
| JobId_g |GUID que es el identificador del trabajo de Runbook. |
| ResultType |El estado del trabajo de Runbook.  Los valores posibles son:<br>- In Progress |
| Categoría | Clasificación del tipo de datos.  Para Automation, el valor es JobStreams. |
| nombreOperación | Especifica el tipo de operación realizada en Azure.  En Automation, el valor es Job. |
| Recurso | Nombre de la cuenta de Automation |
| SourceSystem | Cómo Log Analytics ha recopilado los datos. Siempre *Azure* para Diagnósticos de Azure. |
| ResultDescription |Incluye la secuencia de salida del Runbook. |
| CorrelationId |GUID que es el identificador de correlación del trabajo de Runbook. |
| ResourceId |Especifica el identificador de recursos de la cuenta de Azure Automation del runbook. |
| SubscriptionId | El identificador de la suscripción de Azure (GUID) para la cuenta de Automation. |
| ResourceGroup | Nombre del grupo de recursos de la cuenta de Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Visualización de registros de Automatización en Log Analytics
Ahora que ha iniciado el envío de los registros de los trabajos de Automation a Log Analytics, veamos lo que puede hacer con dichos registros en Log Analytics.

Para ver los registros, ejecute la consulta siguiente: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Envío de un correo electrónico cuando se produce un error en un trabajo del runbook o se suspende
Uno de nuestros principales clientes pregunta por la capacidad para enviar un correo electrónico o un mensaje de texto cuando aparece algún problema en un trabajo del runbook.   

Para crear una regla de alertas, lo primero es crear una búsqueda de registros de los registros del trabajo del runbook que deben invocar la alerta.  Haga clic en el botón **Alerta** para crear y configurar la regla de alerta.

1. En la página de información general de Log Analytics, haga clic en **Búsqueda de registros**.
2. Cree una consulta de búsqueda de registros para la alerta escribiendo la siguiente búsqueda en el campo de consulta: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended)`. También puede agrupar por el valor de RunbookName mediante: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended) | measure Count() by RunbookName_s`.   

   Si ha configurado registros de más de una cuenta de Automation o suscripción a su área de trabajo, puede agrupar las alertas por suscripción o cuenta de Automation.  El nombre de la cuenta de Automatización puede derivarse del campo Resource (Recurso) en la búsqueda de JobLogs.  
3. Para abrir la pantalla **Agregar regla de alerta**, haga clic en **Alerta** en la parte superior de la página. Para más información sobre las opciones para configurar la alerta, consulte [Alertas de Log Analytics](../log-analytics/log-analytics-alerts.md#creating-an-alert-rule).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Búsqueda de todos los trabajos que se han completado con errores
Además de las alertas al producirse errores, puede saber cuando un trabajo de runbook tiene un error de no terminación. En estos casos, PowerShell genera un flujo de errores, pero los errores de no terminación no causan la suspensión ni producirán un error en el trabajo.    

1. En el área de trabajo de Log Analytics, haga clic en **Búsqueda de registros**.
2. En el campo de consulta, escriba `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams StreamType_s=Error | measure count() by JobId_g` y luego haga clic en **Buscar**.

### <a name="view-job-streams-for-a-job"></a>Visualización de las transmisiones de un trabajo
Cuando se depura un trabajo, también se pueden examinar sus transmisiones.  La siguiente consulta muestra todas las transmisiones de un solo trabajo con GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:   

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### <a name="view-historical-job-status"></a>Visualización del estado del historial de trabajos
Por último, puede desear visualizar el historial de trabajos con el paso del tiempo.  Esta consulta se puede usar para buscar el estado de los trabajos con el paso del tiempo.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  
<br> ![Gráfico del historial de trabajos de OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Resumen
Al enviar el estado de trabajo y los datos de flujo de Automation a Log Analytics, puede obtener más información del estado de los trabajos de Automation mediante lo siguiente:
+ Establecimiento de alertas que le notificarán cuando haya un problema
+ Uso de vistas personalizadas y de consultas de búsqueda para visualizar los resultados de runbook, el estado del trabajo de runbook y otras métricas o indicadores clave relacionados.  

Log Analytics proporciona mayor visibilidad operativa para los trabajos de Automation y pueden ayudar a resolver incidentes con más rapidez.  

## <a name="next-steps"></a>Pasos siguientes
* Para aprender a crear diferentes consultas de búsqueda y a revisar los registros de trabajos de Automatización con Log Analytics, consulte [Búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-searches.md)
* Para aprender a crear y recuperar los mensajes de salida y de error de los runbooks en, consulte [Salidas de runbook y mensajes en la Automatización de Azure](automation-runbook-output-and-messages.md)
* Para más información acerca de la ejecución de un runbook, cómo supervisar trabajos del runbook y otros detalles técnicos, consulte [Ejecución de un runbook en Automatización de Azure](automation-runbook-execution.md)
* Para más información acerca de Log Analytics de OMS y de los orígenes de recopilación de datos, consulte [Conexión de Almacenamiento de Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)



<!--HONumber=Feb17_HO2-->


