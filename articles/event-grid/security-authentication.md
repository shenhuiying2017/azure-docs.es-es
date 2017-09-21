---
title: "Seguridad y autenticación de Azure Event Grid"
description: Describe Azure Event Grid y sus conceptos.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/14/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 9bc82e628df5e380db84e22e1f5fd25f75929fdc
ms.contentlocale: es-es
ms.lasthandoff: 09/13/2017

---

# <a name="event-grid-security-and-authentication"></a>Seguridad y autenticación de Event Grid 

Azure Event Grid tiene tres tipos de autenticación:

* Suscripciones a eventos
* Publicación de eventos
* Entrega de eventos de WebHook

## <a name="webhook-event-delivery"></a>Entrega de eventos de WebHook

Los webhooks son una de las muchas maneras de recibir eventos en tiempo real desde Azure Event Grid.

Cada vez que hay un nuevo evento preparado para entregarse, Event Grid envía una solicitud HTTP a su webhook con el evento en el cuerpo.

Al registrar su propio punto de conexión de WebHook con Event Grid, envía una solicitud POST con un código de validación simple con el fin de comprobar la propiedad de dicho punto de conexión. La aplicación necesita responder devolviendo el código de validación. Event Grid no ofrecerá eventos a los puntos de conexión de WebHook que no hayan superado la validación.
 
### <a name="validation-details"></a>Detalles de la validación:

* En el momento de crear o actualizar la suscripción de eventos, Event Grid publica un evento “SubscriptionValidationEvent” al punto de conexión de destino.
* El evento contiene un valor de encabezado “Event-Type: Validation”.
* El cuerpo del evento tiene el mismo esquema que otros eventos de Event Grid.
* Los datos del evento incluyen una propiedad "ValidationCode" con una cadena generada aleatoriamente, por ejemplo, “ValidationCode: acb13…”.

A continuación se muestra un evento SubscriptionValidationEvent de ejemplo.
```json
[{
  "Id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "Topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "Subject": "",
  "Data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "EventType": "Microsoft.EventGrid/SubscriptionValidationEvent",
  "EventTime": "2017-08-06T22:09:30.740323Z"
}]
```

Para comprobar la propiedad del punto de conexión, devuelva el código de validación, por ejemplo, "validation_response: acb13...", un ejemplo de lo que se muestra a continuación.

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Para comprobar la propiedad del punto de conexión, devuelva el código de validación, por ejemplo, "ValidationResponse: acb13…".

Por último, es importante tener en cuenta que Azure Event Grid solo admite puntos de conexión de webhook HTTPS.
## <a name="event-subscription"></a>Suscripción a eventos

Para suscribirse a un evento, debe tener el permiso **Microsoft.EventGrid/EventSubscriptions/Write** en el recurso requerido. Necesita este permiso porque está escribiendo una nueva suscripción en el ámbito del recurso. El recurso requerido difiere en función de si se suscribe a un tema del sistema o a un tema personalizado. Ambos tipos se describen en esta sección.

### <a name="system-topics-azure-service-publishers"></a>Temas del sistema (editores del servicio de Azure)

Para temas del sistema, necesita permiso para escribir una nueva suscripción a eventos en el ámbito del recurso que publica el evento. El formato del recurso es: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por ejemplo, para suscribirse a un evento en una cuenta de almacenamiento denominada **myacct**, necesita el permiso Microsoft.EventGrid/EventSubscriptions/Write en:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Temas personalizados

Para temas personalizados, necesita permiso para escribir una nueva suscripción a eventos en el ámbito del tema de Event Grid. El formato del recurso es: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por ejemplo, para suscribirse a un tema personalizado denominado **mytopic**, necesita el permiso Microsoft.EventGrid/EventSubscriptions/Write en: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="topic-publishing"></a>Publicación de temas

Los temas utilizan la Firma de acceso compartido (SAS) o la autenticación de clave. Se recomienda el uso de SAS, pero la autenticación de clave proporciona programación simple y es compatible con muchos editores de webhook existentes. 

Incluya el valor de autenticación en el encabezado HTTP. Para SAS, use **aeg-sas-token** para el valor del encabezado. Para autenticación de clave, utilice **aeg-sas-key** para el valor del encabezado.

### <a name="key-authentication"></a>Autenticación de clave

La autenticación de clave es la forma más sencilla de autenticación. Utilice el siguiente formato: `aeg-sas-key: <your key>`

Por ejemplo, pasa una clave con: 

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens de SAS

Los tokens de SAS para Event Grid incluyen el recurso, un tiempo de expiración y una firma. El formato del token de SAS es: `r={resource}&e={expiration}&s={signature}`.

