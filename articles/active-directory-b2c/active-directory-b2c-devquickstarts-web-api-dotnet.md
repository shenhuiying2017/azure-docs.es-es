---
title: Azure Active Directory B2C | Microsoft Docs
description: Creación de una aplicación web que llama a una API web mediante Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: msmbaldwin
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/22/2016
ms.author: dastrock

---
# Azure AD B2C: llamada a una API web desde una aplicación web de .NET
Con Azure Active Directory (Azure AD) B2C, puede agregar eficaces características de administración de identidades autoservicio tanto a sus aplicaciones web como a las API web en pocos pasos. En este artículo se describe cómo crear una aplicación web de "lista de tareas pendientes" de controlador de vista de modelos (MV) .NET que llama a una API web mediante tokens de portador.

Este artículo no trata de la implementación de la administración de registros, inicios de sesión y perfiles con Azure AD B2C. Se centra en la llamada a las API web después de que el usuario ya está autenticado. Si aún no lo ha hecho, debe comenzar por el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para conocer los conceptos básicos de Azure AD B2C.

## Obtener un directorio de Azure AD B2C
Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor de todos los usuarios, aplicaciones, grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar con esta guía.

## Creación de una aplicación
A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. En este caso, tanto la aplicación web como la API web se representarán mediante un **identificador de aplicación** único, ya que conforman una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

* Incluir una **aplicación web o una API web** en la aplicación.
* Escribir `https://localhost:44316/` como **URL de respuesta**. Es la dirección URL predeterminada para este ejemplo de código.
* Copiar el **identificador de aplicación** asignado a la aplicación. También lo necesitará más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Crear sus directivas
En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Esta aplicación web contiene tres experiencias de identidad: registro, inicio de sesión y edición de un perfil. Es preciso que cree una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

* Elegir el **nombre para mostrar** y los restantes atributos de registro en la directiva de registro.
* Elija las notificaciones de aplicación de **nombre para mostrar** e **id. de objeto** de cada directiva. Puede elegir también otras notificaciones.
* Copiar el **nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`. Necesitará esos nombres de directiva más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Una vez creadas las tres directivas, está listo para compilar la aplicación.

Tenga en cuenta que este artículo no trata sobre cómo usar las directivas que acaba de crear. Para más información acerca del funcionamiento de las directivas en Azure AD B2C, comience con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Descargar el código
El código de este tutorial [se mantiene en GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Para generar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). También puede clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

La aplicación completada también estará [disponible como archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Una vez descargado el código de ejemplo, abra el archivo .sln de Visual Studio para empezar.

## Configurar la aplicación web de tarea
Para que `TaskWebApp` se comunique con Azure AD B2C, es preciso especificar algunos parámetros comunes. En el proyecto `TaskWebApp`, abra el archivo `web.config` en la raíz del proyecto y reemplace los valores de la sección `<appSettings>`. Puede dejar los valores `AadInstance`, `RedirectUri` y `TaskServiceUrl` como están.

```
  <appSettings>

    ...

    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

<appSettings> <add key="webpages:Version" value="3.0.0.0" /> <add key="webpages:Enabled" value="false" /> <add key="ClientValidationEnabled" value="true" /> <add key="UnobtrusiveJavaScriptEnabled" value="true" /> <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" /> <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" /> <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" /> <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" /> <add key="ida:RedirectUri" value="https://localhost:44316/" /> <add key="ida:SignUpPolicyId" value="b2c\_1_sign\_up" /> <add key="ida:SignInPolicyId" value="b2c_1_sign\_in" /> <add key="ida:UserProfilePolicyId" value="b2c_1\_edit\_profile" /> <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" /> </appSettings>

## Obtener tokens de acceso y llamar a la API de la tarea
En esta sección se describe cómo usar el token recibido durante el inicio de sesión con Azure AD B2C para acceder a una API web que también está protegida con Azure AD B2C.

En este artículo no se explican los detalles de cómo proteger la API. Para saber de qué forma una API web autentica de forma segura las solicitudes con Azure AD B2C, consulte nuestro [artículo de introducción a la API web](active-directory-b2c-devquickstarts-api-dotnet.md).

### Almacenamiento del inicio de sesión en el token
En primer lugar, autentique el usuario (mediante cualquiera de las directivas) y reciba un token de inicio de sesión desde Azure AD B2C. Si no está seguro de cómo ejecutar las directivas, vuelva y consulte el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para conocer los conceptos básicos de Azure AD B2C.

Abra el archivo `App_Start\Startup.Auth.cs`. Hay un cambio importante que debe realizar en el `OpenIdConnectAuthenticationOptions`: debe establecer `SaveSignInToken = true`.

```C#
// App_Start\Startup.Auth.cs

return new OpenIdConnectAuthenticationOptions
{
    // For each policy, give OWIN the policy-specific metadata address, and
    // set the authentication type to the id of the policy
    MetadataAddress = String.Format(aadInstance, tenant, policy),
    AuthenticationType = policy,

    // These are standard OpenID Connect parameters, with values pulled from web.config
    ClientId = clientId,
    RedirectUri = redirectUri,
    PostLogoutRedirectUri = redirectUri,
    Notifications = new OpenIdConnectAuthenticationNotifications
    {
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",

        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### Obtención de un token en los controladores
`TasksController` es responsable de comunicarse con la API web, de enviar solicitudes HTTP a la API para que lea, cree y elimine tareas. Debido a que la API está protegida por Azure AD B2C, debe recuperar primero el token que guardó en el paso anterior.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;

    ...
}
```

El `BootstrapContext` contiene el token de inicio de sesión que adquirió al ejecutar una de las directivas B2C.

### Leer tareas de la API web
Cuando tenga un token, puede adjuntarlo a la solicitud `GET` HTTP en el encabezado `Authorization` para llamar de forma segura a `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### Creación y eliminación de tareas de la API web
Siga el mismo patrón al enviar las solicitudes `POST` y `DELETE` a la API web, mediante el uso de `BootstrapContext` para recuperar el token de inicio de sesión. La acción de creación se implementa automáticamente. Puede probar a finalizar la acción de eliminación en `TasksController.cs`.

## Ejecutar la aplicación de ejemplo
Finalmente, compile y ejecute la aplicación. Regístrese o inicie sesión en la aplicación y cree tareas para el usuario que ha iniciado sesión. Cierre la sesión y iníciela con otro usuario diferente. Cree tareas para ese usuario. Observe cómo se almacenan las tareas por usuario en la API, puesto que la API extrae la identidad del usuario del token que recibe.

Como referencia, el ejemplo completo [se proporciona como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). También puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0727_2016-->