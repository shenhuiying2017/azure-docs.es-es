---
title: API de REST de Resource Manager | Microsoft Docs
description: "Información general sobre la autenticación de las API de REST de Resource Manager y ejemplos de uso"
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: e8d7a1d2-1e82-4212-8288-8697341408c5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: navale;tomfitz;
ms.openlocfilehash: 2f7ba23775545637de865f9ef63680ae22c62164
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="resource-manager-rest-apis"></a>API de REST de Resource Manager
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [CLI de Azure](xplat-cli-azure-resource-manager.md)
> * [Portal](resource-group-portal.md) 
> * [API DE REST](resource-manager-rest-api.md)
> 
> 

Detrás de cada llamada a Azure Resource Manager, detrás de cada plantilla implementada, detrás de cada cuenta de almacenamiento configurada hay una o varias llamadas a la API de RESTful de Azure Resource Manager. En este tema se describen estas API y cómo se les puede llamar sin usar ningún SDK. Este planteamiento puede resultar muy útil si quiere un control total de las solicitudes realizadas en Azure, o si el SDK para su idioma preferido no está disponible o no es compatible con las operaciones que necesita.

En este artículo no se describen todas las API que se muestran en Azure, sino que se utilizan algunas operaciones como ejemplo de cómo conectarse a ellas. Tras comprender los conceptos básicos, podrá leer el artículo [Azure Resource Manager REST API Reference](https://docs.microsoft.com/rest/api/resources/) (Referencia de la API de REST de Azure Resource Manager) para buscar información detallada sobre cómo usar el resto de las API.

## <a name="authentication"></a>Autenticación
La autenticación para Resource Manager se controla mediante Azure Active Directory (AD). Para conectarse a cualquier API, primero debe autenticarse con Azure AD para recibir un token de autenticación que pueda pasar a cada solicitud. Como se describe una llamada pura directamente a las API de REST, se asume que no desea autenticar por medio de una solicitud de nombre de usuario y contraseña. También se supone que no usa dos mecanismos de autenticación en dos fases. Por tanto, creamos lo que se denomina una aplicación de Azure AD y una entidad de servicio que se usan para iniciar la sesión. Pero recuerde que Azure AD admite varios procedimientos de autenticación y todos ellos podrían usarse para recuperar el token de autenticación que se necesita para las solicitudes posteriores de API.
Siga las instrucciones paso a paso disponibles en [Uso del portal para crear una aplicación de Active Directory y una entidad de servicio con acceso a los recursos](resource-group-create-service-principal-portal.md).

### <a name="generating-an-access-token"></a>Generación de un token de acceso
La autenticación en Azure AD se realiza llamando a Azure AD, ubicado en login.microsoftonline.com. Para realizar la autenticación, se necesita la información siguiente:

* El identificador del inquilino de Azure AD (el nombre de Azure AD que está usando para iniciar sesión, que suele ser el mismo que el de su empresa, aunque no necesariamente)
* El identificador de la aplicación (tomado durante el paso de creación de la aplicación de Azure AD)
* Contraseña (que seleccionó al crear la aplicación de Azure AD)

En la siguiente solicitud HTTP, asegúrese de reemplazar "Azure AD Tenant ID" (Id. del inquilino de Azure AD), "Application ID" (Id. de aplicación) y "Password" (Contraseña) por los valores correctos.

**Solicitud HTTP genérica:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... dará como resultado (si la autenticación se realiza correctamente) una respuesta similar a la siguiente:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(El elemento access_token de la respuesta anterior se ha abreviado para mejorar la legibilidad).

**Generación del token de acceso con Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net/&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Generación del token de acceso con PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

La respuesta contiene un token de acceso, información sobre por cuánto tiempo es válido el token e información acerca de para qué recurso se puede utilizar ese token.
El token de acceso que ha recibido en la llamada HTTP anterior se debe pasar para todas las solicitudes a la API de Resource Manager. Se pasa como un valor de encabezado denominado "Authorization" con el valor "Bearer YOUR_ACCESS_TOKEN". Observe el espacio entre "Bearer" y el token de acceso.

Como puede ver en el resultado HTTP anterior, el token es válido durante un período específico de tiempo durante el cual debe almacenar en caché y volver a usar ese mismo token. Aunque es posible autenticarse en Azure AD para cada llamada de API, sería muy ineficaz.

## <a name="calling-resource-manager-rest-apis"></a>Llamada a las API de REST de Resource Manager
Este tema usa solo unas pocas API para explicar el uso básico de las operaciones de REST. Para obtener información acerca de todas las operaciones, vea [API de REST de Resource Manager](https://docs.microsoft.com/rest/api/resources/).

### <a name="list-all-subscriptions"></a>Enumeración de todas las suscripciones
Una de las operaciones más sencillas que se pueden realizar es enumerar las suscripciones disponibles a las que se puede acceder. En la siguiente solicitud puede ver cómo se pasa el token de acceso como un encabezado:

(Reemplace YOUR_ACCESS_TOKEN por el token de acceso real).

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

... y como resultado, obtendrá una lista de suscripciones a las que puede acceder a esta entidad de servicio.

(Los identificadores de suscripción se han abreviado para mejorar la legibilidad).

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Enumeración de todos los grupos de recursos en una suscripción específica
Todos los recursos disponibles con las API de Resource Manager se anidan dentro de un grupo de recursos. Puede consultar a Resource Manager los grupos de recursos existentes en su suscripción con la siguiente solicitud HTTP GET. Observe cómo el identificador de suscripción se transmite como parte de la dirección URL de este momento.

(Reemplace YOUR_ACCESS_TOKEN y SUBSCRIPTION_ID por su token de acceso e identificador de suscripción reales).

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

La respuesta que obtenga dependerá de si tiene definido algún grupo de recursos y si es así, cuántos.

(Los identificadores de suscripción se han abreviado para mejorar la legibilidad).

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos
Hasta ahora, solo hemos consultado a las API de Resource Manager para obtener información. Es hora de crear algunos recursos; empecemos por el más sencillo de todos ellos: un grupo de recursos. La solicitud HTTP siguiente crea un grupo de recursos en una región o ubicación de su elección y le agrega una etiqueta.

(Reemplace YOUR_ACCESS_TOKEN, SUBSCRIPTION_ID, RESOURCE_GROUP_NAME por el token de acceso, identificador de suscripción y nombre del grupo de recursos reales que va a crear).

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Si se completa correctamente, obtendrá una respuesta similar a la siguiente:

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Ha creado correctamente un grupo de recursos en Azure. ¡Enhorabuena!

### <a name="deploy-resources-to-a-resource-group-using-a-resource-manager-template"></a>Implementación de recursos en un grupo de recursos mediante una plantilla de Resource Manager
Con Resource Manager, puede implementar los recursos mediante plantillas. Una plantilla define varios recursos y sus dependencias. En esta sección se supone que está familiarizado con las plantillas de Resource Manager y solo le mostramos cómo realizar la llamada de API para iniciar la implementación. Para obtener más información sobre la creación de plantillas, consulte [Authoring Azure Resource Manager templates](resource-group-authoring-templates.md) (Creación de plantillas de Azure Resource Manager).

La implementación de una plantilla no difiere mucho del modo de llamar a otras API. Un aspecto importante es que la implementación de una plantilla puede tardar bastante tiempo. La llamada de la API simplemente vuelve, y es el propio desarrollador quien tiene que consultar el estado de la implementación para averiguar cuándo se ha realizado. Para obtener más información, consulte [Seguimiento de las operaciones asincrónicas de Azure](resource-manager-async-operations.md).

En este ejemplo, se usa una plantilla publicada y disponible en [GitHub](https://github.com/Azure/azure-quickstart-templates). Dicha plantilla implementa una máquina virtual Linux en la región Oeste de EE. UU. Aunque en este ejemplo se utiliza una plantilla disponible en un repositorio público como GitHub, también puede pasar la plantilla completa como parte de la solicitud. Tenga en cuenta que se proporcionan valores de parámetro en la solicitud que se utilizan dentro de la plantilla implementada.

(Reemplace SUBSCRIPTION_ID, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME,ADMIN_PASSWORD y DNS_NAME_FOR_PUBLIC_IP por los valores pertinentes para su solicitud).

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

La larga respuesta JSON a esta solicitud se ha omitido para mejorar la legibilidad de esta documentación. La respuesta contiene información sobre la implementación de la plantilla que acaba de crear.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre el control de operaciones asincrónicas de REST, vea [Seguimiento de las operaciones asincrónicas de Azure](resource-manager-async-operations.md).
