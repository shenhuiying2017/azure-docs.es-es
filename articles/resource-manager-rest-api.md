<properties
   pageTitle="API de REST de Resource Manager | Microsoft Azure"
   description="Información general sobre la autenticación de las API de REST de Resource Manager y ejemplos de uso"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# API de REST de Resource Manager

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [CLI de Azure](xplat-cli-azure-resource-manager.md)
- [Portal](./azure-portal/resource-group-portal.md)
- [API DE REST](resource-manager-rest-api.md)

Detrás de cada llamada a Azure Resource Manager, detrás de cada plantilla implementada, detrás de cada cuenta de almacenamiento configurada hay una o varias llamadas a la API de RESTful de Azure Resource Manager. En este tema se describen estas API y cómo se les puede llamar sin usar ningún SDK. Esto puede ser muy útil si quiere un control total de todas las solicitudes realizadas en Azure, o si el SDK para su idioma preferido no está disponible o no es compatible con las operaciones que desea realizar.

En este artículo no se describen todas las API que se muestran en Azure, sino que se utilizarán algunas como ejemplo de cómo seguir adelante y conectarse a ellas. Si comprende los conceptos básicos, podrá seguir adelante y leer el artículo [Azure Resource Manager REST API Reference](https://msdn.microsoft.com/library/azure/dn790568.aspx) (Referencia de la API de REST de Azure Resource Manager) para buscar información detallada sobre cómo usar el resto de las API.

## Autenticación
La autenticación para ARM se controla mediante Azure Active Directory (AD). Para conectarse a cualquier API, primero debe autenticarse con Azure AD para recibir un token de autenticación que pueda pasar a cada solicitud. Como describimos una llamada directamente a las API de REST, asumiremos también que no desea autenticar con un nombre de usuario y contraseña normales, donde se muestre un mensaje emergente que le solicita el nombre de usuario y contraseña, y quizá incluso otros mecanismos de autenticación utilizados en los escenarios de autenticación en dos fases. Por tanto, crearemos lo que se denomina una aplicación de Azure AD y una entidad de servicio que se usará para iniciar la sesión. Pero recuerde que Azure AD admite varios procedimientos de autenticación y todos ellos podrían usarse para recuperar el token de autenticación que se necesita para las solicitudes posteriores de API. Siga las instrucciones paso a paso que encontrará en [Uso del portal para crear una aplicación de Active Directory y una entidad de servicio con acceso a los recursos](./resource-group-create-service-principal-portal.md).

### Generación de un token de acceso 
La autenticación en Azure AD se realiza llamando a Azure AD, ubicado en login.microsoftonline.com. Para realizar la autenticación, se necesita la información siguiente:

* El identificador del inquilino de Azure AD (el nombre de Azure AD que está usando para iniciar sesión, que suele ser el mismo que el de su empresa, aunque no necesariamente)
* El identificador de la aplicación (tomado durante el paso de creación de la aplicación de Azure AD)
* Contraseña (que seleccionó al crear la aplicación de Azure AD)

En la siguiente solicitud HTTP, asegúrese de que reemplazar "Azure AD Tenant ID" (Id. del inquilino de Azure AD), "Application ID" (Id. de aplicación) y "Password" (Contraseña) por los valores correctos.

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
(El elemento access\_token de la respuesta anterior se ha abreviado para mejorar la legibilidad).

**Generación del token de acceso con Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Generación del token de acceso con PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

La respuesta contiene un token de acceso, información sobre por cuánto tiempo es válido el token e información acerca de para qué recurso se puede utilizar ese token. El token de acceso que ha recibido en la llamada HTTP anterior debe pasarse para todas las solicitudes a la API de ARM como encabezado llamado "Autorización" con el valor "Bearer YOUR\_ACCESS\_TOKEN". Observe el espacio entre "Bearer" y el token de acceso.

Como puede ver en el resultado HTTP anterior, el token es válido durante un período específico de tiempo durante el cual debe almacenar en caché y volver a usar ese mismo token. Aunque es posible autenticarse en Azure AD para cada llamada de API, sería muy ineficaz.

## Llamada a las API de REST de ARM

[Las API de REST de Azure Resource Manager se documentan aquí](https://msdn.microsoft.com/library/azure/dn790568.aspx), y está fuera del ámbito de este tutorial documentar el uso de todas ellas. Esta documentación solo usará algunas API para explicar el uso básico de las API, y después le remitiremos a la documentación oficial.

### Enumeración de todas las suscripciones

Una de las operaciones más sencillas que se pueden realizar es enumerar las suscripciones disponibles a las que se puede acceder. A continuación puede ver cómo se pasa el token de acceso como un encabezado.

(Reemplace YOUR\_ACCESS\_TOKEN por el token de acceso real).

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

Y como resultado, obtendrá una lista de suscripciones a las que puede acceder a esta entidad de servicio.

(Los identificadores de suscripción siguientes se han abreviado para mejorar la legibilidad).

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

### Enumeración de todos los grupos de recursos en una suscripción específica

Todos los recursos disponibles con las API de ARM se anidan dentro de un grupo de recursos. Vamos a consultar en ARM los grupos de recursos existentes en nuestra suscripción con la siguiente solicitud GET de HTTP. Observe cómo el identificador de suscripción se transmite como parte de la dirección URL de este momento.

(Reemplace YOUR\_ACCESS\_TOKEN y SUBSCRIPTION\_ID por su token de acceso e identificador de suscripción reales).

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

La respuesta que obtenga dependerá de si tiene definido algún grupo de recursos y si es así, cuántos.

(Los identificadores de suscripción siguientes se han abreviado para mejorar la legibilidad).

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

### Crear un grupo de recursos

Hasta ahora solo hemos consultado las API de ARM para obtener información; es el momento que crear algunos recursos y empecemos por el más sencillo de todos, un grupo de recursos. La solicitud HTTP siguiente crea un nuevo grupo de recursos en una región o ubicación de su elección y le agrega una o más etiquetas (el ejemplo siguiente en realidad solo agrega una etiqueta).

(Reemplace YOUR\_ACCESS\_TOKEN, SUBSCRIPTION\_ID, RESOURCE\_GROUP\_NAME por el token de acceso, identificador de suscripción y nombre del grupo de recursos reales que va a crear).

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

Si se realiza correctamente, obtendrá una respuesta similar a la siguiente

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

### Implementación de recursos en un grupo de recursos mediante una plantilla de ARM

Con ARM, puede implementar los recursos mediante plantillas de ARM. Una plantilla de ARM define varios recursos y sus dependencias. En esta sección vamos a suponer que está familiarizado con las plantillas de ARM y solo le mostraremos cómo realizar la llamada de API para iniciar la implementación de una de ellas. Aquí puede encontrar información detallada de las plantillas de ARM.

La implementación de una plantilla de ARM no difiere mucho de cómo llamar a otras API. Un aspecto importante es que la implementación de una plantilla puede tardar bastante tiempo, dependiendo de lo que esté dentro de la plantilla y de la llamada de API que se devolverá, y también dependerá de cómo el desarrollador consulte el estado de la implementación para averiguar cuándo se realiza la implementación.

En este ejemplo, vamos a usar una plantilla de ARM publicada y disponible en [GitHub](https://github.com/Azure/azure-quickstart-templates). La plantilla que se vamos a utilizar implementará una máquina virtual de Linux en la región oeste de EE. UU. Aunque esta plantilla estará disponible en un repositorio público, como GitHub, también puede seleccionar pasar la plantilla completa como parte de la solicitud. Tenga en cuenta que se proporcionamos valores de parámetro como parte de la solicitud que se utilizará en la plantilla utilizada.

(Reemplace SUBSCRIPTION\_ID, RESOURCE\_GROUP\_NAME, DEPLOYMENT\_NAME, YOUR\_ACCESS\_TOKEN, GLOBALY\_UNIQUE\_STORAGE\_ACCOUNT\_NAME, ADMIN\_USER\_NAME,ADMIN\_PASSWORD y DNS\_NAME\_FOR\_PUBLIC\_IP por los valores pertinentes para su solicitud).

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

La respuesta JSON a esta solicitud, que es bastante larga, se ha omitido para mejorar la legibilidad de esta documentación. La respuesta contendrá información sobre la implementación de la plantilla que acaba de crear.

<!---HONumber=AcomDC_0921_2016-->