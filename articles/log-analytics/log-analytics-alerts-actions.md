---
title: Respuesta a las alertas en OMS Log Analytics | Microsoft Docs
description: "Las alertas de Log Analytics identifican información importante en el repositorio de OMS y pueden avisarle proactivamente de problemas o invocar acciones para intentar corregirlos.  En este artículo se describe cómo crear una regla de alerta y los detalles de las distintas acciones que pueden realizar."
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
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fdf22ff85a3a76be5de50632c4948df44c2312df
ms.openlocfilehash: b8731e1fe48b7d809b113eb5273e3962542b8f34
ms.lasthandoff: 03/01/2017


---

# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Adición de acciones a reglas de alerta en Log Analytics
Cuando se [crea una alerta en Log Analytics](log-analytics-alerts.md), tiene la opción de [configurar la regla de alerta](log-analytics-alerts.md) para realizar una o varias acciones.  En este artículo se describen las diferentes acciones que están disponibles y los detalles sobre la configuración de cada tipo.

| Acción | Descripción |
|:--|:--|
| [Correo electrónico](#email-actions) |    Envía un correo electrónico con los detalles de la alerta a uno o varios destinatarios. |
| [Webhook](#webhook-actions) | Invoca un proceso externo a través de una sola solicitud HTTP POST. |
| [Runbook](#runbook-actions) | Inicia un runbook en Azure Automation. |


## <a name="email-actions"></a>Acciones de correo electrónico
Las acciones de correo electrónico envían un correo electrónico con los detalles de la alerta a uno o varios destinatarios.  Puede especificar el asunto del mensaje de correo, pero su contenido es un formato estándar construido por Log Analytics.  En él se incluye información de resumen, como el nombre de la alerta, además de los detalles de hasta diez registros devueltos por la búsqueda de registros.  También se incluye un vínculo a una búsqueda de registros de Log Analytics que devolverá todo el conjunto de registros de esa consulta.   El remitente del mensaje de correo es el *equipo de Microsoft Operations Management Suite&lt;noreply@oms.microsoft.com&gt;*. 

Las acciones de correo electrónico requieren las propiedades de la siguiente tabla.

| Propiedad | Descripción |
|:--- |:--- |
| Asunto |El asunto del correo electrónico.  No se puede modificar el cuerpo del mensaje de correo. |
| Recipients |Direcciones de todos los destinatarios de correo electrónico.  Si especifica más de una dirección, separe las direcciones con un punto y coma (;). |


## <a name="webhook-actions"></a>Acciones de webhook

Las acciones de Webhook permiten invocar un proceso externo a través de una sola solicitud HTTP POST.  El servicio que se llama debe admitir webhooks y determinar cómo utilizará toda la carga que reciba.  También puede llamar a una API de REST que no admita específicamente webhooks mientras la solicitud esté en un formato que entienda la API.  Ejemplos de uso de un webhook en respuesta a una alerta donde se envía un mensaje en [Slack](http://slack.com) o se crea un incidente en [PagerDuty](http://pagerduty.com/).  Puede encontrar un tutorial completo sobre la creación de una regla de alerta con un webhook para llamar a Slack en [Webhooks en alertas de Log Analytics](log-analytics-alerts-webhooks.md).

Las acciones de webhook requieren las propiedades de la siguiente tabla.

| Propiedad | Descripción |
|:--- |:--- |
| Dirección URL de Webhook |La dirección URL del webhook. |
| Carga de JSON personalizada |Carga personalizada para enviar con el webhook.  Consulte a continuación para más información. |


Los webhooks incluyen una dirección URL y una carga en formato JSON que son los datos enviados al servicio externo.  De forma predeterminada, la carga incluirá los valores en la tabla siguiente.  Puede optar por reemplazar esta carga con una personalizada de su propiedad.  En ese caso, puede utilizar las variables de la tabla para cada uno de los parámetros para incluir su valor en la carga personalizada.

| Parámetro | Variable | Descripción |
|:--- |:--- |:--- |
| AlertRuleName |#AlertRuleName |Nombre de la regla de alerta. |
| AlertThresholdOperator |#thresholdoperator |Operador de umbral para la regla de alerta.  *Mayor que* o *menor que*. |
| AlertThresholdValue |#thresholdvalue |Valor de umbral para la regla de alerta. |
| LinkToSearchResults |#LinkToSearchResults |Vincular a la búsqueda de registros de Log Analytics que devuelve los registros de la consulta que creó la alerta. |
| ResultCount |#searchresultcount |Número de registros en los resultados de la búsqueda. |
| SearchIntervalEndtimeUtc |#SearchIntervalEndtimeUtc |Hora de finalización de la consulta en formato UTC. |
| SearchIntervalInSeconds |#searchinterval |Período de tiempo para la regla de alerta. |
| SearchIntervalStartTimeUtc |#SearchIntervalStartTimeUtc |Hora de inicio para la consulta en formato UTC. |
| SearchQuery |#SearchQuery |Consulta de búsqueda de registros utilizada por la regla de alerta. |
| SearchResults |Consulte a continuación |Registros devueltos por la consulta en formato JSON.  Limitado a los primeros 5000 registros. |
| WorkspaceID |#WorkspaceID |Identificador del área de trabajo de OMS. |

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


Puede recorrer un ejemplo completo de creación de una regla de alerta con un webhook para iniciar un servicio externo en [Webhooks en alertas de Log Analytics](log-analytics-alerts-webhooks.md).

## <a name="runbook-actions"></a>Acciones de runbook
Las acciones de runbook inician un runbook en Automatización de Azure.  Para usar este tipo de acción, debe tener la [solución de Automatización](log-analytics-add-solutions.md) instalada y configurada en el área de trabajo de OMS.  Puede seleccionar entre los runbooks de la cuenta de automatización que configuró en la solución de Automatización.

Las acciones de runbook requieren las propiedades de la siguiente tabla.

| Propiedad | Descripción |
|:--- |:---|
| Runbook | El runbook que desea iniciar cuando se crea una alerta. |
| Ejecutar en | Especifique **Azure** para ejecutar el runbook en la nube.  Especifique **Hybrid Worker** para ejecutar el runbook en un agente con [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md ) instalado.  |

Las acciones de runbook inician el runbook mediante un [webhook](../automation/automation-webhooks.md).  Al crear la regla de alerta, se creará automáticamente un nuevo webhook para el runbook con el nombre **OMS Alert Remediation** seguido de un GUID.  

No se pueden llenar directamente los parámetros del runbook, pero el [parámetro $WebhookData](../automation/automation-webhooks.md) incluirá los detalles de la alerta, como los resultados de la búsqueda de registros que lo crearan.  El runbook tendrá que definir **$WebhookData** como parámetro para tener acceso a las propiedades de la alerta.  Los datos de la alerta están disponibles en formato json en una sola propiedad denominada **SearchResults** de la propiedad **RequestBody** de **$WebhookData**.  Esta tendrá las propiedades de la siguiente tabla.

| Nodo | Descripción |
|:--- |:--- |
| id |Ruta de acceso y el GUID de la búsqueda. |
| __metadata |Información sobre la alerta, como el número de registros y el estado de los resultados de búsqueda. |
| value |Entrada independiente para cada registro en los resultados de búsqueda.  Los detalles de la entrada coincidirán con las propiedades y los valores del registro. |

Por ejemplo, el siguiente runbook podría extraer los registros devueltos por la búsqueda de registros y asignar propiedades diferentes según el tipo de cada registro.  Tenga en cuenta que el runbook se inicia mediante la conversión de **RequestBody** de json, por lo que puede actuar como un objeto de PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

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


## <a name="next-steps"></a>Pasos siguientes
- Complete un tutorial para [configurar un webhook](log-analytics-alerts-webhooks.md) con una regla de alerta.  
- Aprenda a escribir [runbooks en Automatización de Azure](https://azure.microsoft.com/documentation/services/automation) para solucionar los problemas identificados por las alertas.
