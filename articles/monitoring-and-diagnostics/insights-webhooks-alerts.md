---
title: "Configuración de webhooks en alertas de métrica de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo redirigir alertas de Azure a otros sistemas que no sean de Azure."
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
ms.openlocfilehash: 049803e7701c68559103d9b1fa5dfacf820d0548
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Configuración de un webhook en una alerta de métrica de Azure
Puede usar los webhooks para redirigir una notificación de alerta de Azure a otros sistemas para su procesamiento posterior o acciones personalizadas. Puede usar un webhook en una alerta para redirigirla a servicios que envían SMS, registran errores, notifican a un equipo mediante servicios de chat y mensajería o llevan a cabo otras acciones. 

En este artículo se describe cómo establecer un webhook en una alerta de métrica de Azure. También muestra el aspecto de la carga útil para HTTP POST a un webhook. Para obtener información sobre la configuración y el esquema de una alerta de registro de actividad de Azure (alerta de eventos), consulte [Llamada a un webhook cuando se activan alertas del registro de actividades de Azure](insights-auditlog-to-webhook-email.md).

Las alertas de Azure usan HTTP POST para enviar el contenido de la alerta en formato JSON a un URI de webhook que se proporciona al crear la alerta. El esquema se define más adelante en este artículo. Este URI debe ser un punto de conexión HTTP o HTTPS válido. Azure envía una entrada por cada solicitud cuando se activa una alerta.

## <a name="configure-webhooks-via-the-azure-portal"></a>Configuración de webhooks mediante Azure Portal
Puede agregar o actualizar el URI de webhook, en [Azure Portal](https://portal.azure.com/), vaya a **Create/Update Alerts** (Crear/Actualizar alertas).

![Adición de un panel de regla de alerta](./media/insights-webhooks-alerts/Alertwebhook.png)

También puede configurar una alerta para enviarla a un URI de webhook mediante los [cmdlets de Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), la [CLI multiplataforma](insights-cli-samples.md#work-with-alerts) o la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Autenticación del webhook
El webhook puede autenticarse mediante una autorización basada en token. El URI del webhook se guarda con un identificador de token. Por ejemplo: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Esquema de carga
La operación POST contiene el siguiente esquema y carga útil de JSON para todas las alertas basadas en métricas:

```JSON
{
    "WebhookName": "Alert1515515157799",
    "RequestBody": {
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
}
```


| Campo | Obligatorio | Conjunto fijo de valores | Notas |
|:--- |:--- |:--- |:--- |
| status |Y |Activado, Resuelto |Estado de la alerta en función de las condiciones que haya establecido. |
| contexto |Y | |Contexto de la alerta |
| timestamp |Y | |La hora en la que se desencadenó la alerta. |
| id |Y | |Cada regla de alerta tiene un identificador único. |
| Nombre |Y | |Nombre de la alerta |
| Descripción |Y | |Descripción de la alerta. |
| conditionType |Y |Métrica, Evento |Se admiten dos tipos de alertas: métrica y evento. Las alertas de métrica se basan en una condición de métrica. Las alertas de evento se basan en un evento del registro de actividad. Use este valor para comprobar si la alerta está basada en una métrica o en un evento. |
| condition |Y | |Los campos específicos que buscar en función del campo **conditionType**. |
| metricName |Para alertas de métricas | |El nombre de la métrica que define qué supervisa la regla. |
| metricUnit |Para alertas de métricas |Bytes, BytesPerSecond, Count, CountPerSecond, Percent, Seconds |La unidad permitida en la métrica. Consulte [Valores permitidos](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Para alertas de métricas | |Valor real de la métrica que causó la alerta |
| threshold |Para alertas de métricas | |Valor de umbral en el que se activa la alerta |
| windowSize |Para alertas de métricas | |El período de tiempo que se usa para supervisar la actividad de la alerta según el umbral. El valor debe estar comprendido entre 5 minutos y 1 día. El valor debe tener el formato de duración ISO 8601. |
| timeAggregation |Para alertas de métricas |Average, Last, Maximum, Minimum, None, Total |La manera en que se recopilan los datos se debería combinar con el tiempo. El valor predeterminado es Average. Consulte [Valores permitidos](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Para alertas de métricas | |Operador usado para comparar los datos de métrica actuales con el umbral establecido. |
| subscriptionId |Y | |El identificador de la suscripción de Azure. |
| resourceGroupName |Y | |Nombre del grupo de recursos del recurso afectado. |
| resourceName |Y | |Nombre del recurso afectado. |
| resourceType |Y | |Tipo del recurso afectado. |
| ResourceId |Y | |Identificador de recurso del recurso afectado. |
| resourceRegion |Y | |Región o ubicación del recurso afectado. |
| portalLink |Y | |Vínculo directo a la página de resumen de recursos del portal. |
| propiedades |N |Opcional |Un conjunto de pares clave/valor que incluye detalles sobre el evento. Por ejemplo, `Dictionary<String, String>`. El campo de propiedades es opcional. En un flujo de trabajo basado en una aplicación lógica o una interfaz de usuario personalizada, los usuarios pueden especificar pares clave/valores que se pueden pasar con la carga útil. Una forma alternativa para pasar propiedades personalizadas a la webhook es mediante el propio URI de webhook (como parámetros de consulta). |

> [!NOTE]
> Solo se puede establecer el campo de **propiedades** mediante la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>pasos siguientes
* Aprenda más sobre los webhooks y las alertas de Azure en el vídeo [Integración de las alertas de Azure con PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080).
* Obtenga información sobre cómo [ejecutar scripts de Azure Automation (Runbooks) en alertas de Azure](http://go.microsoft.com/fwlink/?LinkId=627081).
* Aprenda a [usar una aplicación lógica para enviar un mensaje SMS a través de Twilio desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Sepa cómo [usar una aplicación lógica para enviar un mensaje de Slack desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Obtenga información sobre cómo [usar una aplicación lógica para enviar un mensaje a una cola de Azure desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).
