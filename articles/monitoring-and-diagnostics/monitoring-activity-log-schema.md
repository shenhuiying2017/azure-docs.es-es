---
title: Esquema de eventos del registro de actividad de Azure | Microsoft Docs
description: Consulte el esquema de eventos de los datos que se emiten en el registro de actividad
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: johnkem
ms.openlocfilehash: 91129da9ef7791a506292d9e13e386a25ee341a8
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2017
---
# <a name="azure-activity-log-event-schema"></a>Esquema de eventos del registro de actividad de Azure
El **registro de actividad de Azure** es un registro que proporciona información de los eventos de nivel de suscripción que se han producido en Azure. En este artículo se describe el esquema de eventos por categoría de datos.

## <a name="administrative"></a>Administrativo
Esta categoría contiene el registro de todas las operaciones de creación, actualización, eliminación y acción realizadas a través de Resource Manager. Los ejemplos de los tipos de eventos que aparecen en esta categoría incluyen "crear máquina virtual" y "eliminar grupo de seguridad de red". Cada acción realizada por un usuario o una aplicación mediante Resource Manager se modela como una operación en un tipo de recurso determinado. Si el tipo de operación es Write, Delete o Action, los registros de inicio y corrección o error de esa operación se registran en la categoría Administrativo. La categoría Administrativo también incluye los cambios realizados en el control de acceso basado en roles de una suscripción.

### <a name="sample-event"></a>Evento de ejemplo
```json
{
  "authorization": {
    "action": "microsoft.support/supporttickets/write",
    "role": "Subscription Admin",
    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
  },
  "caller": "admin@contoso.com",
  "channels": "Operation",
  "claims": {
    "aud": "https://management.core.windows.net/",
    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
    "iat": "1421876371",
    "nbf": "1421876371",
    "exp": "1421880271",
    "ver": "1.0",
    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
    "puid": "20030000801A118C",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
    "name": "John Smith",
    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
    "appidacr": "2",
    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
    "http://schemas.microsoft.com/claims/authnclassreference": "1"
  },
  "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "description": "",
  "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
  "eventName": {
    "value": "EndRequest",
    "localizedValue": "End request"
  },
  "httpRequest": {
    "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
    "clientIpAddress": "192.168.35.115",
    "method": "PUT"
  },
  "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
  "level": "Informational",
  "resourceGroupName": "MSSupportGroup",
  "resourceProviderName": {
    "value": "microsoft.support",
    "localizedValue": "microsoft.support"
  },
  "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
  "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "operationName": {
    "value": "microsoft.support/supporttickets/write",
    "localizedValue": "microsoft.support/supporttickets/write"
  },
  "properties": {
    "statusCode": "Created"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": "Created",
    "localizedValue": "Created (HTTP Status Code: 201)"
  },
  "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
  "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
  "subscriptionId": "s1"
}
```

### <a name="property-descriptions"></a>Descripciones de propiedades
| Nombre del elemento | Description |
| --- | --- |
| authorization |Blob de propiedades RBAC del evento. Normalmente incluye las propiedades "action", "role" y "scope". |
| caller |Dirección de correo electrónico del usuario que ha realizado la operación, la notificación de UPN o la notificación de SPN basada en la disponibilidad. |
| canales nueva |Uno de los siguientes valores: "Admin", "Operation" |
| claims |Token de JWT que se usa en Active Directory para autenticar el usuario o la aplicación para llevar a cabo esta operación en Resource Manager. |
| correlationId |Normalmente, un GUID en formato de cadena. Los eventos que comparten correlationId pertenecen a la misma acción general. |
| Description |Descripción de texto estático de un evento. |
| eventDataId |Identificador único de un evento. |
| httpRequest |Blob que describe la solicitud HTTP. Normalmente incluye "clientRequestId", "clientIpAddress" y "method" (método HTTP). Por ejemplo, PUT). |
| level |Nivel del evento. Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" y "Verbose" |
| resourceGroupName |Nombre del grupo de recursos del recurso afectado. |
| resourceProviderName |Nombre del proveedor de recursos del recurso afectado. |
| resourceId |Identificador de recurso del recurso afectado. |
| operationId |GUID compartido entre los eventos correspondientes a una sola operación. |
| operationName |Nombre de la operación. |
| propiedades |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describen los detalles del evento. |
| status |Cadena que describe el estado de la operación. Algunos valores habituales son: Started, In Progress, Succeeded, Failed, Active y Resolved. |
| subStatus |Por lo general, el código de estado HTTP de la llamada de REST correspondiente, pero también puede incluir otras cadenas que describen un subestado, como estos valores habituales: OK (código de estado HTTP: 200), Created (código de estado HTTP: 201), Accepted (código de estado HTTP: 202), No Content (código de estado HTTP: 204), Bad Request (código de estado HTTP: 400), Not Found (código de estado HTTP: 404), Conflict (código de estado HTTP: 409), Internal Server Error (código de estado HTTP: 500), Service Unavailable (código de estado HTTP: 503) y Gateway Timeout (código de estado HTTP: 504). |
| eventTimestamp |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| submissionTimestamp |Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId |Identificador de la suscripción de Azure. |

