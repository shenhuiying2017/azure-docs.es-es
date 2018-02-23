---
title: Llamada a un webhook cuando se activa una alerta del registro de actividad de Azure | Microsoft Docs
description: "Aprenda a enrutar los eventos de registro de actividad a otros servicios para acciones personalizadas. Por ejemplo, puede enviar mensajes SMS, registrar errores o notificar a un equipo a través del servicio de chat o mensajería."
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
ms.openlocfilehash: 9872c30d123f0a7443e28dc58ee0d4e16572a390
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="call-a-webhook-on-an-azure-activity-log-alert"></a>Llamada a un webhook cuando se activa una alerta del registro de actividad de Azure
Puede usar webhooks para enrutar una notificación de alerta de Azure a otros sistemas para postprocesamiento o para acciones personalizadas. Puede usar un webhook en una alerta para redirigirla a servicios que envían mensajes SMS, para registrar errores, para notificar a un equipo mediante servicios de chat y mensajería o llevar a cabo otras acciones. También puede configurar una alerta de registro de actividad para enviar correo electrónico cuando se activa una alerta.

En este artículo se describe cómo establecer un webhook al que llamar cuando se activa una alerta de registro de actividad de Azure. También muestra el aspecto de la carga útil para HTTP POST a un webhook. Para obtener información sobre la configuración y el esquema de una alerta de métrica, vea [Configuración de un webhook en una alerta de métrica de Azure](insights-webhooks-alerts.md). 

> [!NOTE]
> Actualmente, la característica que admite llamadas a un webhook en una alerta de registro de actividad de Azure está en versión preliminar.
>
>

Puede configurar una alerta de registro de actividad mediante los [cmdlets de Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), una [CLI multiplataforma](insights-cli-samples.md#work-with-alerts) o las [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx). Actualmente, no se puede usar Azure Portal para configurar una alerta de registro de actividad.

## <a name="authenticate-the-webhook"></a>Autenticación del webhook
El webhook se puede autenticar con uno de estos métodos:

* **Autenticación basada en tokens**. El URI del webhook se guarda con un identificador de token. Por ejemplo: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
* **Autorización básica**. El URI del webhook se guarda con un nombre de usuario y una contraseña. Por ejemplo: `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de carga
La operación POST contiene el siguiente esquema con carga útil de JSON para todas las alertas basadas en el registro de actividad. Este esquema es similar al usado por las alertas basadas en métricas.

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
| status |Usado para alertas de métrica. Siempre se establece en activado para las alertas de registro de actividad.|
| contexto |Contexto del evento. |
| activityLog | Las propiedades de registro del evento.|
| authorization |Propiedades de control de acceso basado en rol (RBAC) del evento. Estas propiedades suelen incluir **action**, **role** y **scope**. |
| action | Acción capturada por la alerta. |
| ámbito | Ámbito de la alerta (es decir, el recurso).|
| canales nueva | Operación. |
| claims | Colección de información relacionada con las notificaciones. |
| caller |GUID o nombre de usuario del usuario que ha realizado la operación, la notificación de UPN o la notificación de SPN basada en la disponibilidad. Puede ser NULL para ciertas llamadas del sistema. |
| correlationId |Normalmente, un GUID en formato de cadena. Los eventos con **correlationId** pertenecen a la misma acción de mayor tamaño. Suelen tener el mismo valor de **correlationId**. |
| Descripción |Descripción de la alerta que se definió al crear la alerta. |
| eventSource |Nombre de la infraestructura o el servicio de Azure que generó el evento. |
| eventTimestamp |Hora en que se produjo el evento. |
| eventDataId |Identificador único del evento. |
| level |Uno de los siguientes valores: Critical, Error, Warning, Informational o Verbose. |
| operationName |Nombre de la operación. |
| operationId |Generalmente, un GUID que se comparte entre los eventos. El GUID suele corresponder a una sola operación. |
| ResourceId |Identificador de recurso del recurso afectado. |
| resourceGroupName |Nombre del grupo de recursos del recurso afectado. |
| resourceProviderName |Proveedor de recursos del recurso afectado. |
| status |Valor de cadena que indica el estado de la operación. Entre los valores habituales, se incluyen Started, In Progress, Succeeded, Failed, Active y Resolved. |
| subStatus |Normalmente, incluye el código de estado HTTP de la llamada de REST correspondiente. También podría incluir otras cadenas que describen un subestado. Entre los valores de subestado habituales se incluyen OK (código de estado HTTP: 200), Created (código de estado HTTP: 201), Accepted (código de estado HTTP: 202), No Content (código de estado HTTP: 204), Bad Request (código de estado HTTP: 400), Not Found (código de estado HTTP: 404), Conflict (código de estado HTTP: 409), Internal Server Error (código de estado HTTP: 500), Service Unavailable (código de estado HTTP: 503) y Gateway Timeout (código de estado HTTP: 504). |
| subscriptionId |Identificador de la suscripción de Azure. |
| submissionTimestamp |Momento en que el servicio de Azure que procesó la solicitud ha generado el evento. |
| resourceType | El tipo de recurso que generó el evento.|
| propiedades |Conjunto de pares clave/valor que incluye detalles sobre el evento. Por ejemplo, `Dictionary<String, String>`. |

## <a name="next-steps"></a>pasos siguientes
* Obtenga más información sobre el [registro de actividad](monitoring-overview-activity-logs.md).
* Aprenda a [ejecutar scripts de Azure Automation (runbooks) en alertas de Azure](http://go.microsoft.com/fwlink/?LinkId=627081).
* Aprenda a [usar una aplicación lógica para enviar un mensaje SMS a través de Twilio desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este ejemplo corresponde a alertas de métrica, pero puede modificarse para que funcione con una alerta de registro de actividad.
* Aprenda a [usar una aplicación lógica para enviar un mensaje de Slack desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este ejemplo corresponde a alertas de métrica, pero puede modificarse para que funcione con una alerta de registro de actividad.
* Aprenda a [usar una aplicación lógica para enviar un mensaje a una cola de Azure desde una alerta de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este ejemplo corresponde a alertas de métrica, pero puede modificarse para que funcione con una alerta de registro de actividad.
