---
title: "Azure Active Directory B2C: protección de los servicios REST mediante la autenticación HTTP básica"
description: "Proteja los intercambios de notificaciones de la API de REST personalizada en Azure AD B2C mediante la autenticación HTTP básica"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 0d4594f5e7c0a13d50993dd42d4780c1ba703140
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Protección de los servicios REST mediante la autenticación HTTP básica
En un [artículo de Azure AD B2C relacionado](active-directory-b2c-custom-rest-api-netfw.md), se crea un servicio REST (API web) que se integra en los recorridos del usuario de Azure Active Directory B2C (Azure AD B2C) sin autenticación. 

En dicho artículo se agrega una autenticación HTTP básica al servicio REST, con el fin de que solo los usuarios comprobados, lo que incluye B2C, puedan acceder a la API. Con la autenticación HTTP básica, se establecen las credenciales de usuario (identificador de aplicación y secreto de aplicación) en la directiva personalizada. 

Para más información, consulte [Basic authentication in ASP.NET web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication) (Autenticación básica en ASP.NET Web API).

## <a name="prerequisites"></a>requisitos previos
Complete los pasos del artículo [Azure Active Directory B2C: integración de intercambios de notificaciones de API de REST en el recorrido del usuario de Azure AD B2C como validación en entradas de usuario](active-directory-b2c-custom-rest-api-netfw.md).

## <a name="step-1-add-authentication-support"></a>Paso 1: Adición de compatibilidad con la autenticación

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Paso 1.1 Adición de la configuración de la aplicación al archivo web.config del proyecto
1. Abra el proyecto de Visual Studio que ha creado. 

2. Agregue la siguiente configuración de la aplicación al archivo web.config en el elemento `appSettings`:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Cree una contraseña y establezca el valor de `WebApp:ClientSecret`.

    Para generar una contraseña compleja, ejecute el siguiente código de PowerShell. Puede usar cualquier valor arbitrario.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Paso 1.2: Instalación de las bibliotecas de OWIN
Para comenzar, agregue los paquetes de NuGet de middleware OWIN al proyecto mediante la Consola del Administrador de paquetes de Visual Studio:

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Paso 1.3: Adición de una clase de middleware de autenticación
Agregue la clase `ClientAuthMiddleware.cs` a la carpeta *App_Start*. Para ello:

1. Haga clic con el botón derecho en la carpeta *App_Start*, seleccione **Agregar** y, después, seleccione **Clase**.

   ![Agregar la clase ClientAuthMiddleware.cs a la carpeta App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. En el cuadro **Nombre**, escriba **ClientAuthMiddleware.cs**.

   ![Creación de nueva clase de C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Abra el archivo *App_Start\ClientAuthMiddleware.cs* y reemplace su contenido por el código siguiente:

    ```csharp
    
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
Agregue una clase de inicio de OWIN denominada `Startup.cs` a la API. Para ello:
1. Haga clic con el botón derecho en el proyecto, seleccione **Agregar** > **Nuevo elemento** y luego busque **OWIN**.

   ![Agregar una clase de inicio de OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Abra el archivo *Startup.cs* y reemplace su contenido por el código siguiente:

    ```csharp
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

### <a name="step-15-protect-the-identity-api-class"></a>Paso 1.5: Protección de la clase de API de identidad
Abra Controllers\IdentityController.cs y agregue la etiqueta `[Authorize]` a la clase del controlador. Esta etiqueta solo permite acceder al controlador a aquellos usuarios que cumplan el requisito de autorización.

![Agregar la etiqueta Authorize al controlador](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Paso 2: Publicación en Azure
Para publicar el proyecto, en el Explorador de soluciones, haga clic con el botón derecho en el proyecto **Contoso.AADB2C.API** y seleccione **Publicar**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Paso 3: Adición del identificador y del secreto de la aplicación de servicios REST a Azure AD B2C
Tras proteger el servicio REST con el identificador (nombre de usuario) y el secreto del cliente, debe almacenar las credenciales en su inquilino de Azure AD B2C. Su directiva personalizada proporciona las credenciales al invocar los servicios REST. 

### <a name="step-31-add-a-restful-services-client-id"></a>Paso 3.1: Adición de un identificador de cliente de servicios REST
1. En su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.


2. Seleccione **Policy Keys** (Claves de directiva) para ver las claves disponibles en el inquilino.

3. Seleccione **Agregar**.

4. En **Opciones**, seleccione **Manual**.

5. En **Nombre**, escriba **B2cRestClientId**.  
    Es posible que se agregue automáticamente el prefijo *B2C_1A_*.

6. En el cuadro **Secreto**, escriba el identificador de aplicación que ha definido anteriormente.

7. En **Uso de la clave**, seleccione **Secreto**.

8. Seleccione **Crear**.

9. Confirme que ha creado la clave `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-a-restful-services-client-secret"></a>Paso 3.2: Adición del secreto de cliente de servicios REST
1. En su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.

2. Seleccione **Policy Keys** (Claves de directiva) para ver las claves disponibles en el inquilino.

3. Seleccione **Agregar**.

4. En **Opciones**, seleccione **Manual**.

5. En **Nombre**, escriba **B2cRestClientSecret**.  
    Es posible que se agregue automáticamente el prefijo *B2C_1A_*.

6. En el cuadro **Secreto**, escriba el secreto de aplicación que ha definido anteriormente.

7. En **Uso de la clave**, seleccione **Secreto**.

8. Seleccione **Crear**.

9. Confirme que ha creado la clave `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Paso 4: Cambio del perfil técnico para que admita la autenticación básica en su directiva de extensión
1. Abra el archivo de la directiva de extensión (TrustFrameworkExtensions.xml) en el directorio de trabajo.

2. Busque el nodo `<TechnicalProfile>` que incluya `Id="REST-API-SignUp"`.

3. Busque el elemento `<Metadata>`.

4. Cambie el valor de *AuthenticationType* a *Basic*, como se muestra a continuación:
    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Agregue el siguiente fragmento de código XML inmediatamente después del elemento `<Metadata>` de cierre: 

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
    Tras agregar el fragmento de código, su perfil técnico debe ser similar al código XML siguiente:
    
    ![Adición de elementos XML de la autenticación básica](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Paso 5: Carga de la directiva en el inquilino

1. En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra **Azure AD B2C**.

2. Seleccione **Marco de experiencia de identidad**.

3. Abra **Todas las directivas**.

4. Seleccione **Cargar directiva**.

5. Active la casilla **Sobrescribir la directiva si existe**.

6. Cargue el archivo *TrustFrameworkExtensions.xml* y asegúrese de que pasa la validación.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Paso 6: Prueba de la directiva personalizada con Ejecutar ahora
1. Abra **Configuración de Azure AD B2C** y seleccione **Marco de experiencia de identidad**.

    >[!NOTE]
    >La función Ejecutar ahora requiere al menos que una aplicación esté registrada previamente en el inquilino. Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.

2. Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado, y seleccione **Ejecutar ahora**.

3. Pruebe el proceso escribiendo **Test** en el cuadro **Nombre propio**.  
    Azure AD B2C muestra un mensaje de error en la parte superior de la ventana.

    ![Prueba de la API de identidad](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. En el campo **Nombre propio**, escriba un nombre (que no sea "Test").  
    Azure AD B2C registra al usuario y envía un número de fidelidad a la aplicación. Observe el número de este ejemplo:

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Descargue el código y los archivos de la directiva completos
* Una vez completado el tutorial [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md), le recomendamos que compile su escenario mediante sus archivos de directiva personalizados. Hemos proporcionado [archivos de directiva de ejemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic) para que los tenga como referencia.
* El código completo se puede descargar en [Ejemplo de solución de Visual Studio para referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

## <a name="next-steps"></a>pasos siguientes
* [Uso de certificados de cliente para proteger la API de REST](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

