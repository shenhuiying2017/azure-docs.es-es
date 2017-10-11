---
title: "Reenvío de datos de informes de DSC de Azure Automation a Log Analytics de OMS | Microsoft Docs"
description: "En este artículo se muestra cómo enviar datos de los informes de Desired State Configuration (DSC) a Log Analytics de Microsoft Operations Management Suite para ofrecer mayor información y administración."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: eslesar
ms.openlocfilehash: 316031c5297a0201c8db4a9e177298c78962c673
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="forward-azure-automation-dsc-reporting-data-to-oms-log-analytics"></a>Reenvío de datos de informes de DSC de Azure Automation a Log Analytics de OMS

Automation datos del estado de los nodos de DSC al área de trabajo de Log Analytics de Microsoft Operations Management Suite (OMS).  
El estado de cumplimiento se puede ver en Azure Portal, o con PowerShell, tanto para los nodos como para los recursos de DSC individuales en las configuraciones de los nodos. Con Log Analytics puede:

* Obtener información de cumplimiento de nodos administrados y recursos individuales
* Desencadenar un correo electrónico o una alerta basados en el estado de cumplimiento
* Escribir consultas avanzadas en los nodos administrados
* Correlacionar el estado de cumplimiento en las cuentas de Automation
* Visualizar el historial de cumplimiento de los nodo con el paso del tiempo

## <a name="prerequisites"></a>Requisitos previos

Para empezar a enviar los registros de 
DSC de Automation a Log Analytics, necesita:

* La versión de noviembre de 2016, o una posterior, de [Azure PowerShell](/powershell/azure/overview) (v2.3.0).
* Una cuenta de Automatización de Azure Para más información, consulte [Introducción a Azure Automation](automation-offering-get-started.md)
* Un área de trabajo de Log Analytics con una oferta de servicio de **Automation & Control**. Para más información, consulte [Introducción a Log Analytics](../log-analytics/log-analytics-get-started.md).
* Al menos un nodo de DSC de Azure Automation. Para más información, consulte [Incorporación de máquinas para administrarlas con DSC de Azure Automation](automation-dsc-onboarding.md) 

## <a name="set-up-integration-with-log-analytics"></a>Configuración de la integración con Log Analytics

Para comenzar a importar datos de DSC de Azure Automation en Log Analytics, haga lo siguiente:

