---
title: Llamada a un webhook cuando se activan alertas del registro de actividades de Azure | Microsoft Docs
description: "Enrute los eventos de registro de actividades a otros servicios para acciones personalizadas. Por ejemplo, envíe SMS, registre errores o notifique a un equipo a través del servicio de chat o mensajería."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 08467aed4e1601b32598fc42515d9c38b601a9d4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="call-a-webhook-on-azure-activity-log-alerts"></a>Llamada a un webhook cuando se activan alertas del registro de actividades de Azure
Los webhooks permiten enrutar una notificación de alerta de Azure a otros sistemas para su procesamiento posterior o acciones personalizadas. Puede usar un webhook en una alerta para enrutarla a servicios que envían SMS, registran errores, notifican a un equipo mediante servicios de chat y mensajería o llevan a cabo otras acciones diversas. En este artículo se describe cómo establecer un webhook al que llamar cuando se activa una alerta del registro de actividades de Azure. También muestra el aspecto de la carga útil para HTTP POST a un webhook. Para información sobre la configuración y el esquema de una alerta de métrica de Azure, [consulte esta página en su lugar](insights-webhooks-alerts.md). También puede configurar una alerta de registro de actividad para enviar correo electrónico cuando se active.

> [!NOTE]
> Esta característica está actualmente en versión preliminar y se quitará en algún momento en el futuro.
>
>

Puede configurar una alerta de registro de actividad mediante los [cmdlets de Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), la [CLI multiplataforma](insights-cli-samples.md#work-with-alerts) o la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx). Actualmente, no se puede configurar una mediante Azure Portal.

## <a name="authenticating-the-webhook"></a>Autenticación del webhook
El webhook se puede autenticar con uno de estos métodos:

1. **Autorización basada en token**: el identificador URI de webhook se guarda con un identificador de token, por ejemplo, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Autorización básica**: el identificador URI de webhook se guarda con un nombre de usuario y una contraseña, por ejemplo, `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de carga
La operación POST contiene el siguiente esquema y carga útil de JSON para todas las alertas basadas en el registro de actividad. Este esquema es similar al usado por las alertas basadas en métrica.

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| status |Usado para alertas de métrica. Siempre se establece en "activated" para las alertas de registro de actividad. |
| contexto |Contexto del evento |
| activityLog | Las propiedades de registro del evento.|
| authorization |Propiedades RBAC del evento. Normalmente incluyen "action", "role" y "scope". |
| action | Acción capturada por la alerta. |
| ámbito | Ámbito de la alerta (p. ej., recurso).|
| canales nueva | Operación |
| claims | Una colección de información relacionada con las notificaciones. |
| caller |GUID o nombre de usuario que realizó la operación, la notificación de UPN o la notificación de SPN basada en la disponibilidad. Puede ser null para ciertas llamadas del sistema. |
| correlationId |Normalmente, un GUID en formato de cadena. Los eventos con correlationId pertenecen a la misma acción de mayor tamaño y suelen compartir un campo correlationId. |
| Descripción |Descripción de la alerta como se estableció al crearla |
| eventSource |Nombre de la infraestructura o el servicio de Azure que generó el evento |
| eventTimestamp |Hora en la que se produjo el evento. |
| eventDataId |Identificador único para el evento |
| level |Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" y "Verbose". |
| operationName |Nombre de la operación. |
| operationId |Por lo general, un GUID compartido entre los eventos correspondientes a una sola operación |
| ResourceId |Identificador de recurso del recurso afectado. |
| resourceGroupName |Nombre del grupo de recursos del recurso afectado |
| resourceProviderName |Proveedor de recursos del recurso afectado |
| status |String. Estado de la operación. Entre los valores habituales, se incluyen "Started", "In Progress", "Succeeded", "Failed", "Active", "Resolved". |
| subStatus |Normalmente, incluye el código de estado HTTP de la llamada de REST correspondiente. También podría incluir otras cadenas que describen un subestado. Entre los valores de subestado habituales se incluyen OK (código de estado HTTP: 200), Created (código de estado HTTP: 201), Accepted (código de estado HTTP: 202), No Content (código de estado HTTP: 204), Bad Request (código de estado HTTP: 400), Not Found (código de estado HTTP: 404), Conflict (código de estado HTTP: 409), Internal Server Error (código de estado HTTP: 500), Service Unavailable (código de estado HTTP: 503) y Gateway Timeout (código de estado HTTP: 504) |
| subscriptionId |Identificador de suscripción de Azure |
| submissionTimestamp |Cuándo generó el evento el servicio de Azure que procesó la solicitud |
| resourceType | El tipo de recurso que generó el evento.|
| propiedades |Conjunto de pares `<Key, Value>` (es decir, `Dictionary<String, String>`) que incluye detalles sobre el evento |

## <a name="next-steps"></a>pasos siguientes
* [Más información sobre el registro de actividad](monitoring-overview-activity-logs.md)
* [Ejecute scripts de Azure Automation (Runbooks) en alertas de Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Use una aplicación lógica para enviar un SMS a través de Twilio desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta de registro de actividad.
* [Use una aplicación lógica para enviar un mensaje de Slack desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta de registro de actividad.
* [Use una aplicación lógica para enviar un mensaje a una cola de Azure desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este ejemplo es para alertas de métrica, pero podría modificarse para funcionar con una alerta de registro de actividad.
