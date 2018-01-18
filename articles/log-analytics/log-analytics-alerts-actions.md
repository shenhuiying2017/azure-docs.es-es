---
title: Respuesta a las alertas en Azure Log Analytics | Microsoft Docs
description: "Las alertas de Log Analytics identifican información importante en el área de trabajo de Azure y pueden avisarlo proactivamente de problemas o invocar acciones para intentar corregirlos.  En este artículo se describe cómo crear una regla de alerta y los detalles de las distintas acciones que pueden realizar."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e80481f074bc196caae7c03f54134eaef0fb46d5
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Adición de acciones a reglas de alerta en Log Analytics
Cuando se [crea una alerta en Log Analytics](log-analytics-alerts.md), tiene la opción de [configurar la regla de alerta](log-analytics-alerts.md) para realizar una o varias acciones.  En este artículo se describen las diferentes acciones que están disponibles y los detalles sobre la configuración de cada tipo.

| . | DESCRIPCIÓN |
|:--|:--|
| [Correo electrónico](#email-actions) | Envía un correo electrónico con los detalles de la alerta a uno o varios destinatarios. |
| [Webhook](#webhook-actions) | Invoca un proceso externo a través de una sola solicitud HTTP POST. |
| [Runbook](#runbook-actions) | Inicia un runbook en Azure Automation. |


## <a name="email-actions"></a>Acciones de correo electrónico
Las acciones de correo electrónico envían un correo electrónico con los detalles de la alerta a uno o varios destinatarios.  Puede especificar el asunto del mensaje de correo, pero su contenido es un formato estándar construido por Log Analytics.  En él se incluye información de resumen, como el nombre de la alerta, además de los detalles de hasta diez registros devueltos por la búsqueda de registros.  También se incluye un vínculo a una búsqueda de registros de Log Analytics que devolverá todo el conjunto de registros de esa consulta.   El remitente del mensaje de correo es el *equipo de Microsoft Operations Management Suite&lt;noreply@oms.microsoft.com&gt;*. 

Las acciones de correo electrónico requieren las propiedades de la siguiente tabla.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| Asunto |El asunto del correo electrónico.  No se puede modificar el cuerpo del mensaje de correo. |
| Recipients |Direcciones de todos los destinatarios de correo electrónico.  Si especifica más de una dirección, separe las direcciones con un punto y coma (;). |


## <a name="webhook-actions"></a>Acciones de webhook

Las acciones de Webhook permiten invocar un proceso externo a través de una sola solicitud HTTP POST.  El servicio que se llama debe admitir webhooks y determinar cómo utilizará toda la carga que reciba.  También puede llamar a una API de REST que no admita específicamente webhooks mientras la solicitud esté en un formato que entienda la API.  Ejemplos de uso de un webhook en respuesta a una alerta donde se envía un mensaje en [Slack](http://slack.com) o se crea un incidente en [PagerDuty](http://pagerduty.com/).  Puede encontrar un tutorial completo sobre la creación de una regla de alerta con un webhook para llamar a Slack en [Webhooks en alertas de Log Analytics](log-analytics-alerts-webhooks.md).

Las acciones de webhook requieren las propiedades de la siguiente tabla.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| Dirección URL de Webhook |La dirección URL del webhook. |
| Carga de JSON personalizada |Carga personalizada para enviar con el webhook.  Consulte a continuación para más información. |


Los webhooks incluyen una dirección URL y una carga en formato JSON que son los datos enviados al servicio externo.  De forma predeterminada, la carga incluirá los valores en la tabla siguiente.  Puede optar por reemplazar esta carga con una personalizada de su propiedad.  En ese caso, puede utilizar las variables de la tabla para cada uno de los parámetros para incluir su valor en la carga personalizada.

>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), la carga del webhook ha cambiado.  Los detalles del formato se encuentran en la [API de REST de Azure Log Analytics](https://aka.ms/loganalyticsapiresponse).  Puede ver una muestra en la sección [Ejemplos](#sample-payload) más adelante.

| . | Variable | DESCRIPCIÓN |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nombre de la regla de alerta. |
| AlertThresholdOperator |ThresholdOperator |Operador de umbral para la regla de alerta.  *Mayor que* o *menor que*. |
| AlertThresholdValue |#thresholdvalue |Valor de umbral para la regla de alerta. |
| LinkToSearchResults |#linktosearchresults |Vincular a la búsqueda de registros de Log Analytics que devuelve los registros de la consulta que creó la alerta. |
| ResultCount |#searchresultcount |Número de registros en los resultados de la búsqueda. |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |Hora de finalización de la consulta en formato UTC. |
| SearchIntervalInSeconds |#searchinterval |Período de tiempo para la regla de alerta. |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |Hora de inicio para la consulta en formato UTC. |
| SearchQuery |#searchquery |Consulta de búsqueda de registros utilizada por la regla de alerta. |
| SearchResults |Consulte a continuación |Registros devueltos por la consulta en formato JSON.  Limitado a los primeros 5000 registros. |
| WorkspaceID |#workspaceid |Identificador del área de trabajo de Log Analytics. |

Por ejemplo, podría especificar la siguiente carga personalizada que incluye un único parámetro denominado *text*.  El servicio al que llama este webhook estaría esperando este parámetro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Esta carga de ejemplo se resolvería en algo similar a lo siguiente al enviarse al webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Para incluir resultados de búsqueda en una carga personalizada, agregue la siguiente línea como una propiedad de nivel superior en la carga de json.  

    "IncludeSearchResults":true

Por ejemplo, para crear una carga personalizada que incluya solo el nombre de la alerta y los resultados de la búsqueda, puede utilizar lo siguiente. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Puede recorrer un ejemplo completo de creación de una regla de alerta con un webhook para iniciar un servicio externo en [Creación de una acción de alerta de webhook en Log Analytics de OMS para enviar mensajes a Slack](log-analytics-alerts-webhooks.md).


## <a name="runbook-actions"></a>Acciones de runbook
Las acciones de runbook inician un runbook en Azure Automation.  Para usar este tipo de acción, debe tener la [solución de Automation](log-analytics-add-solutions.md) instalada y configurada en el área de trabajo de Log Analytics.  Puede seleccionar entre los runbooks de la cuenta de Automation que configuró en la solución de Automation.

Las acciones de runbook requieren las propiedades de la siguiente tabla.

| Propiedad | DESCRIPCIÓN |
|:--- |:---|
| Runbook | El runbook que desea iniciar cuando se crea una alerta. |
| Ejecutar en | Especifique **Azure** para ejecutar el runbook en la nube.  Especifique **Hybrid Worker** para ejecutar el runbook en un agente con [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md ) instalado.  |

Las acciones de runbook inician el runbook mediante un [webhook](../automation/automation-webhooks.md).  Al crear la regla de alerta, se creará automáticamente un nuevo webhook para el runbook con el nombre **OMS Alert Remediation** seguido de un GUID.  

No se pueden llenar directamente los parámetros del runbook, pero el [parámetro $WebhookData](../automation/automation-webhooks.md) incluirá los detalles de la alerta, como los resultados de la búsqueda de registros que lo crearan.  El runbook tendrá que definir **$WebhookData** como parámetro para tener acceso a las propiedades de la alerta.  Los datos de alerta están disponibles en formato json en una propiedad única denominada **SearchResult** (para las acciones de runbook y acciones de webhook con carga estándar) o **SearchResults** (acciones de webhook con carga personalizada, incluido **IncludeSearchResults ": true**) en la propiedad **RequestBody** de **$WebhookData**.  Esta tendrá las propiedades de la siguiente tabla.

>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), la carga del runbook ha cambiado.  Los detalles del formato se encuentran en la [API de REST de Azure Log Analytics](https://aka.ms/loganalyticsapiresponse).  Puede ver una muestra en la sección [Ejemplos](#sample-payload) más adelante.  

| Nodo | DESCRIPCIÓN |
|:--- |:--- |
| id |Ruta de acceso y el GUID de la búsqueda. |
| __metadata |Información sobre la alerta, como el número de registros y el estado de los resultados de búsqueda. |
| value |Entrada independiente para cada registro en los resultados de búsqueda.  Los detalles de la entrada coincidirán con las propiedades y los valores del registro. |

Por ejemplo, los siguientes runbooks podrían extraer los registros devueltos por la búsqueda de registros y asignar propiedades diferentes según el tipo de cada registro.  Tenga en cuenta que el runbook se inicia mediante la conversión de **RequestBody** de json, por lo que puede actuar como un objeto de PowerShell.

>[!NOTE]
> Estos runbook usan **SearchResult**, que es la propiedad que contiene los resultados de las acciones de runbook y acciones de webhook con carga estándar.  Si se ha llamado el runbook desde una respuesta de webhook con una carga personalizada, tendría que cambiar esta propiedad a **SearchResults**.

El siguiente runbook funcionará con la carga desde el [área de trabajo de Log Analytics heredada](log-analytics-log-search-upgrade.md).

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResult.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }

El siguiente runbook funcionará con la carga desde un [área de trabajo de Log Analytics heredada](log-analytics-log-search-upgrade.md).

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody

    # Get all metadata properties    
    $AlertRuleName = $RequestBody.AlertRuleName
    $AlertThresholdOperator = $RequestBody.AlertThresholdOperator
    $AlertThresholdValue = $RequestBody.AlertThresholdValue
    $AlertDescription = $RequestBody.Description
    $LinktoSearchResults =$RequestBody.LinkToSearchResults
    $ResultCount =$RequestBody.ResultCount
    $Severity = $RequestBody.Severity
    $SearchQuery = $RequestBody.SearchQuery
    $WorkspaceID = $RequestBody.WorkspaceId
    $SearchWindowStartTime = $RequestBody.SearchIntervalStartTimeUtc
    $SearchWindowEndTime = $RequestBody.SearchIntervalEndtimeUtc
    $SearchWindowInterval = $RequestBody.SearchIntervalInSeconds

    # Get detailed search results
    if($RequestBody.SearchResult -ne $null)
    {
        $SearchResultRows    = $RequestBody.SearchResult.tables[0].rows 
        $SearchResultColumns = $RequestBody.SearchResult.tables[0].columns;

        foreach ($SearchResultRow in $SearchResultRows)
        {   
            $Column = 0
            $Record = New-Object –TypeName PSObject 
        
            foreach ($SearchResultColumn in $SearchResultColumns)
            {
                $Name = $SearchResultColumn.name
                $ColumnValue = $SearchResultRow[$Column]
                $Record | Add-Member –MemberType NoteProperty –Name $name –Value $ColumnValue -Force
                        
                $Column++
            }

            # Include code to work with the record. 
            # For example $Record.Computer to get the computer property from the record.
            
        }
    }



## <a name="sample-payload"></a>Carga de ejemplo
En esta sección se muestra la carga de ejemplo para las acciones de webhook y runbook en una [área de trabajo de Log Analytics actualizada](log-analytics-log-search-upgrade.md) y otra heredada.

### <a name="webhook-actions"></a>Acciones de webhook
Ambos ejemplos usan **SearchResult**, que es la propiedad que contiene los resultados de las acciones de webhook con carga estándar.  Si el webhook utilizó una carga personalizada que incluye los resultados de búsqueda, esta propiedad sería **SearchResults**.

#### <a name="legacy-workspace"></a>Área de trabajo heredada.
A continuación se muestra una carga de ejemplo para una acción de webhook en una área de trabajo heredada.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Type=Usage",
    "SearchResult": {
        "id": "subscriptions/subscriptionID/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspace-workspaceID/search/SearchGUID|10.1.0.7|2017-09-27T10-30-38Z",
        "__metadata": {
        "resultType": "raw",
        "total": 1,
        "top": 2147483647,
        "RequestId": "SearchID|10.1.0.7|2017-09-27T10-30-38Z",
        "CoreSummaries": [
            {
            "Status": "Successful",
            "NumberOfDocuments": 135000000
            }
        ],
        "Status": "Successful",
        "NumberOfDocuments": 135000000,
        "StartTime": "2017-09-27T10:30:38.9453282Z",
        "LastUpdated": "2017-09-27T10:30:44.0907473Z",
        "ETag": "636421050440907473",
        "sort": [
            {
            "name": "TimeGenerated",
            "order": "desc"
            }
        ],
        "requestTime": 361
        },
        "value": [
        {
            "Computer": "-",
            "SourceSystem": "OMS",
            "TimeGenerated": "2017-09-26T13:59:59Z",
            "ResourceUri": "/subscriptions/df1ec963-d784-4d11-a779-1b3eeb9ecb78/resourcegroups/mms-eus/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
            "DataType": "Operation",
            "StartTime": "2017-09-26T13:00:00Z",
            "EndTime": "2017-09-26T13:59:59Z",
            "Solution": "LogManagement",
            "BatchesWithinSla": 8,
            "BatchesOutsideSla": 0,
            "BatchesCapped": 0,
            "TotalBatches": 8,
            "AvgLatencyInSeconds": 0.0,
            "Quantity": 0.002502,
            "QuantityUnit": "MBytes",
            "IsBillable": false,
            "MeterId": "a4e29a95-5b4c-408b-80e3-113f9410566e",
            "LinkedMeterId": "00000000-0000-0000-0000-000000000000",
            "id": "954f7083-cd55-3f0a-72cb-3d78cd6444a3",
            "Type": "Usage",
            "MG": "00000000-0000-0000-0000-000000000000",
            "__metadata": {
            "Type": "Usage",
            "TimeGenerated": "2017-09-26T13:59:59Z"
            }
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Type%3DUsage",
    "Description": null,
    "Severity": "Low"
    }


#### <a name="upgraded-workspace"></a>Área de trabajo actualizada.
A continuación se muestra una carga de ejemplo para una acción de webhook en una área de trabajo actualizada.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "workspaceID",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
                "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }


### <a name="runbooks"></a>Runbooks

#### <a name="legacy-workspace"></a>Área de trabajo heredada
A continuación se muestra una carga de ejemplo para una acción de runbook en una área de trabajo heredada.

    {
        "SearchResult": {
            "id": "subscriptions/subscriptionID/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspace-workspaceID/search/searchGUID|10.1.0.7|TimeStamp",
            "__metadata": {
                "resultType": "raw",
                "total": 1,
                "top": 2147483647,
                "RequestId": "searchGUID|10.1.0.7|2017-09-27T10-51-43Z",
                "CoreSummaries": [{
                    "Status": "Successful",
                    "NumberOfDocuments": 135000000
                }],
                "Status": "Successful",
                "NumberOfDocuments": 135000000,
                "StartTime": "2017-09-27T10:51:43.3075124Z",
                "LastUpdated": "2017-09-27T10:51:51.1002092Z",
                "ETag": "636421063111002092",
                "sort": [{
                    "name": "TimeGenerated",
                    "order": "desc"
                }],
                "requestTime": 511
            },
            "value": [{
                "Computer": "-",
                "SourceSystem": "OMS",
                "TimeGenerated": "2017-09-26T13:59:59Z",
                "ResourceUri": "/subscriptions/AnotherSubscriptionID/resourcegroups/SampleResourceGroup/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                "DataType": "Operation",
                "StartTime": "2017-09-26T13:00:00Z",
                "EndTime": "2017-09-26T13:59:59Z",
                "Solution": "LogManagement",
                "BatchesWithinSla": 8,
                "BatchesOutsideSla": 0,
                "BatchesCapped": 0,
                "TotalBatches": 8,
                "AvgLatencyInSeconds": 0.0,
                "Quantity": 0.002502,
                "QuantityUnit": "MBytes",
                "IsBillable": false,
                "MeterId": "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "LinkedMeterId": "00000000-0000-0000-0000-000000000000",
                "id": "954f7083-cd55-3f0a-72cb-3d78cd6444a3",
                "Type": "Usage",
                "MG": "00000000-0000-0000-0000-000000000000",
                "__metadata": {
                    "Type": "Usage",
                    "TimeGenerated": "2017-09-26T13:59:59Z"
                }
            }]
        }
    }

#### <a name="upgraded-workspace"></a>Área de trabajo actualizada
A continuación se muestra una carga de ejemplo para una acción de runbook en una área de trabajo actualizada.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "AutomationAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "861bd466-5400-44be-9552-5ba40823c3aa",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
            "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Critical"
    }



## <a name="next-steps"></a>pasos siguientes
- Complete un tutorial para [configurar un webhook](log-analytics-alerts-webhooks.md) con una regla de alerta.  
- Aprenda a escribir [runbooks en Azure Automation](https://azure.microsoft.com/documentation/services/automation) para solucionar los problemas identificados por las alertas.