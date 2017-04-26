# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooks para alertas del registro de actividad de Azure
Como parte de la definición de un grupo de acciones, se pueden configurar puntos de conexión de Webhook para recibir notificaciones de alertas del registro de actividad. Los webhooks permiten enrutar estas notificaciones a otros sistemas para su procesamiento posterior o acciones personalizadas. Este artículo muestra el aspecto de la carga útil para HTTP POST a un webhook.

Para más información sobre la configuración y el esquema de las alertas del registro de actividad de Azure, [vea esta página en su lugar](monitoring-activity-log-alerts.md).

Para más información sobre la configuración y el esquema de los grupos de acciones, [vea esta página en su lugar](monitoring-action-groups.md).

## <a name="authenticating-the-webhook"></a>Autenticación del webhook
El weebhook se puede autenticar mediante autorización basada en token: el identificador URI de webhook se guarda con un identificador de token, por ejemplo, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

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
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                },
            }
        },
        "properties": {}
    }
}
```

Para obtener detalles de esquema concretos sobre las alertas del registro de actividad de notificación de estado de servicio, [haga clic aquí](monitoring-service-notifications.md). Para obtener detalles de esquema concretos sobre el resto de las alertas del registro de actividad, [haga clic aquí](monitoring-overview-activity-logs.md)

| Nombre del elemento | Description |
| --- | --- |
| status |Usado para alertas de métrica. Siempre se establece en "activated" para las alertas de registro de actividad. |
| contexto |Contexto del evento |
| resourceProviderName |Proveedor de recursos del recurso afectado |
| conditionType |Siempre "Event" |
| name |Nombre de la regla de alerta. |
| id |Identificador de recurso de la alerta |
| Description |Descripción de la alerta como se estableció al crearla |
| subscriptionId |Identificador de suscripción de Azure |
| timestamp |Cuándo generó el evento el servicio de Azure que procesó la solicitud |
| resourceId |Identificador de recurso del recurso afectado. |
| resourceGroupName |Nombre del grupo de recursos del recurso afectado |
| propiedades |Conjunto de pares `<Key, Value>` (es decir, `Dictionary<String, String>`) que incluye detalles sobre el evento. |
| event |Elemento que contiene metadatos sobre el evento |
| authorization |Propiedades RBAC del evento. Estas propiedades normalmente incluyen "action", "role" y "scope". |
| categoría |Categoría del evento. Los valores admitidos son: Administrative, Alert, Security, ServiceHealth y Recommendation. |
| caller |Dirección de correo electrónico del usuario que realizó la operación, la notificación de UPN o la notificación de SPN basada en la disponibilidad. Puede ser null para ciertas llamadas del sistema. |
| correlationId |Normalmente, un GUID en formato de cadena. Los eventos con correlationId pertenecen a la misma acción de mayor tamaño y suelen compartir un campo correlationId. |
| eventDescription |Descripción de texto estático del evento |
| eventDataId |Identificador único para el evento |
| eventSource |Nombre de la infraestructura o el servicio de Azure que generó el evento |
| httpRequest |La solicitud normalmente incluye "clientRequestId", "clientIpAddress" y "method" HTTP (por ejemplo, PUT). |
| level |Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" y "Verbose" |
| operationId |Por lo general, un GUID compartido entre los eventos correspondientes a una sola operación |
| operationName |Nombre de la operación. |
| propiedades |Propiedades del evento. |
| status |String. Estado de la operación. Entre los valores habituales, se incluyen "Started", "In Progress", "Succeeded", "Failed", "Active", "Resolved". |
| subStatus |Normalmente, incluye el código de estado HTTP de la llamada de REST correspondiente. También podría incluir otras cadenas que describen un subestado. Entre los valores de subestado habituales se incluyen OK (código de estado HTTP: 200), Created (código de estado HTTP: 201), Accepted (código de estado HTTP: 202), No Content (código de estado HTTP: 204), Bad Request (código de estado HTTP: 400), Not Found (código de estado HTTP: 404), Conflict (código de estado HTTP: 409), Internal Server Error (código de estado HTTP: 500), Service Unavailable (código de estado HTTP: 503) y Gateway Timeout (código de estado HTTP: 504) |

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre el registro de actividad](monitoring-overview-activity-logs.md)
* [Ejecute scripts de Azure Automation (Runbooks) en alertas de Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Use una aplicación lógica para enviar un SMS a través de Twilio desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta de registro de actividad.
* [Use una aplicación lógica para enviar un mensaje de Slack desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta de registro de actividad.
* [Use una aplicación lógica para enviar un mensaje a una cola de Azure desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta de registro de actividad.
