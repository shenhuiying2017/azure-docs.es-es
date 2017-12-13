---
title: Identidad de servicio administrada en App Service y Azure Functions | Microsoft Docs
description: "Guía de configuración y referencia conceptual para la compatibilidad de la identidad de servicio administrada en Azure App Service y Azure Functions"
services: app-service
author: mattchenderson
manager: cfowler
editor: 
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/13/2017
ms.author: mahender
ms.openlocfilehash: 6b2dcaa4b0e0f59bf8a632b48813ba6a24202ec5
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="how-to-use-azure-managed-service-identity-public-preview-in-app-service-and-azure-functions"></a>Uso de la identidad del servicio administrada (versión preliminar pública) en App Service y Azure Functions

> [!NOTE] 
> La identidad de servicio administrada para App Service y Azure Functions se encuentra actualmente en versión preliminar.

En este tema se muestra cómo crear una identidad de aplicación administrada para las aplicaciones de App Service y Azure Functions y cómo usarla para tener acceso a otros recursos. Una identidad de servicio administrada de Azure Active Directory permite a la aplicación tener acceso fácilmente a otros recursos protegidos de AAD, como Azure Key Vault. La identidad está administrada por la plataforma Azure y no requiere que aprovisione o rote los secretos. Para obtener más información acerca de la identidad de servicio administrada, vea [Managed Service Identity (MSI) for Azure resources ](../active-directory/msi-overview.md) (Identidad de servicio administrada para recursos de Azure).

## <a name="creating-an-app-with-an-identity"></a>Creación de una aplicación con una identidad

Para crear una aplicación con una identidad se requiere la configuración de una propiedad adicional en la aplicación.

> [!NOTE] 
> Solo la ranura principal de un sitio recibirá la identidad. Las identidades de servicio administradas para ranuras de implementación aún no se admiten.


### <a name="using-the-azure-portal"></a>Uso de Azure Portal

Para configurar una identidad de servicio administrada en el portal, primero tendrá que crear una aplicación como lo hace normalmente y, a continuación, habilitar la característica.

1. Cree una aplicación en el portal como lo haría normalmente. Navegue hasta el portal.

2. Si utiliza una aplicación de función, vaya a **Características de la plataforma**. Para otros tipos de aplicación, desplácese hacia abajo hasta el grupo **Configuración** en el panel de navegación izquierdo.

3. Seleccione **Identidad de servicio administrada**.

4. Cambie **Registrar en Azure Active Directory** a **Activado**. Haga clic en **Guardar**.

