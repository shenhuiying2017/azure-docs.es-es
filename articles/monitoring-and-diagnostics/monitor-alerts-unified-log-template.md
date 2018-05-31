---
title: Creación de una alerta de registro con una plantilla de Resource Manager | Microsoft Docs
description: Aprenda a crear una alerta de registro mediante una plantilla de Azure Resource Manager y la API.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 14a2560d91fd0f8dcc729b32c7155c4b74aa8aa1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204165"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Creación de una alerta de registro con una plantilla de Resource Manager
En este artículo se muestra cómo se pueden administrar [alertas de registro](monitor-alerts-unified-log.md) mediante programación a escala en Azure con la [plantilla de Azure Resource Manager](..//azure-resource-manager/resource-group-authoring-templates.md) a través de [Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) y la [CLI de Azure](../azure-resource-manager/resource-group-template-deploy-cli.md). Actualmente, Alertas de Azure admite las alertas de registro en consultas de [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) y [Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>Administración de alertas de registro en Log Analytics
Las alertas de registro de [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) se integran en la [nueva experiencia de alertas de Azure](monitoring-overview-unified-alerts.md); aunque todavía se ejecuta fuera de las API de Log Analytics y sigue siendo compatible con el esquema usado anteriormente para administrar [alertas en el portal de OMS](..//log-analytics/log-analytics-alerts-creating.md).

> [!NOTE]
> A partir del 14 de mayo de 2018, todas las alertas de un área de trabajo comenzarán a extenderse automáticamente a Azure. Los usuarios pueden decidir si desean iniciar la extensión de alertas a Azure antes del 14 de mayo de 2018. Para más información, consulte [Extensión de alertas de OMS a Azure](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Uso de la plantilla de Azure Resource Manager
Las reglas de alerta que ejecutan una búsqueda guardada a intervalos regulares crean alertas de Log Analytics. Si los resultados de la consulta coinciden con los criterios especificados, se crea un registro de alertas y se ejecutan una o varias acciones. 

Existen plantillas de recursos para la [búsqueda guardada de Log Analytics](../log-analytics/log-analytics-log-searches.md) y las [alertas de Log Analytics](../log-analytics/log-analytics-alerts.md) disponibles en la sección Log Analytics de la documentación. Obtenga más información sobre cómo [Adding Log Analytics saved searches and alerts](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md) (Adición de búsquedas y alertas guardadas de Log Analytics); que incluye ejemplos ilustrativos además de detalles del esquema.

### <a name="using-resource-template-via-apipowershell"></a>Uso de la plantilla de recursos a través de Powershell o API
La API de REST de alertas de Log Analytics es de tipo RESTful y se tiene acceso a ella mediante la API de REST de Azure Resource Manager. Luego se puede obtener acceso a la API desde una línea de comandos de PowerShell y se generarán resultados de la búsqueda en formato JSON, lo que le permite usar los resultados de muchas formas distintas mediante programación.

Obtenga más información al respecto en [Creación y administración de reglas de alerta de Log Analytics con la API de REST](../log-analytics/log-analytics-api-alerts.md); que incluye ejemplos de acceso a la API de Powershell.

## <a name="managing-log-alert-on-application-insights"></a>Administración de alertas de registro en Application Insights
Las alertas de registro de Azure Application Insights se han incorporado como parte de las nuevas alertas de Azure en Azure Monitor. Por lo tanto, se ejecutan en la API de Azure Monitor como grupo de operaciones de REST de [reglas de consulta programada](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

### <a name="using-azure-resource-manager-template"></a>Uso de la plantilla de Azure Resource Manager
Las alertas de recursos de Application Insights tienen un tipo de `Microsoft.Insights/scheduledQueryRules/`. Para más información sobre este tipo de recurso, vea [Azure Monitor: referencia de la API de reglas de consulta programada](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

Esta es la estructura de la plantilla de recursos basada en la [creación de reglas de consulta programada](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate), con datos de ejemplo establecidos como variables.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> El campo de etiqueta con vínculo oculto al recurso de destino es obligatorio con el uso de la plantilla de recursos o la llamada API de [reglas de consulta programada ](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/). 

El json del ejemplo anterior puede guardarse como (digamos) sampleScheduledQueryRule.json a los efectos de este tutorial y puede implementarse con [Azure Resource Manager en Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Uso de la plantilla de recursos a través de Powershell o la CLI
Azure Monitor: la API de reglas de consulta programadas es una API de REST totalmente compatible con la API de REST de Azure Resource Manager. Por lo tanto, se puede utilizar a través de Powershell con el cmdlet de Resource Manager y con la CLI de Azure.

Seguidamente se muestra la utilización de la plantilla de recursos de ejemplo mostrada anteriormente (sampleScheduledQueryRule.json) a través del cmdlet de PowerShell de Azure Resource Manager:
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Seguidamente se muestra la utilización de la plantilla de recursos de ejemplo mostrada anteriormente (sampleScheduledQueryRule.json) a través del comando de Azure Resource Manager en la CLI de Azure:

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Si la operación se realiza correctamente, se devolverá 201 para indicar que se ha creado la regla de alertas o 200 si se ha modificado una regla de alerta existente.


## <a name="next-steps"></a>Pasos siguientes
* Conocer las [acciones de webhook para alertas de registro](monitor-alerts-unified-log-webhook.md)
* Obtenga información sobre las nuevas [Alertas de Azure](monitoring-overview-unified-alerts.md)
* Más información sobre [Application Insights](../application-insights/app-insights-analytics.md)
* Más información sobre [Log Analytics](../log-analytics/log-analytics-overview.md).   
