---
title: "Configuración de webhooks en alertas de métrica de Azure | Microsoft Docs"
description: Redistribuir alertas de Azure a otros sistemas que no sean de Azure.
author: johnkemnetz
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 8b3ae540-1d19-4f3d-a635-376042f8a5bb
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: johnkem
ms.openlocfilehash: 1a885166e5c71f13da222bfc22b0fc579096c52f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Configuración de un webhook en una alerta de métrica de Azure
Los webhooks permiten enrutar una notificación de alerta de Azure a otros sistemas para su procesamiento posterior o acciones personalizadas. Puede usar un webhook en una alerta para enrutarla a servicios que envían SMS, registran errores, notifican a un equipo mediante servicios de chat y mensajería o llevan a cabo otras acciones diversas. En este artículo, se describe cómo establecer un webhook en una alerta de métrica de Azure y cuál es el aspecto de la carga útil de la operación HTTP POST a un webhook. Para más información sobre la configuración y el esquema de una alerta de registro de actividad de Azure (alerta de eventos), [consulte esta página en su lugar](insights-auditlog-to-webhook-email.md).

Las alertas de Azure envían en HTTP POST el contenido de la alerta en formato JSON (el esquema se define a continuación) a un URI de webhook que se proporciona al crear la alerta. Este URI debe ser un punto de conexión HTTP o HTTPS válido. Azure envía una entrada por cada solicitud cuando se activa una alerta.

## <a name="configuring-webhooks-via-the-portal"></a>Configuración de webhooks a través del portal
Puede agregar o actualizar el URI de webhook en la pantalla Create/Update Alerts (Crear/Actualizar alertas) en el [portal](https://portal.azure.com/).

![Agregar una regla de alerta](./media/insights-webhooks-alerts/Alertwebhook.png)

También puede configurar una alerta para enviarla a un URI de webhook mediante los [cmdlets de Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), la [CLI multiplataforma](insights-cli-samples.md#work-with-alerts) o la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticación del webhook
El webhook puede autenticarse mediante una autorización basada en token. El URI del webhook se guarda con un identificador de token, como `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Esquema de carga
La operación POST contiene el siguiente esquema y carga útil de JSON para todas las alertas basadas en métricas.

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Campo | Obligatorio | Conjunto fijo de valores | Notas |
|:--- |:--- |:--- |:--- |
| status |Y |"Activado", "Resuelto" |Estado de la alerta en función de las condiciones que haya establecido |
| contexto |Y | |Contexto de la alerta |
| timestamp |Y | |La hora en la que se desencadenó la alerta. |
| id |Y | |Cada regla de alerta tiene un id. único. |
| name |Y | |Nombre de la alerta |
| description |Y | |Descripción de la alerta |
| conditionType |Y |"Métrica", "Evento" |Se admiten dos tipos de alertas. Uno basado en una condición de métrica y otro, en un evento en el registro de actividad. Use este valor para comprobar si la alerta está basada en una métrica o en un evento. |
| condition |Y | |Los campos específicos que buscar en función del campo conditionType |
| metricName |para alertas de métricas | |El nombre de la métrica que define qué supervisa la regla. |
| metricUnit |para alertas de métricas |"Bytes", "BytesPerSecond", "Count", "CountPerSecond", "Percent", "Seconds" |La unidad permitida en la métrica. [Los valores permitidos aparecen aquí](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |para alertas de métricas | |Valor real de la métrica que causó la alerta |
| threshold |para alertas de métricas | |Valor de umbral en el que se activa la alerta |
| windowSize |para alertas de métricas | |El período de tiempo que se usa para supervisar la actividad de la alerta según el umbral. Debe estar comprendido entre 5 minutos y 1 día. Formato de duración ISO 8601. |
| timeAggregation |para alertas de métricas |"Average", "Last", "Maximum", "Minimum", "None", "Total" |La manera en que se recopilan los datos se debería combinar con el tiempo. El valor predeterminado es Average. [Los valores permitidos aparecen aquí](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |para alertas de métricas | |Operador usado para comparar los datos de métrica actuales con el umbral establecido |
| subscriptionId |Y | |Identificador de suscripción de Azure |
| resourceGroupName |Y | |Nombre del grupo de recursos del recurso afectado. |
| resourceName |Y | |Nombre del recurso afectado |
| resourceType |Y | |Tipo del recurso afectado |
| resourceId |Y | |Identificador de recurso del recurso afectado. |
| resourceRegion |Y | |Región o ubicación del recurso afectado |
| portalLink |Y | |Vínculo directo a la página de resumen de recursos del portal |
| propiedades |N |Opcional |Conjunto de pares `<Key, Value>` (es decir, `Dictionary<String, String>`) que incluye detalles sobre el evento El campo de propiedades es opcional. En un flujo de trabajo basado en una aplicación lógica o una interfaz de usuario personalizada, los usuarios pueden especificar clave/valores que se pueden pasar con la carga útil. La forma alternativa para pasar propiedades personalizadas a la webhook es mediante el propio URI de webhook (como parámetros de consulta). |

> [!NOTE]
> Solo se puede establecer el campo de propiedades mediante la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Pasos siguientes
* Aprenda más sobre los webhooks y las alertas de Azure en el vídeo [Integración de las alertas de Azure con PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
* [Ejecute scripts de Azure Automation (Runbooks) en alertas de Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Use una aplicación lógica para enviar un SMS a través de Twilio desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
* [Use una aplicación lógica para enviar un mensaje de Slack desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
* [Use una aplicación lógica para enviar un mensaje a una cola de Azure desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