El recurso es la ruta de acceso para el tema al que envía eventos. Por ejemplo, una ruta de acceso de recurso válida es: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Genere la firma a partir de una clave.

Por ejemplo, un valor **aeg-sas-token** válido es:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
``` 

En el ejemplo siguiente se crea un token de SAS para su uso con Event Grid:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    string encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString());

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Control de acceso de administración

Azure Event Grid permite controlar el nivel de acceso dado a distintos usuarios para realizar diversas operaciones de administración, como enumerar las suscripciones a eventos, crear otras nuevas y generar claves. Event Grid utiliza la comprobación de acceso basada en rol (RBAC) para esto.

### <a name="operation-types"></a>Tipos de operación

Azure Event Grid admite las siguientes acciones:

* Microsoft.EventGrid/*/read 
* Microsoft.EventGrid/*/write 
* Microsoft.EventGrid/*/delete 
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action 
* Microsoft.EventGrid/topics/listKeys/action 
* Microsoft.EventGrid/topics/regenerateKey/action

Las tres últimas operaciones devuelven información potencialmente confidencial que se filtra de las operaciones de lectura normales. Una práctica recomendada es restringir el acceso a estas operaciones. Se pueden crear roles personalizados con [Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md), la [interfaz de la línea de comandos (CLI) de Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md) y la [API de REST](../active-directory/role-based-access-control-manage-access-rest.md).

### <a name="enforcing-role-based-access-check-rbac"></a>Exigencia de la comprobación de acceso basada en roles (RBAC)

Utilice los pasos siguientes para exigir RBAC para los distintos usuarios:

#### <a name="create-a-custom-role-definition-file-json"></a>Creación de un archivo de definición de rol personalizado (.json)

Las siguientes son definiciones de roles de Event Grid de ejemplo que permiten a los usuarios realizar distintos conjuntos de acciones.

**EventGridReadOnlyRole.json**: Permitir únicamente operaciones de solo lectura.
```json
{ 
  "Name": "Event grid read only role", 
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856", 
  "IsCustom": true, 
  "Description": "Event grid read only role", 
  "Actions": [ 
    "Microsoft.EventGrid/*/read" 
  ], 
  "NotActions": [ 
  ], 
  "AssignableScopes": [ 
    "/subscriptions/<Subscription Id>" 
  ] 
}
```

**EventGridNoDeleteListKeysRole.json**: Permitir acciones de publicación restringidas pero denegar acciones de eliminación.
```json
{ 
  "Name": "Event grid No Delete Listkeys role", 
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174", 
  "IsCustom": true, 
  "Description": "Event grid No Delete Listkeys role", 
  "Actions": [     
    "Microsoft.EventGrid/*/write", 
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action" 
    "Microsoft.EventGrid/topics/listkeys/action", 
    "Microsoft.EventGrid/topics/regenerateKey/action" 
  ], 
  "NotActions": [ 
    "Microsoft.EventGrid/*/delete" 
  ], 
  "AssignableScopes": [ 
    "/subscriptions/<Subscription id>" 
  ] 
}
```

**EventGridContributorRole.json**: Permite todas las acciones de Event Grid.  
```json
{ 
  "Name": "Event grid contributor role", 
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514", 
  "IsCustom": true, 
  "Description": "Event grid contributor role", 
  "Actions": [ 
    "Microsoft.EventGrid/*/write", 
    "Microsoft.EventGrid/*/delete", 
    "Microsoft.EventGrid/topics/listkeys/action", 
    "Microsoft.EventGrid/topics/regenerateKey/action", 
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action" 
  ], 
  "NotActions": [], 
  "AssignableScopes": [ 
    "/subscriptions/d48566a8-2428-4a6c-8347-9675d09fb851" 
  ] 
} 
```

#### <a name="install-and-login-to-azure-cli"></a>Instalación de la CLI de Azure e inicio de sesión en ella

* CLI de Azure versión 0.8.8 o posterior. Para instalar la última versión y asociarla a la suscripción de Azure, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](../cli-install-nodejs.md).
* Azure Resource Manager en la CLI de Azure Para más información, consulte [Uso de la CLI de Azure con Resource Manager](../xplat-cli-azure-resource-manager.md).

#### <a name="create-a-custom-role"></a>Crear un rol personalizado

Para crear un rol personalizado, use:

    azure role create --inputfile <file path>

#### <a name="assign-the-role-to-a-user"></a>Asignación de un rol a un usuario


    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>


## <a name="next-steps"></a>Pasos siguientes

* Para obtener una introducción a Event Grid, vea [About Event Grid](overview.md) (Acerca de Event Grid).

