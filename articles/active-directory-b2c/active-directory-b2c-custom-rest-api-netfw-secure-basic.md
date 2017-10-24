---
title: "Azure Active Directory B2C: Protección de los servicios REST con la autenticación básica HTTP"
description: "Ejemplo de cómo proteger los intercambios de notificaciones de la API de REST personalizada en Azure AD B2C mediante la autenticación básica HTTP"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 226ecbe7e4d9d95fd4ba3255c95ec6baa1a86576
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-http-basic-authentication"></a>Azure Active Directory B2C: Protección de los servicios REST con la autenticación básica HTTP
En un [artículo relacionado](active-directory-b2c-custom-rest-api-netfw.md), hemos creado un servicio REST (API Web) que se integra en recorridos de usuario de Azure AD B2C sin autenticación. Este artículo muestra cómo agregar la autenticación básica HTTP al servicio REST, por lo que solo los usuarios comprobados, incluidos B2C, pueden tener acceso a la API. Con la autenticación básica HTTP, se establecen las credenciales de usuario (identificador de aplicación y secreto de aplicación) en la directiva personalizada para usar las credenciales. 

> [!NOTE]
>
>El artículo siguiente describe la [Autenticación básica en una API Web de ASP.NET](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/basic-authentication) con más detalle.

## <a name="prerequisites"></a>Requisitos previos
Complete los pasos previos del artículo [Integración de intercambios de notificaciones de API de REST en los recorridos de usuario de Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md)

## <a name="step-1-add-authentication-support"></a>Paso 1: Adición de compatibilidad con la autenticación

### <a name="step-11-add-application-settings-to-projects-webconfig-file"></a>Paso 1.1 Adición de la configuración de la aplicación al archivo web.config del proyecto
1. Abra el proyecto de visual studio que creó anteriormente 
2. Agregue la siguiente configuración de la aplicación al archivo web.config en el elemento `appSettings`:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Cree una contraseña y establezca el valor de `WebApp:ClientSecret`

    Para generar una contraseña compleja, puede ejecutar el siguiente PowerShell. Puede usar cualquier valor arbitrario.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Paso 1.2: Instalación de las bibliotecas OWIN
Para comenzar, agregue los paquetes de NuGet de middleware OWIN al proyecto mediante la Consola del Administrador de paquetes de Visual Studio:

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-authentication-middleware-class"></a>Paso 1.3: Adición de la clase de middleware de autenticación
Agregue la clase `ClientAuthMiddleware.cs` en la carpeta `App_Start`. Haga clic con el botón derecho en la carpeta `App_Start`, seleccione **Agregar** y, a continuación, seleccione **clase**