## <a name="service-health"></a>Estado del servicio
Esta categoría contiene el registro de los incidentes de estado del servicio que se han producido en Azure. Un ejemplo del tipo de evento que aparece en esta categoría es "SQL Azure en el este de EE. UU. está experimentando un tiempo de inactividad". Los eventos de estado del servicio son de cinco variedades: Acción requerida, Recuperación asistida, Incidente, Mantenimiento, Información o Seguridad, y solo aparecen si tiene un recurso en la suscripción que se vaya a ver afectado por el evento.

### <a name="sample-event"></a>Evento de ejemplo
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```

### <a name="property-descriptions"></a>Descripciones de propiedades
Nombre del elemento | Descripción
-------- | -----------
canales nueva | Es uno de los siguientes valores: "Admin", "Operation"
correlationId | Normalmente es un GUID en formato de cadena. Los eventos que pertenecen a la misma acción general suelen compartir el mismo valor de correlationId.
Descripción | Descripción del evento.
eventDataId | Identificador único de un evento.
eventName | Título del evento.
level | Nivel del evento. Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" y "Verbose"
resourceProviderName | Nombre del proveedor de recursos del recurso afectado. Si es desconocido, será null.
resourceType| Tipo del recurso afectado. Si es desconocido, será null.
subStatus | Normalmente es null para los eventos del estado del servicio.
eventTimestamp | Marca de tiempo de cuándo se generó el registro de eventos y se envió al registro de actividad.
submissionTimestamp |   Marca de tiempo de cuándo el evento empezó a estar disponible en el registro de actividad.
subscriptionId | Suscripción de Azure en la que se registró este evento.
status | Cadena que describe el estado de la operación. Algunos valores comunes: Activo y Resuelto.
operationName | Nombre de la operación. Normalmente es Microsoft.ServiceHealth/incident/action.
categoría | "ServiceHealth"
resourceId | Identificador del recurso afectado (si es conocido). En caso contrario, se proporciona el identificador de suscripción.
Properties.title | El título localizado de esta comunicación. El idioma predeterminado es inglés.
Properties.communication | Los detalles localizados de la comunicación con el formato HTML. El valor predeterminado es inglés.
Properties.incidentType | Valores posibles: AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security
Properties.trackingId | Identifica el incidente al que está asociado este evento. Se utiliza para correlacionar los eventos relacionados con un incidente.
Properties.impactedServices | Un blob JSON con escape que describe los servicios y regiones a los que afecta el incidente. Una lista de servicios, cada uno de los cuales tiene un valor ServiceName y una lista de ImpactedRegions, cada una de los cuales tiene un valor RegionName.
Properties.defaultLanguageTitle | La comunicación en inglés
Properties.defaultLanguageContent | La comunicación en inglés en formato html o como texto sin formato
Properties.stage | Valores posibles de AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security: Active y Resolved. En el caso de Maintenance son: Active, Planned, InProgress, Canceled, Rescheduled, Resolved y Complete
Properties.communicationId | La comunicación de este evento está asociada.

## <a name="alert"></a>Alerta
Esta categoría contiene el registro de todas las activaciones de alertas de Azure. Un ejemplo del tipo de evento que aparece en esta categoría es "El % de CPU en myVM ha estado por encima de 80 durante los últimos 5 minutos". Varios sistemas de Azure tienen un concepto de alerta: puede definir una regla de algún tipo y recibir una notificación cuando las condiciones coincidan con esa regla. Cada vez que un tipo de alerta de Azure compatible "se activa" o se cumplen las condiciones para generar una notificación, también se inserta un registro de la activación en esta categoría del Registro de actividad.

### <a name="sample-event"></a>Evento de ejemplo

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>Descripciones de propiedades
| Nombre del elemento | Descripción |
| --- | --- |
| caller | Siempre es Microsoft.Insights/alertRules. |
| canales nueva | Siempre es "Admin, Operation". |
| claims | Blob JSON con el SPN (nombre de entidad de seguridad de servicio), o tipo de recurso, del motor de alertas. |
| correlationId | GUID en formato de cadena. |
| Descripción |Descripción de texto estático del evento de alerta. |
| eventDataId |Identificador único del evento de alerta. |
| level |Nivel del evento. Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" y "Verbose" |
| resourceGroupName |Nombre del grupo de recursos del recurso afectado si se trata de una alerta de métrica. Para otros tipos de alerta, es el nombre del grupo de recursos que contiene la propia alerta. |
| resourceProviderName |Nombre del proveedor de recursos del recurso afectado si se trata de una alerta de métrica. Para otros tipos de alerta, es el nombre del proveedor de recursos de la propia alerta. |
| resourceId | Nombre del identificador del recurso afectado si se trata de una alerta de métrica. Para otros tipos de alerta, es el identificador del propio recurso de alerta. |
| operationId |GUID compartido entre los eventos correspondientes a una sola operación. |
| operationName |Nombre de la operación. |
| propiedades |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describen los detalles del evento. |
| status |Cadena que describe el estado de la operación. Algunos valores habituales son: Started, In Progress, Succeeded, Failed, Active y Resolved. |
| subStatus | Normalmente es null para las alertas. |
| eventTimestamp |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| submissionTimestamp |Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId |Identificador de la suscripción de Azure. |

### <a name="properties-field-per-alert-type"></a>Campo Propiedades por tipo de alerta
El campo Propiedades contendrá valores diferentes dependiendo del origen del evento de alerta. Dos proveedores de eventos de alerta comunes son las alertas de registro de actividad y las alertas de métrica.

#### <a name="properties-for-activity-log-alerts"></a>Propiedades de las alertas del registro de actividad
| Nombre del elemento | Descripción |
| --- | --- |
| properties.subscriptionId | Identificador de suscripción del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.eventDataId | Identificador de datos del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.resourceGroup | Grupo de recursos del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.resourceId | Identificador de recurso del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.eventTimestamp | Marca de tiempo del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.operationName | Nombre de la operación del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.status | Estado del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad.|

#### <a name="properties-for-metric-alerts"></a>Propiedades de las alertas de métrica
| Nombre del elemento | Descripción |
| --- | --- |
| properties.RuleUri | Identificador de recurso de la propia regla de alerta de métrica. |
| properties.RuleName | Nombre de la regla de alertas de métrica. |
| properties.RuleDescription | Descripción de la regla de alertas de métrica (tal y como se define en la regla de alertas). |
| properties.Threshold | Valor de umbral que se usa en la evaluación de la regla de alertas de métrica. |
| properties.WindowSizeInMinutes | Tamaño de la ventana que se usa en la evaluación de la regla de alertas de métrica. |
| properties.Aggregation | Tipo de agregación definido en la regla de alertas de métrica. |
| properties.Operator | Operador condicional usado en la evaluación de la regla de alertas de métrica. |
| properties.MetricName | Nombre de la métrica usada en la evaluación de la regla de alertas de métrica. |
| properties.MetricUnit | Unidad de la métrica usada en la evaluación de la regla de alertas de métrica. |

## <a name="autoscale"></a>Autoscale
Esta categoría contiene el registro de los eventos relacionados con el funcionamiento del motor de escalado automático en función de cualquier configuración de escalado automático que haya definido en la suscripción. Un ejemplo del tipo de evento que aparece en esta categoría es "Error de acción de escalado automático". Con el escalado automático puede escalar horizontalmente o reducir horizontalmente de forma automática el número de instancias de un tipo de recurso compatible en función de la hora del día o cargar datos (métricas) mediante una configuración de escalado automático. Cuando se cumplen las condiciones para escalar o reducir verticalmente, se registran los eventos de inicio y corrección o error en esta categoría.

### <a name="sample-event"></a>Evento de ejemplo
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>Descripciones de propiedades
| Nombre del elemento | Descripción |
| --- | --- |
| caller | Siempre es Microsoft.Insights/autoscaleSettings. |
| canales nueva | Siempre es "Admin, Operation". |
| claims | Blob JSON con el SPN (nombre de entidad de seguridad de servicio), o tipo de recurso, del motor de escalado automático. |
| correlationId | GUID en formato de cadena. |
| Descripción |Descripción de texto estático del evento de escalado automático. |
| eventDataId |Identificador único del evento de escalado automático. |
| level |Nivel del evento. Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" y "Verbose" |
| resourceGroupName |Nombre del grupo de recursos para la configuración del escalado automático. |
| resourceProviderName |Nombre del proveedor de recursos para la configuración del escalado automático. |
| resourceId |Identificador de recurso de la configuración del escalado automático. |
| operationId |GUID compartido entre los eventos correspondientes a una sola operación. |
| operationName |Nombre de la operación. |
| propiedades |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describen los detalles del evento. |
| properties.Description | Descripción detallada de lo que hacía el motor de escalado automático. |
| properties.ResourceName | Identificador del recurso afectado (recurso en el que se estaba llevando a cabo la acción de escalado) |
| properties.OldInstancesCount | Número de instancias antes de que tuviera efecto la acción de escalado automático. |
| properties.NewInstancesCount | Número de instancias después de que tuviera efecto la acción de escalado automático. |
| properties.LastScaleActionTime | Marca de tiempo de cuándo se produjo la acción de escalado automático. |
| status |Cadena que describe el estado de la operación. Algunos valores habituales son: Started, In Progress, Succeeded, Failed, Active y Resolved. |
| subStatus | Normalmente es null para el escalado automático. |
| eventTimestamp |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| submissionTimestamp |Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId |Identificador de la suscripción de Azure. |

## <a name="security"></a>Seguridad
Esta categoría contiene el registro de todas las alertas generado por Azure Security Center. Un ejemplo del tipo de evento que vería en esta categoría es "Se ejecutó un archivo de extensión doble sospechoso".

### <a name="sample-event"></a>Evento de ejemplo
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/d4742bb8-c279-4903-9653-9858b17d0c2e/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/d4742bb8-c279-4903-9653-9858b17d0c2e/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "d4742bb8-c279-4903-9653-9858b17d0c2e",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Descripciones de propiedades
| Nombre del elemento | Descripción |
| --- | --- |
| canales nueva | Siempre "Operation" |
| correlationId | GUID en formato de cadena. |
| description |Descripción de texto estático del evento de seguridad. |
| eventDataId |Identificador único del evento de seguridad. |
| eventName |Nombre descriptivo del evento de seguridad. |
| id |Identificador único del recurso del evento de seguridad. |
| level |Nivel del evento. Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" o "Verbose" |
| resourceGroupName |Nombre del grupo de recursos del recurso. |
| resourceProviderName |Nombre del proveedor de recursos de Azure Security Center. Siempre "Microsoft.Security". |
| resourceType |Tipo de recurso que generó el evento de seguridad, por ejemplo, "Microsoft.Security/locations/alerts" |
| resourceId |Id. de recurso de la alerta de seguridad. |
| operationId |GUID compartido entre los eventos correspondientes a una sola operación. |
| operationName |Nombre de la operación. |
| propiedades |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describen los detalles del evento. Estas propiedades variarán según el tipo de alerta de seguridad. Vea [esta página](../security-center/security-center-alerts-type.md) para obtener una descripción de los tipos de alertas que proceden de Security Center. |
| properties.Severity |Nivel de gravedad. Los valores posibles son "High," "Medium" o "Low". |
| status |Cadena que describe el estado de la operación. Algunos valores habituales son: Started, In Progress, Succeeded, Failed, Active y Resolved. |
| subStatus | Normalmente es null para los eventos de seguridad. |
| eventTimestamp |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| submissionTimestamp |Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId |Identificador de la suscripción de Azure. |

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre el registro de actividad (antes, Registros de auditoría)](monitoring-overview-activity-logs.md)
* [Transmisión del registro de actividad de Azure a centros de eventos](monitoring-stream-activity-logs-event-hubs.md)
