---
title: Uso de la API de REST de alertas de Log Analytics de OMS
description: "Con la API de REST de alertas de Log Analytics se pueden crear y administrar alertas de Log Analytics, que forma parte de Operations Management Suite (OMS).  En este artículo encontrará información detallada sobre la API y varios ejemplos para realizar distintas operaciones."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ce72ffef4394bf3bbe39fa420c4fcaa965ae35c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Creación y administración de reglas de alerta de Log Analytics con la API de REST
Con la API de REST de alertas de Log Analytics se pueden crear y administrar alertas de Operations Management Suite (OMS).  En este artículo encontrará información detallada sobre la API y varios ejemplos para realizar distintas operaciones.

La API de REST de búsqueda de Log Analytics es de tipo RESTful y se puede obtener acceso a ella a través de la API de REST de Azure Resource Manager. En este documento encontrará ejemplos donde se tiene acceso a la API desde una línea de comandos de PowerShell a través de [ARMClient](https://github.com/projectkudu/ARMClient), una herramienta de línea de comandos de código abierto que simplifica la tarea de invocar a la API de Azure Resource Manager. El uso de ARMClient y PowerShell es una de las muchas opciones para tener acceso a la API de búsqueda de Log Analytics. Con estas herramientas, puede usar la API de Azure Resource Manager de RESTful para realizar llamadas a las áreas de trabajo de OMS y ejecutar comandos de búsqueda dentro de ellas. La API generará resultados de búsqueda, en formato JSON, lo que le permite usar los resultados de búsqueda de muchas formas distintas mediante programación.

## <a name="prerequisites"></a>Requisitos previos
Actualmente, solo se pueden crear alertas con una búsqueda guardada en Log Analytics.  Para obtener más información, consulte [API de búsqueda de registros de Log Analytics](log-analytics-log-search-api.md) .

## <a name="schedules"></a>Programaciones
Una búsqueda guardada puede tener una o varias programaciones. La programación define la frecuencia con que se realiza la búsqueda y el intervalo de tiempo en el que se identifican los criterios.
Las programaciones tienen las propiedades de la siguiente tabla.

| Propiedad | Descripción |
|:--- |:--- |
| Intervalo |Frecuencia con que se realiza la búsqueda. Se mide en minutos. |
| QueryTimeSpan |Intervalo de tiempo en el que se evalúan los criterios. Debe ser igual o mayor que Intervalo. Se mide en minutos. |
| Versión |Versión de API en uso.  Actualmente, siempre debe estar establecida en 1. |

Por ejemplo, en una consulta de evento con un valor de Intervalo de 15 minutos y un valor de Timespan de 30 minutos, la consulta se ejecutaría cada 15 minutos y se desencadenaría una alerta si los criterios siguieran evaluándose como True durante un intervalo de 30 minutos.

### <a name="retrieving-schedules"></a>Recuperar programaciones
Use el método Get para recuperar todas las programaciones de una búsqueda guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Use el método Get con un identificador de programación para recuperar una programación concreta de una búsqueda guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

La siguiente es una respuesta de ejemplo de una programación.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Creación de una programación
Use el método Put con un identificador de programación único para crear una programación nueva.  Tenga en cuenta que dos programaciones no pueden tener el mismo identificador, aunque estén asociados a diferentes búsquedas guardadas.  Al crear una programación en la consola de OMS, se crea un GUID para el identificador de programación.

> [!NOTE]
> El nombre de todas las búsquedas guardadas, programaciones y acciones creadas con Log Analytics API debe estar en minúsculas.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Edición de una programación
Utilice el método Put con un identificador de programación existente para modificar la programación de la misma búsqueda guardada.  El cuerpo de la solicitud debe incluir el valor etag de la programación.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Eliminar programaciones
Para eliminar una programación, use el método Delete con un identificador de programación.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Acciones
Una programación puede tener varias acciones. Una acción puede definir uno o varios procesos que se van a realizar (como enviar un correo o iniciar un Runbook), o bien puede definir un umbral que determina si los resultados de una búsqueda coinciden con algunos criterios.  Algunas acciones definen ambos aspectos, de forma que los procesos se realizan cuando se alcance el umbral.

Todas las acciones tienen las propiedades de la siguiente tabla.  Los distintos tipos de alertas tienen diferentes propiedades adicionales, descritas aquí.

| Propiedad | Descripción |
|:--- |:--- |
| Tipo |Tipo de la acción.  Actualmente, los valores posibles son Alert y Webhook. |
| Nombre |Nombre para mostrar de la alerta. |
| Versión |Versión de API en uso.  Actualmente, siempre debe estar establecida en 1. |

### <a name="retrieving-actions"></a>Recuperar acciones
Use el método Get para recuperar todas las acciones de una programación.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Use el método Get con el identificador de una acción para recuperar esa acción concreta para una programación.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Crear o editar acciones
Para crear una acción, use el método Put con un identificador de acción único de la programación.  Al crear una acción en la consola de OMS, se crea un GUID para el identificador de acción.

> [!NOTE]
> El nombre de todas las búsquedas guardadas, programaciones y acciones creadas con Log Analytics API debe estar en minúsculas.

Utilice el método Put con un identificador de acción existente para modificar la programación de la misma búsqueda guardada.  El cuerpo de la solicitud debe incluir el valor etag de la programación.

El formato de solicitud para crear una acción varía según el tipo de acción. En las secciones siguientes se proporcionan ejemplos.

### <a name="deleting-actions"></a>Eliminar acciones
Use el método Delete con el identificador de acción para eliminar esa acción.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Acciones de alerta
Una programación debe tener una acción de alerta única y exclusivamente.  Las acciones de alerta tienen una o varias de las secciones de la siguiente tabla.  Cada una de ellas se describe con más detalle abajo.

| Sección | Descripción |
|:--- |:--- |
| Umbral |Criterios para establecer cuándo se va a ejecutar la acción. |
| EmailNotification |Se envía un correo electrónico a varios destinatarios. |
| Corrección |Se inicia un Runbook en Automatización de Azure para intentar corregir el problema detectado. |

#### <a name="thresholds"></a>Umbrales
Una acción de alerta debe tener un umbral única y exclusivamente.  Cuando los resultados de una búsqueda guardada coinciden con el umbral de una acción asociada a esa búsqueda, se ejecutan los demás procesos de esa acción.  Una acción también puede contener solo un umbral para que pueda usarse con acciones de otros tipos que no contienen umbrales.

Los umbrales tienen las propiedades de la siguiente tabla.

| Propiedad | Descripción |
|:--- |:--- |
| Operador |Operador de la comparación de umbral. <br> gt = Mayor que <br> lt = Menor que |
| Valor |Valor del umbral. |

Por ejemplo, en una consulta de evento con un valor de Intervalo de 15 minutos, un valor de Timespan de 30 minutos y un valor de Threshold mayor que 10, la consulta se ejecutaría cada 15 minutos y se desencadenaría una alerta si devolviera 10 eventos creados durante un intervalo de 30 minutos.

La siguiente es una respuesta de ejemplo de una acción con un solo umbral.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Use el método Put con un identificador de acción único para crear una acción de umbral para una programación.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Use el método Put con un identificador de acción existente para modificar una acción de umbral para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>Notificación por correo electrónico
Las notificaciones de correo electrónico envían correo a uno o más destinatarios.  Tienen las propiedades de la siguiente tabla.

| Propiedad | Descripción |
|:--- |:--- |
| Recipients |Lista de direcciones de correo electrónico. |
| Asunto |Asunto del correo electrónico. |
| Datos adjuntos |Actualmente no se admiten datos adjuntos, por lo que siempre aparecerá un valor “None”. |

La siguiente es una respuesta de ejemplo de una acción de notificación de correo con un umbral.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Use el método Put con un identificador de acción único para crear una acción de correo electrónico para una programación.  En el siguiente ejemplo se crea una notificación de correo electrónico con un umbral para que el correo se envíe cuando los resultados de la búsqueda guardada superen el umbral.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Use el método Put con un identificador de acción existente para modificar una acción de correo electrónico para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Acciones de corrección
Las correcciones inician un Runbook en Automatización de Azure que intenta corregir el problema identificado por la alerta.  Debe crear un webhook para el Runbook usado en una acción de corrección y, luego, especificar el URI en la propiedad WebhookUri.  Cuando esta acción se crea con la consola de OMS, se crea automáticamente un nuevo webhook para el Runbook.

Las correcciones tienen las propiedades de la siguiente tabla.

| Propiedad | Descripción |
|:--- |:--- |
| RunbookName |Nombre del Runbook. Debe coincidir con un Runbook publicado en la cuenta de automatización que configuró en la solución de Automatización en el área de trabajo de OMS. |
| WebhookUri |URI del webhook. |
| Expiry |Fecha y hora de expiración del webhook.  Si el webhook no tiene una fecha expiración, puede ser cualquier fecha futura válida. |

La siguiente es una respuesta de ejemplo de una acción de corrección con un umbral.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Use el método Put con un identificador de acción único para crear una acción de corrección para una programación.  En el siguiente ejemplo se crea una corrección con un umbral para que el Runbook se inicie cuando los resultados de la búsqueda guardada superen el umbral.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Use el método Put con un identificador de acción existente para modificar una acción de corrección para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Ejemplo
El siguiente es un ejemplo completo que ilustra cómo crear una alerta de correo electrónico.  En él, se crea una programación junto con una acción que contiene un umbral y un correo electrónico.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Acciones de webhook
Las acciones de webhook inician un proceso llamando a una dirección URL y, opcionalmente, proporcionando una carga que se va a enviar.  Son similares a las acciones de corrección, salvo por el hecho de que están pensadas para webhooks que pueden invocar procesos que no tienen que ver con Runbooks de Automatización de Azure.  También ofrecen la posibilidad extra de proporcionar una carga adicional para entregarla en el proceso remoto.

Las acciones de webhook no tienen umbral, sino que deben agregarse a una programación que tenga una acción de alerta con un umbral.  Se pueden agregar varias acciones de webhook, y todas ellas se ejecutarán cuando se alcance el umbral.

Las acciones de webhook tienen las propiedades de la siguiente tabla.

| Propiedad | Descripción |
|:--- |:--- |
| WebhookUri |Asunto del correo electrónico. |
| CustomPayload |Carga personalizada que se va a enviar al webhook.  El formato dependerá de lo que el webhook espere. |

La siguiente es una respuesta de ejemplo de una acción de webhook y una acción de alerta asociada con un umbral.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Crear o editar una acción de webhook
Use el método Put con un identificador de acción único para crear una acción de webhook para una programación.  En el siguiente ejemplo se crea una acción de webhook y una acción de alerta con un umbral para que el webhook se active cuando los resultados de la búsqueda guardada superen el umbral.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Use el método Put con un identificador de acción existente para modificar una acción de webhook para una programación.  El cuerpo de la solicitud debe incluir el valor etag de la acción.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Pasos siguientes
* Use la [API de búsqueda de registros de Log Analytics](log-analytics-log-search-api.md) en Log Analytics.