![Identidad de servicio administrada en App Service](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

Para configurar una identidad de servicio administrada mediante la CLI de Azure, debe usar el comando `az webapp assign-identity` en una aplicación existente. Tiene tres opciones para ejecutar los ejemplos de esta sección:

- Usar [Azure Cloud Shell](../cloud-shell/overview.md) desde Azure Portal.
- Use Azure Cloud Shell integrado mediante el botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
- [Instale la versión más reciente de la CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.21 o posterior), si prefiere usar una consola de la CLI local. 

Los siguientes pasos le guiarán por la creación de una aplicación web y la asignación a la misma de una identidad mediante la CLI:

1. Si usa la CLI de Azure en una consola local, lo primero que debe hacer es iniciar sesión en Azure mediante el [inicio de sesión de az](/cli/azure/#login). Use una cuenta asociada a la suscripción de Azure en la que desea implementar la aplicación:

    ```azurecli-interactive
    az login
    ```
2. Cree una aplicación web mediante la CLI. Para más ejemplos de cómo utilizar la CLI con App Service, consulte los [ejemplos de la CLI de App Service](../app-service/app-service-cli-samples.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myplan --resource-group myResourceGroup --sku S1
    az webapp create --name myapp --resource-group myResourceGroup --plan myplan
    ```

3. Ejecute el comando `assign-identity` para crear la identidad de esta aplicación:

    ```azurecli-interactive
    az webapp assign-identity --name myApp --resource-group myResourceGroup
    ```

### <a name="using-an-azure-resource-manager-template"></a>Uso de una plantilla de Azure Resource Manager

Se puede utilizar una plantilla de Azure Resource Manager para automatizar la implementación de los recursos de Azure. Para obtener más información acerca de la implementación en App Service y Functions, vea [Automating resource deployment in App Service](../app-service/app-service-deploy-complex-application-predictably.md) (Automatización de la implementación de recursos en App Service) y [Automatización de la implementación de recursos para una aplicación de función en Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Se puede crear cualquier recurso de tipo `Microsoft.Web/sites` con una identidad mediante la inclusión de la siguiente propiedad en la definición de recursos:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

Esto indica a Azure que debe crear y administrar la identidad para la aplicación.

Por ejemplo, una aplicación web podría tener el aspecto siguiente:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Cuando se crea el sitio, tiene las siguientes propiedades adicionales:
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Donde `<TENANTID>` y `<PRINCIPALID>` se reemplazan por GUID. La propiedad tenantId identifica a qué inquilino AAD pertenece la aplicación. El valor principalId es un identificador único para la nueva identidad de la aplicación. En AAD, la aplicación tiene el mismo nombre que asignó a la instancia de App Service o Azure Functions.

## <a name="obtaining-tokens-for-azure-resources"></a>Obtención de tokens para recursos de Azure

Una aplicación puede utilizar su identidad para obtener tokens para otros recursos protegidos por AAD, como Azure Key Vault. Estos tokens representan a la aplicación que accede al recurso, y no a un usuario específico de la aplicación. 

> [!IMPORTANT]
> Es posible que tenga que configurar el recurso de destino para permitir el acceso desde la aplicación. Por ejemplo, si se solicita un token a Key Vault, debe asegurarse de que ha agregado una directiva de acceso que incluya la identidad de la aplicación. De lo contrario, las llamadas a Key Vault se rechazarán, incluso si incluyen el token. Para obtener más información acerca de los recursos que admiten tokens de la identidad de servicio administrada, consulte [Azure services that support Azure AD authentication](../active-directory/msi-overview.md#which-azure-services-support-managed-service-identity) (Servicios de Azure que admiten la autenticación de Azure AD).

Hay un protocolo de REST sencillo para obtener un token en App Service y Azure Functions. Para las aplicaciones de .NET, la biblioteca Microsoft.Azure.Services.AppAuthentication proporciona una abstracción sobre este protocolo y admite una experiencia de desarrollo local.

### <a name="asal"></a>Uso de la biblioteca Microsoft.Azure.Services.AppAuthentication para .NET

En el caso de las aplicaciones y las funciones de .NET, la manera más sencilla de trabajar con una identidad de servicio administrada es utilizar el paquete Microsoft.Azure.Services.AppAuthentication. Esta biblioteca también le permitirá probar el código localmente en la máquina de desarrollo, con su cuenta de usuario de Visual Studio, la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) o la autenticación integrada de Active Directory. Para obtener más información sobre las opciones de desarrollo local con esta biblioteca, consulte la [referencia de Microsoft.Azure.Services.AppAuthentication]. En esta sección se muestra cómo empezar a usar la biblioteca en su código.

1. Agregue referencias a los paquetes de NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) y [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) para la aplicación.

2.  Agregue el siguiente código a su aplicación:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Para obtener más información sobre Microsoft.Azure.Services.AppAuthentication y las operaciones que expone, consulte la [referencia de Microsoft.Azure.Services.AppAuthentication] y el [ejemplo de .NET sobre App Service y KeyVault con MSI](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Uso del protocolo de REST

Una aplicación con una identidad de servicio administrada tiene dos variables de entorno definidas:
- MSI_ENDPOINT
- MSI_SECRET

La variable **MSI_ENDPOINT** es una dirección URL local desde la que la aplicación puede solicitar tokens. Para obtener un token para un recurso, realice una solicitud HTTP GET para este punto de conexión, incluyendo los parámetros siguientes:

> [!div class="mx-tdBreakAll"]
> |Nombre de parámetro|En el|Descripción|
> |-----|-----|-----|
> |resource|Consultar|El URI del recurso del recurso AAD para el que se debe obtener un token.|
> |api-version|Consultar|La versión de la API de token que se usará. Actualmente, la única versión admitida es "2017-09-01".|
> |secret|Encabezado|El valor de la variable de entorno MSI_SECRET.|


Una respuesta 200 OK incluye un cuerpo JSON con las siguientes propiedades:

> [!div class="mx-tdBreakAll"]
> |Nombre de propiedad|Descripción|
> |-------------|----------|
> |access_token|El token de acceso solicitado. El servicio web de llamada puede usar este token para autenticarse en el servicio web de recepción.|
> |expires_on|La hora a la que expira el token de acceso. La fecha se representa como el número de segundos desde 1970-01-01T0:0:0Z UTC hasta la fecha de expiración. Este valor se utiliza para determinar la duración de los tokens almacenados en caché.|
> |resource|El URI del identificador de la aplicación del servicio web de recepción.|
> |token_type|Indica el valor de tipo de token. El único tipo que admite Azure AD es portador. Para obtener más información sobre los tokens de portador, consulte [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)[Marco de autorización de OAuth2.0: uso del token de portador (RFC 6750)].|


Esta respuesta es la misma que la [respuesta para la solicitud de token de acceso de servicio a servicio de AAD](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#service-to-service-access-token-response).

> [!NOTE] 
> Las variables de entorno se configuran cuando el proceso se inicia por primera vez, por lo que después de habilitar la funcionalidad Managed Service Identity de la aplicación puede que necesite reiniciar la aplicación, o implementar de nuevo el código, antes de que `MSI_ENDPOINT` y `MSI_SECRET` estén disponibles en el código.

### <a name="rest-protocol-examples"></a>Ejemplos de protocolo de REST
Una solicitud de ejemplo puede tener el siguiente aspecto:
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
Y una respuesta de ejemplo puede tener el siguiente aspecto:
```
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Ejemplos de código
Para realizar esta solicitud en C#:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> Para los lenguajes. NET, también puede usar [Microsoft.Azure.Services.AppAuthentication](#asal) en lugar de crear esta solicitud personalmente.

En Node.js:
```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

En PowerShell:
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```


[referencia de Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