1. Inicie sesión en su cuenta de Azure en PowerShell. Consulte [Inicio de sesión con Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. Obtenga el valor de _ResourceId_ de su cuenta de Automation mediante la ejecución del siguiente comando de PowerShell: (si tiene más de una cuenta de Automation, elija el valor de _ResourceID_ de la cuenta que desea configurar).

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
  ```
1. Obtenga el valor de _ResourceId_ del espacio de trabajo de Log Analytics mediante la ejecución del siguiente comando de PowerShell: (si tiene más de un espacio de trabajo, elija el valor de _ResourceID_ del espacio de trabajo que desea configurar).

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
  ```
1. Ejecute el siguiente comando de PowerShell, pero reemplace `<AutomationResourceId>` y `<WorkspaceResourceId>` por los valores de _ResourceId_ valores de cada uno de los pasos anteriores:

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories "DscNodeStatus"
  ```

Si desea detener la importación de datos de DSC de Azure Automation en Log Analytics, ejecute el siguiente comando de PowerShell.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories "DscNodeStatus"
```

## <a name="view-the-dsc-logs"></a>Visualización de los registros de DSC

Después de configurar la integración con Log Analytics para los datos de DSC de Automation, aparecerá un botón **Búsqueda de registros** en la hoja **Nodos DSC** de la cuenta de Automation. Haga clic en el botón **Búsqueda de registros** para ver los registros de los datos del nodo DSC.

![Botón Búsqueda de registros](media/automation-dsc-diagnostics/log-search-button.png)

Se abre la hoja **Búsqueda de registros**, donde se ve una operación **DscNodeStatusData** para cada nodo DSC y una operación **DscResourceStatusData** para cada [recurso de DSC](https://msdn.microsoft.com/powershell/dsc/resources) llamado en la configuración que se aplica a un nodo.

La operación **DscResourceStatusData** contiene información de los recursos de DSC en que se produjeron errores.

Haga clic en la operación que desee de la lista para ver los datos de la misma.

Para ver los registros, realice la búsqueda en Log Analytics. Consulte [Búsqueda de datos mediante búsquedas de registros](../log-analytics/log-analytics-log-searches.md).
Escriba la siguiente consulta para buscar los registros de DSC:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus"`

La consulta también se puede restringir por nombre de operación. Por ejemplo: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus" OperationName = "DscNodeStatusData"

### <a name="send-an-email-when-a-dsc-compliance-check-fails"></a>Envío de un correo electrónico cuando se produce un error en cualquier comprobación de cumplimiento de DSC

Uno de nuestros principales clientes solicita la capacidad para enviar un correo electrónico o un mensaje de texto cuando aparece algún problema en una configuración de DSC.   

Para crear una regla de alerta, primero debe crear una búsqueda de registros para los registros del informe de DSC que deben invocar la alerta.  Haga clic en el botón **Alerta** para crear y configurar la regla de alerta.

1. En la página de información general de Log Analytics, haga clic en **Búsqueda de registros**.
1. Cree una consulta de búsqueda de registros para la alerta; para ello, escriba la siguiente búsqueda en el campo de la consulta: `Type=AzureDiagnostics Category=DscNodeStatus NodeName_s=DSCTEST1 OperationName=DscNodeStatusData ResultType=Failed`

  Si ha configurado registros de más de una cuenta de Automation o suscripción a su área de trabajo, puede agrupar las alertas por suscripción o cuenta de Automation.  
  El nombre de la cuenta de Automation puede derivarse del campo Resource (Recurso) en la búsqueda de DscNodeStatusData.  
1. Para abrir la pantalla **Agregar regla de alerta**, haga clic en **Alerta** en la parte superior de la página. Para más información acerca de las opciones para configurar la alerta, consulte [Información sobre alertas en Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Búsqueda de recursos de DSC con errores en todos los nodos

Una ventaja de usar Log Analytics es que es posible buscar comprobaciones con errores en todos los nodos.
Para buscar todas las instancias de recursos de DSC con errores:

1. En la página de información general de Log Analytics, haga clic en **Búsqueda de registros**.
1. Cree una consulta de búsqueda de registros para la alerta; para ello, escriba la siguiente búsqueda en el campo de la consulta: `Type=AzureDiagnostics Category=DscNodeStatus OperationName=DscResourceStatusData ResultType=Failed`

### <a name="view-historical-dsc-node-status"></a>Visualización del historial de estado del nodo de DSC

Por último, puede desear visualizar el historial de estado del nodo de DSC con el paso del tiempo.  
Esta consulta se puede usar para buscar el estado del nodos de DSC con el paso del tiempo.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Se mostrará un gráfico del estado del nodo con el paso del tiempo.

## <a name="log-analytics-records"></a>Registros de Log Analytics

Diagnostics, de Azure Automation, crea dos categorías de registros en Log Analytics.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propiedad | Descripción |
| --- | --- |
| TimeGenerated |Fecha y hora en que se ejecutó la comprobación de cumplimiento. |
| nombreOperación |DscNodeStatusData |
| ResultType |Si el nodo es compatible. |
| NodeName_s |El nombre del nodo administrado. |
| NodeComplianceStatus_s |Si el nodo es compatible. |
| DscReportStatus |Si la comprobación de cumplimiento se ejecutó correctamente. |
| ConfigurationMode | Cómo se aplica la configuración al nodo. Los valores posibles son __"ApplyOnly"__, __"ApplyandMonitior"__ y __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: DSC se aplica la configuración y es lo único que hace, salvo que se inserte una nueva configuración en el nodo de destino o cuando se extrae una nueva configuración de un servidor. Después de la aplicación inicial de una nueva configuración, DSC no comprueba si se ha producido una desviación desde un estado configurado previamente. DSC intenta aplicar la configuración hasta que sea la correcta antes de __ApplyOnly__ surta efecto. </li><li> __ObjectID__: es el valor predeterminado. El LCM aplica las nuevas configuraciones. Después de la aplicación inicial de una nueva configuración, si el nodo de destino se desvía del estado deseado, DSC notifica la discrepancia en los registros. DSC intenta aplicar la configuración hasta que sea la correcta antes de __ApplyAndMonitor__ surta efecto.</li><li>__ApplyAndAutoCorrect__: DSC aplica las configuraciones nuevas. Después de la aplicación inicial de una nueva configuración, si el nodo de destino se desvía del estado deseado, DSC notifica la discrepancia en los registros y vuelve a aplicar la configuración actual.</li></ul> |
| HostName_s | El nombre del nodo administrado. |
| IPAddress | La dirección IPv4 del nodo administrado. |
| Categoría | DscNodeStatus |
| Recurso | El nombre de la cuenta de Azure Automation. |
| Tenant_g | GUID que identifica al inquilino para el llamador. |
| NodeId_g |GUID que identifica el nodo administrado. |
| DscReportId_g |GUID que identifica el informe. |
| LastSeenTime_t |Fecha y hora en que el informe se vio por última vez. |
| ReportStartTime_t |Fecha y hora en que el informe se inició. |
| ReportEndTime_t |Fecha y hora en que el informe se completó. |
| NumberOfResources_d |El número de recursos de DSC que se llama en la configuración que se aplica al nodo. |
| SourceSystem | Cómo Log Analytics ha recopilado los datos. Siempre *Azure* para Diagnósticos de Azure. |
| ResourceId |Especifica la cuenta de Azure Automation. |
| ResultDescription | La descripción de esta operación. |
| SubscriptionId | El identificador de la suscripción de Azure (GUID) para la cuenta de Automation. |
| ResourceGroup | Nombre del grupo de recursos de la cuenta de Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID que es el identificador de correlación del informe de cumplimiento. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propiedad | Descripción |
| --- | --- |
| TimeGenerated |Fecha y hora en que se ejecutó la comprobación de cumplimiento. |
| nombreOperación |DscResourceStatusData|
| ResultType |Si el recurso es compatible. |
| NodeName_s |El nombre del nodo administrado. |
| Categoría | DscNodeStatus |
| Recurso | El nombre de la cuenta de Azure Automation. |
| Tenant_g | GUID que identifica al inquilino para el llamador. |
| NodeId_g |GUID que identifica el nodo administrado. |
| DscReportId_g |GUID que identifica el informe. |
| DscResourceId_s |El nombre de la instancia del recurso de DSC. |
| DscResourceName_s |El nombre del recurso de DSC. |
| DscResourceStatus_s |Si el recurso de DSC es compatible. |
| DscModuleName_s |El nombre del módulo de PowerShell que contiene el recurso de DSC. |
| DscModuleVersion_s |La versión del módulo de PowerShell que contiene el recurso de DSC. |
| DscConfigurationName_s |El nombre de la configuración aplicada al nodo. |
| ErrorCode_s | El código de error si se produjo un error en el recurso. |
| ErrorMessage_s |El mensaje de error si se produjo un error en el recurso. |
| DscResourceDuration_d |El tiempo, en segundos, durante el que se ejecutó el recurso de DSC. |
| SourceSystem | Cómo Log Analytics ha recopilado los datos. Siempre *Azure* para Diagnósticos de Azure. |
| ResourceId |Especifica la cuenta de Azure Automation. |
| ResultDescription | La descripción de esta operación. |
| SubscriptionId | El identificador de la suscripción de Azure (GUID) para la cuenta de Automation. |
| ResourceGroup | Nombre del grupo de recursos de la cuenta de Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID que es el identificador de correlación del informe de cumplimiento. |

## <a name="summary"></a>Resumen

Al enviar los datos de DSC de Automation a Log Analytics, puede obtener más información del estado de los nodos de DSC de Automation mediante las siguientes acciones:

* Establecimiento de alertas que le notificarán cuando haya un problema
* Uso de vistas personalizadas y de consultas de búsqueda para visualizar los resultados de runbook, el estado del trabajo de runbook y otras métricas o indicadores clave relacionados.  

Log Analytics proporciona mayor visibilidad operativa para los datos de DSC de Automation y puede ayudar a resolver incidentes con mayor rapidez.  

## <a name="next-steps"></a>Pasos siguientes

* Para aprender a crear diferentes consultas de búsqueda y a revisar los registros de DSC de Automation con Log Analytics, consulte el artículo [Búsqueda de datos mediante búsquedas de registros](../log-analytics/log-analytics-log-searches.md)
* Para más información acerca del uso de DSC de Azure Automation, consulte [Introducción al DSC de Automatización de Azure](automation-dsc-getting-started.md)
* Para más información acerca de Log Analytics de OMS y de los orígenes de recopilación de datos, consulte [Conexión de Almacenamiento de Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)

