<properties
	pageTitle="Azure Insights: Uso de registros de auditoría para enviar notificaciones de alerta por correo electrónico y webhook en Azure Insights | Microsoft Azure"
	description="Consulte cómo usar las entradas de registro de auditoría de servicio para llamar a direcciones URL web o enviar notificaciones de correo electrónico en Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Uso de registros de auditoría para enviar notificaciones de alerta por correo electrónico y webhook en Azure Insights

En este artículo se muestra el esquema de carga cuando un evento de registro de auditoría desencadena un webhook y se describe cómo puede enviar mensajes de correo electrónico con el mismo evento.

>[AZURE.NOTE] Esta funcionalidad actualmente está en vista previa. Durante los próximos meses, se mejorará la infraestructura y el rendimiento de Alerta sobre eventos. En esta vista previa, solo se puede acceder a esta característica mediante Azure PowerShell y la CLI. Más adelante estará disponible el acceso a esta característica mediante el Portal de Azure.

## Webhooks
Los webhooks permiten enrutar las notificaciones de alerta de Azure a otros sistemas para su procesamiento posterior o notificaciones personalizadas. Un ejemplo de esto puede ser el enrutamiento de la alerta a los servicios que pueden controlar una solicitud web entrante para enviar SMS, registrar errores o notificar a una persona mediante servicios de chat y mensajería. El identificador URI de webhook debe ser un punto de conexión HTTP o HTTPS válido.

## Email
Se pueden enviar mensajes de correo electrónico a cualquier dirección de correo electrónico válida. También se enviará una notificación a los administradores y los coadministradores de la suscripción en la que se ejecuta la regla.

### Ejemplo de regla de correo electrónico
Debe configurar una regla de correo electrónico y una regla de webhook y después indicar a las reglas que se deben iniciar cuando se produzca el evento de registro de auditoría. Puede ver un ejemplo de uso de PowerShell en [Azure Insights PowerShell quickstart samples (Ejemplos de inicio rápido de PowerShell de Azure Insights)](insights-powershell-samples.md#alert-on-audit-log-event).


## Autenticación
Hay dos formas de identificador URI de autenticación:

1. Autenticación basada en token, para lo que se guarda el identificador URI de webhook con un identificador de token como parámetro de consulta. Por ejemplo, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Autenticación básica mediante un identificador de usuario y una contraseña. Por ejemplo, https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## Esquema de carga de webhook de notificación de eventos de registro de auditoría
Cuando hay disponible un nuevo evento, la alerta de eventos de registro de auditoría ejecuta el webhook configurado con metadatos de eventos en la carga de webhook. En el ejemplo siguiente se muestra el esquema de carga de webhook:

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

|Nombre del elemento|	Descripción|
|---|---|
|status |Establecido siempre en "activado"|
|contexto|Contexto del evento|
|resourceProviderName|Proveedor de recursos del recurso afectado|
|conditionType |"Evento"|
|name |Nombre de la regla de alerta|
|id |Identificador de recurso de la alerta|
|description|	Descripción establecida en la alerta por el autor de la alerta|
|subscriptionId |GUID de suscripción de Azure|
|timestamp|	Marca de tiempo cuando el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento|
|resourceId |Identificador URI de identificador de recurso que identifica el recurso de forma única|
|resourceGroupName|Resource-group-name del recurso afectado|
|propiedades |Conjunto de pares <Key  Value> (es decir, el diccionario<String  String>) que incluye detalles sobre el evento|
|event|Elemento que contiene metadatos sobre el evento|
|authorization|Captura las propiedades RBAC del evento. Normalmente incluyen "action", "role" y "scope".|
|categoría | Categoría del evento. Los valores admitidos son Administrative, Alert, Security, ServiceHealth y Recommendation|
|caller|Dirección de correo electrónico del usuario que ha realizado la operación, la notificación de UPN o la notificación de SPN basada en la disponibilidad. Puede ser null para ciertas llamadas del sistema.|
|correlationId|	Normalmente, un GUID en formato de cadena. Los eventos con correlationId pertenecen a una misma acción de mayor tamaño y suelen compartir el mismo correlationId.|
|eventDescription |Texto estático que describe un evento|
|eventDataId|Identificador único de un evento|
|eventSource |Nombre del servicio o la infraestructura de Azure que generó el evento|
|httpRequest|	Normalmente incluye "clientRequestId", "clientIpAddress" y "method" (el método HTTP, por ejemplo, PUT).|
|level|Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" y "Verbose"|
|operationId|Suele ser un GUID compartido entre los eventos correspondientes a una sola operación|
|operationName|Nombre de la operación|
|propiedades |El elemento dentro del elemento de evento contiene las propiedades del evento.|
|status|Cadena que describe el estado de la operación. Entre los valores habituales se incluyen Started, In Progress, Succeeded, Failed, Active y Resolved|
|subStatus|	Normalmente, incluye el código de estado HTTP de la llamada de REST correspondiente. También podría incluir otras cadenas que describen un subestado. Entre los valores de subestado comunes se incluyen OK (código de estado HTTP: 200), Created (código de estado HTTP: 201), Accepted (código de estado HTTP: 202), No Content (código de estado HTTP: 204), Bad Request (código de estado HTTP: 400), Not Found (código de estado HTTP: 404), Conflict (código de estado HTTP: 409), Internal Server Error (código de estado HTTP: 500), Service Unavailable (código de estado HTTP: 503) y Gateway Timeout (código de estado HTTP: 504)|

<!---HONumber=AcomDC_0330_2016-->