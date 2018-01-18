---
title: Comprender el esquema de webhook usado en las alertas del registro de actividad | Microsoft Docs
description: "Obtenga información acerca del esquema del archivo JSON que se publica en una dirección URL de webhook cuando se activa una alerta del registro de actividad."
author: johnkemnetz
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: 7816efd44c01c3ed60c95d8699042f89cf6de5ec
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooks para alertas del registro de actividad de Azure
Como parte de la definición de un grupo de acciones, se pueden configurar puntos de conexión de webhook para recibir notificaciones de alertas del registro de actividad. Los webhooks permiten enrutar estas notificaciones a otros sistemas para su procesamiento posterior o acciones personalizadas. Este artículo muestra el aspecto de la carga útil para HTTP POST a un webhook.

Para más información sobre las alertas del registro de actividad, consulte cómo [crear alertas del registro de actividad de Azure](monitoring-activity-log-alerts.md).

Para obtener información sobre los grupos de acciones, consulte cómo [crear grupos de acciones](monitoring-action-groups.md).

## <a name="authenticate-the-webhook"></a>Autenticación del webhook
El webhook puede usar opcionalmente autorización basada en token para la autenticación. El identificador URI del webhook se guarda con un identificador de token, por ejemplo, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Esquema de carga
La carga útil JSON incluida en la operación POST difiere según el campo data.context.activityLog.eventSource de la carga útil.

###<a name="common"></a>Común
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
###<a name="administrative"></a>Administrativo
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
###<a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
    "status": "Activated",
    "context": {
        "activityLog": {
        "channels": "Admin",
        "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
        "description": "Active: Virtual Machines - Australia East",
        "eventSource": "ServiceHealth",
        "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
        "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
        "level": "Informational",
        "operationName": "Microsoft.ServiceHealth/incident/action",
        "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
        "properties": {
            "title": "Virtual Machines - Australia East",
            "service": "Virtual Machines",
            "region": "Australia East",
            "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
            "incidentType": "Incident",
            "trackingId": "0NIH-U2O",
            "impactStartTime": "2017-10-18T02:48:00.0000000Z",
            "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
            "defaultLanguageTitle": "Virtual Machines - Australia East",
            "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
            "stage": "Active",
            "communicationId": "636439673646212912",
            "version": "0.1.1"
        },
        "status": "Active",
        "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
        "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Para obtener detalles del esquema específico sobre alertas del registro de actividad de notificaciones de mantenimiento del servicio, consulte [Notificaciones de mantenimiento del servicio](monitoring-service-notifications.md). Además, puede obtener información acerca de cómo [configurar notificaciones webhook del estado del servicio con las soluciones de administración de problemas existentes](../service-health/service-health-alert-webhook-guide.md).

Para obtener información del esquema específico en todas las otras alertas del registro de actividad, consulte [Información general sobre el registro de actividad de Azure](monitoring-overview-activity-logs.md).

| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| status |Usado para alertas de métrica. Siempre se establece en "activated" para las alertas del registro de actividad. |
| contexto |Contexto del evento |
| resourceProviderName |Proveedor de recursos del recurso afectado |
| conditionType |Siempre "Event" |
| Nombre |Nombre de la regla de alerta. |
| id |Identificador de recurso de la alerta |
| Descripción |Descripción de la alerta establecida al crear la alerta. |
| subscriptionId |Identificador de suscripción de Azure |
| timestamp |Cuándo generó el evento el servicio de Azure que procesó la solicitud |
| ResourceId |Identificador de recurso del recurso afectado. |
| resourceGroupName |Nombre del grupo de recursos del recurso afectado. |
| propiedades |Conjunto de pares `<Key, Value>` (es decir, `Dictionary<String, String>`) que incluye detalles sobre el evento. |
| event |Elemento que contiene metadatos sobre el evento. |
| authorization |Las propiedades del Control de acceso basado en rol del evento. Estas propiedades normalmente incluyen la acción, el rol y el ámbito. |
| categoría |Categoría del evento. Los valores admitidos incluyen: Administrative, Alert, Security, ServiceHealth y Recommendation. |
| caller |Dirección de correo electrónico del usuario que realizó la operación, la notificación de UPN o la notificación de SPN basada en la disponibilidad. Puede ser null para ciertas llamadas del sistema. |
| correlationId |Normalmente, un GUID en formato de cadena. Los eventos con correlationId pertenecen a la misma acción de mayor tamaño y suelen compartir un campo correlationId. |
| eventDescription |Descripción de texto estático del evento |
| eventDataId |Identificador único para el evento |
| eventSource |Nombre de la infraestructura o el servicio de Azure que generó el evento |
| httpRequest |La solicitud normalmente incluye clientRequestId, clientIpAddress y el método HTTP (por ejemplo, PUT). |
| level |Uno de los siguientes valores: Critical, Error, Warning e Informational. |
| operationId |Por lo general, un GUID compartido entre los eventos correspondientes a una sola operación |
| operationName |Nombre de la operación. |
| propiedades |Propiedades del evento. |
| status |String. Estado de la operación. Entre los valores habituales, se incluyen Started, In Progress, Succeeded, Failed, Active y Resolved. |
| subStatus |Normalmente, incluye el código de estado HTTP de la llamada de REST correspondiente. También podría incluir otras cadenas que describen un subestado. Entre los valores de subestado habituales se incluyen OK (código de estado HTTP: 200), Created (código de estado HTTP: 201), Accepted (código de estado HTTP: 202), No Content (código de estado HTTP: 204), Bad Request (código de estado HTTP: 400), Not Found (código de estado HTTP: 404), Conflict (código de estado HTTP: 409), Internal Server Error (código de estado HTTP: 500), Service Unavailable (código de estado HTTP: 503) y Gateway Timeout (código de estado HTTP: 504). |

## <a name="next-steps"></a>pasos siguientes
* [Más información sobre el registro de actividad](monitoring-overview-activity-logs.md).
* [Ejecución de scripts de Azure Automation (runbooks) en alertas de Azure](http://go.microsoft.com/fwlink/?LinkId=627081).
* [Uso de una aplicación lógica para enviar un SMS a través de Twilio desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta del registro de actividad.
* [Uso de una aplicación lógica para enviar un mensaje de Slack desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta del registro de actividad.
* [Uso de una aplicación lógica para enviar un mensaje a una cola de Azure desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta del registro de actividad.
