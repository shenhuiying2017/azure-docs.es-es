---
title: Configuración de webhooks en alertas de registro de actividad de Azure | Microsoft Docs
description: 'Consulte cómo usar alertas de registro de actividad para llamar a webhooks. '
author: kamathashwin
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: ashwink

---
# Configuración de un webhook en una alerta de registro de actividad de Azure
Los webhooks permiten enrutar una notificación de alerta de Azure a otros sistemas para su procesamiento posterior o acciones personalizadas. Puede usar un webhook en una alerta para enrutarla a servicios que envían SMS, registran errores, notifican a un equipo mediante servicios de chat y mensajería o llevan a cabo otras acciones diversas. En este artículo, se describe cómo establecer un webhook en una alerta de registro de actividad de Azure y cuál es el aspecto de la carga útil de la operación HTTP POST a un webhook. Para información sobre la configuración y el esquema de una alerta de métrica de Azure, [consulte esta página en su lugar](../monitoring-and-diagnostics/insights-webhooks-alerts.md). También puede configurar una alerta de registro de actividad para enviar correo electrónico cuando se active.

> [!NOTE]
> Esta característica está actualmente en versión preliminar, por lo que el rendimiento y la calidad son variables.
> 
> 

Puede configurar una alerta de registro de actividad mediante los [cmdlets de Azure PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md#create-alert-rules), la [CLI multiplataforma](../monitoring-and-diagnostics/insights-cli-samples.md#work-with-alerts) o la [API de REST Insights](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## Autenticación del webhook
El webhook se puede autenticar con uno de estos métodos:

1. **Autorización basada en token**: el URI de webhook se guarda con un identificador de token, por ejemplo, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Autorización básica**: el URI de webhook se guarda con un nombre de usuario y una contraseña, por ejemplo, `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## Esquema de carga
La operación POST contiene el siguiente esquema y carga útil de JSON para todas las alertas basadas en el registro de actividad. Este esquema es similar al usado por las alertas basadas en métrica.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

| Nombre del elemento | Description |
| --- | --- |
| status |Usado para alertas de métrica. Siempre se establece en "activated" para las alertas de registro de actividad. |
| contexto |Contexto del evento |
| resourceProviderName |Proveedor de recursos del recurso afectado |
| conditionType |Siempre "Event" |
| name |Nombre de la regla de alerta. |
| id |Identificador de recurso de la alerta |
| description |Descripción de la alerta como se estableció al crearla |
| subscriptionId |Identificador de suscripción de Azure |
| timestamp |Cuándo generó el evento el servicio de Azure que procesó la solicitud |
| resourceId |Identificador de recurso del recurso afectado. |
| resourceGroupName |Nombre del grupo de recursos del recurso afectado |
| propiedades |Conjunto de pares `<Key, Value>` (es decir, `Dictionary<String, String>`) que incluye detalles sobre el evento |
| event |Elemento que contiene metadatos sobre el evento |
| authorization |Propiedades RBAC del evento. Normalmente incluyen "action", "role" y "scope". |
| categoría |Categoría del evento. Los valores admitidos son: Administrative, Alert, Security, ServiceHealth y Recommendation. |
| caller |Dirección de correo electrónico del usuario que realizó la operación, la notificación de UPN o la notificación de SPN basada en la disponibilidad. Puede ser null para ciertas llamadas del sistema. |
| correlationId |Normalmente, un GUID en formato de cadena. Los eventos con correlationId pertenecen a la misma acción de mayor tamaño y suelen compartir un campo correlationId. |
| eventDescription |Descripción de texto estático del evento |
| eventDataId |Identificador único para el evento |
| eventSource |Nombre de la infraestructura o el servicio de Azure que generó el evento |
| httpRequest |Normalmente incluye "clientRequestId", "clientIpAddress" y "method" (el método HTTP, por ejemplo, PUT). |
| level |Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" y "Verbose" |
| operationId |Por lo general, un GUID compartido entre los eventos correspondientes a una sola operación |
| operationName |Nombre de la operación. |
| propiedades |Propiedades del evento. |
| status |String. Estado de la operación. Entre los valores habituales, se incluyen "Started", "In Progress", "Succeeded", "Failed", "Active", "Resolved". |
| subStatus |Normalmente, incluye el código de estado HTTP de la llamada de REST correspondiente. También podría incluir otras cadenas que describen un subestado. Entre los valores de subestado habituales se incluyen OK (código de estado HTTP: 200), Created (código de estado HTTP: 201), Accepted (código de estado HTTP: 202), No Content (código de estado HTTP: 204), Bad Request (código de estado HTTP: 400), Not Found (código de estado HTTP: 404), Conflict (código de estado HTTP: 409), Internal Server Error (código de estado HTTP: 500), Service Unavailable (código de estado HTTP: 503) y Gateway Timeout (código de estado HTTP: 504) |

## Pasos siguientes
* [Aprenda más sobre el registro de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)
* [Ejecute scripts de Azure Automation (Runbooks) en alertas de Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Use una aplicación lógica para enviar un SMS a través de Twilio desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta de registro de actividad.
* [Use una aplicación lógica para enviar un mensaje de Slack desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta de registro de actividad.
* [Use una aplicación lógica para enviar un mensaje a una cola de Azure desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta de registro de actividad.

<!---HONumber=AcomDC_0921_2016-->