![Adición de la clase ClientAuthMiddleware.cs en la carpeta App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

Escriba `ClientAuthMiddleware.cs` como nombre de la clase

![Creación de nueva clase de C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

Abra el archivo `App_Start\ClientAuthMiddleware.cs` y reemplace el contenido del archivo por el código siguiente:


```C#

using Microsoft.Owin;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using System.Security.Principal;
using System.Text;
using System.Threading.Tasks;
using System.Web;

namespace Contoso.AADB2C.API
{
    /// <summary>
    /// Class to create a custom owin middleware to check for client authentication
    /// </summary>
    public class ClientAuthMiddleware
    {
        private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
        private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];

        /// <summary>
        /// Gets or sets the next owin middleware
        /// </summary>
        private Func<IDictionary<string, object>, Task> Next { get; set; }

        /// <summary>
        /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
        /// </summary>
        /// <param name="next"></param>
        public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
        {
            this.Next = next;
        }

        /// <summary>
        /// Invoke client authentication middleware during each request.
        /// </summary>
        /// <param name="environment">Owin environment</param>
        /// <returns></returns>
        public Task Invoke(IDictionary<string, object> environment)
        {
            // Get wrapper class for the environment
            var context = new OwinContext(environment);

            // Check whether the authorization header is available. This contains the credentials.
            var authzValue = context.Request.Headers.Get("Authorization");
            if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
            {
                // Process next middleware
                return Next(environment);
            }

            // Get credentials
            var creds = authzValue.Substring("Basic ".Length).Trim();
            string clientId;
            string clientSecret;

            if (RetrieveCreds(creds, out clientId, out clientSecret))
            {
                // Set transaction authenticated as client
                context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
            }

            return Next(environment);
        }

        /// <summary>
        /// Retrieve credentials from header
        /// </summary>
        /// <param name="credentials">Authorization header</param>
        /// <param name="clientId">Client identifier</param>
        /// <param name="clientSecret">Client secret</param>
        /// <returns>True if valid credentials were presented</returns>
        private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
        {
            string pair;
            clientId = clientSecret = string.Empty;

            try
            {
                pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
            }
            catch (FormatException)
            {
                return false;
            }
            catch (ArgumentException)
            {
                return false;
            }

            var ix = pair.IndexOf(':');
            if (ix == -1)
            {
                return false;
            }

            clientId = pair.Substring(0, ix);
            clientSecret = pair.Substring(ix + 1);

            // Return whether credentials are valid
            return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
        }
    }
}
```

### <a name="step-14-add-an-owin-startup-class"></a>Paso 1.4: Adición de una clase de inicio de OWIN
Agregue una clase de inicio OWIN a la API llamada `Startup.cs`. Haga clic con el botón derecho en el proyecto, seleccione **Agregar** y **Nuevo elemento**; después, busque OWIN.

![Agregar una clase de inicio de OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

Abra el archivo `Startup.cs` y reemplace el contenido del archivo por el código siguiente:

```C#
using Microsoft.Owin;
using Owin;

[assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
namespace Contoso.AADB2C.API
{
    public class Startup
    {
        public void Configuration(IAppBuilder app)
        {
                app.Use<ClientAuthMiddleware>();
        }
    }
}
```

### <a name="step-15-protect-identity-api-class"></a>Paso 1.5: Protección de la clase de API Identity
Abra Controllers\IdentityController.cs y agregue la etiqueta `[Authorize]` a la clase del controlador. La etiqueta `[Authorize]` restringe el acceso al controlador para aquellos usuarios que cumplen el requisito de autorización.

![Adición de la etiqueta [Authorize] al controlador](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Paso 2: Publicación en Azure
Para publicar el proyecto, en el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Contoso.AADB2C.API** y seleccione **Publicar**.

## <a name="step-3-add-the-restful-services-app-id--app-secret-to-azure-ad-b2c"></a>Paso 3: Adición del identificador de la aplicación de servicios REST y del secreto de aplicación a Azure AD B2C
Después de proteger su servicio REST con el identificador de cliente (nombre de usuario) y el secreto de cliente, debe almacenar las credenciales en el inquilino de Azure AD B2C. La directiva personalizada proporciona las credenciales al invocar los servicios REST.  

### <a name="step-31-add-restful-services-client-id"></a>Paso 3.1: Adición del identificador de cliente de servicios REST
1.  Vaya a su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.
2.  Seleccione **Policy Keys** (Claves de directiva) para ver las claves disponibles en el inquilino.
3.  Haga clic en **+Agregar**.
4.  En **Opciones**, use **Manual**.
5.  En **Nombre**, use `B2cRestClientId`.  
    Es posible que se agregue automáticamente el prefijo `B2C_1A_`.
6.  En el cuadro de texto **Secreto**, escriba el identificador de aplicación definido anteriormente
7.  En **Uso de claves**, use **Secreto**.
8.  Haga clic en **Crear**
9.  Confirme que ha creado la clave `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-restful-services-client-secret"></a>Paso 3.2: Adición del secreto de cliente de servicios REST
1.  Vaya a su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.
2.  Seleccione **Policy Keys** (Claves de directiva) para ver las claves disponibles en el inquilino.
3.  Haga clic en **+Agregar**.
4.  En **Opciones**, use **Manual**.
5.  En **Nombre**, use `B2cRestClientSecret`.  
    Es posible que se agregue automáticamente el prefijo `B2C_1A_`.
6.  En el cuadro de texto **Secreto**, escriba el secreto de aplicación definido anteriormente
7.  En **Uso de claves**, use **Secreto**.
8.  Haga clic en **Crear**
9.  Confirme que ha creado la clave `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technicalprofile-to-support-basic-authentication-in-your-extension-policy"></a>Paso 4: Cambio de `TechnicalProfile` para que admita la autenticación básica en la directiva de extensión
1.  Abra el archivo de directiva de extensión (TrustFrameworkExtensions.xml) desde el directorio de trabajo.
2.  Busque el nodo `<TechnicalProfile>` que se incluye con `Id="REST-API-SignUp"`.
3.  Localice el elemento `<Metadata>`.
4.  Cambie `AuthenticationType` a `Basic`.
```xml
<Item Key="AuthenticationType">Basic</Item>
```
5.  Agregue el siguiente fragmento de código XML inmediatamente después del cierre del elemento `<Metadata>`:  

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
Después de agregar los fragmentos de código XML, `TechnicalProfile` debería ser similar a:

![Adición de elementos XML de la autenticación básica](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Paso 5: Carga de la directiva en el inquilino

1.  En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y haga clic en **Azure AD B2C**.
2.  Seleccione **Marco de experiencia de identidad**.
3.  Haga clic en **Todas las directivas**.
4.  Seleccione **Cargar directiva**
5.  Active la casilla **Sobrescribir la directiva si existe**.
6.  **Cargue** TrustFrameworkExtensions.xml y asegúrese de que no se produce ningún error en la validación.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Paso 6: Prueba de la directiva personalizada con Ejecutar ahora
1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.

    >[!NOTE]
    >
    >    **Ejecutar ahora** Requiere al menos que una aplicación esté registrada previamente en el inquilino. 
    >    Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.

2.  Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.
3.  Intente escribir "Test" en el campo **Nombre propio**, B2C muestra un mensaje de error en la parte superior de la página.

    ![Prueba de la API de identidad](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  Intente escribir un nombre (distinto de "Test") en el campo **Nombre propio**. B2C registra al usuario y envía loyaltyNumber a la aplicación. Observe el número de este JWT en este ejemplo.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="next-steps"></a>Pasos siguientes
* [Uso de certificados de cliente para proteger la API de REST](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Opcional] Descarga de los archivos y código de la directiva completos
* Se recomienda que, en lugar de usar estos archivos de ejemplo, cree su escenario con sus propios archivos de directiva personalizada tras completar el tutorial de introducción a las directivas personalizadas.  [Archivos de directiva de ejemplo de referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)
* Puede descargar aquí el código completo [Ejemplo de solución de Visual Studio para referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)