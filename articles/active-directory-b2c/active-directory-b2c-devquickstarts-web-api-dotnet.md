---
title: Azure Active Directory B2C | Microsoft Docs
description: "Creación de una aplic. web de .NET y llamada a una API web con Azure Active Directory B2C y tokens de acceso de OAuth 2.0."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.openlocfilehash: 48452eb68f826d1c7aa61d5e5531f941ac1422b0
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Azure AD B2C: llamada a una API web de .NET desde una aplic. web de .NET

Con Azure AD B2C, puede agregar características eficaces de administración de identidades a las aplicaciones web y las API web. En este artículo se describe cómo solicitar tokens de acceso y hacer llamadas desde una aplic. web de "tareas pendientes" .NET a una API web de .NET.

Este artículo no trata de la implementación de la administración de registros, inicios de sesión y perfiles con Azure AD B2C. Se centra en la llamada a las API web después de que el usuario ya está autenticado. Si todavía no lo ha hecho, debe hacer lo siguiente:

* Comenzar a trabajar con una [aplicación web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Comenzar a trabajar con una [API web .NET](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Requisito previo

Para crear una aplicación web que llama a una API web, debe hacer lo siguiente:

1. [Crear un inquilino de Azure AD B2C](active-directory-b2c-get-started.md).
2. [Registrar una API web](active-directory-b2c-app-registration.md#register-a-web-api).
3. [Registrar una aplicación web](active-directory-b2c-app-registration.md#register-a-web-app).
4. [Configurar directivas](active-directory-b2c-reference-policies.md).
5. [Conceder a la aplicación web permisos para usar la API web](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> La API de web y la aplicación de cliente deben usar el mismo directorio de Azure AD B2C.
>

## <a name="download-the-code"></a>Descargar el código

El código de este tutorial se conserva [en GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Puede clonar el ejemplo al ejecutar lo siguiente:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Una vez descargado el código de ejemplo, abra el archivo .sln de Visual Studio para empezar. El archivo de solución contiene dos proyectos: `TaskWebApp` y `TaskService`. `TaskWebApp` es una aplicación web MVC con la que el usuario interactúa. `TaskService` es la API web del back-end de la aplicación que almacena la lista de tareas pendientes de cada usuario. En este artículo no se incluye la creación de la aplicación web `TaskWebApp` ni de la API web `TaskService`. Para información sobre cómo crear la aplicación web .NET con Azure AD B2C, consulte el [tutorial de aplicación web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Para información sobre cómo crear la API web .NET protegida con Azure AD B2C, consulte el [tutorial de API web .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Actualización de la configuración de Azure AD B2C

Nuestro ejemplo está configurado para usar las directivas y el identificador de cliente de nuestro inquilino de demostración. Si desea usar su propio inquilino:

1. Abra `web.config` en el proyecto `TaskService` y reemplace los valores de

    * `ida:Tenant` por el nombre del inquilino
    * `ida:ClientId` por el identificador de la aplicación de API web
    * `ida:SignUpSignInPolicyId` por el nombre de directiva "Inicio de sesión o registro"

2. Abra `web.config` en el proyecto `TaskWebApp` y reemplace los valores de

    * `ida:Tenant` por el nombre del inquilino
    * `ida:ClientId` por el identificador de la aplicación de API web
    * `ida:ClientSecret` por la clave secreta de aplicación web
    * `ida:SignUpSignInPolicyId` por el nombre de directiva "Inicio de sesión o registro"
    * `ida:EditProfilePolicyId` por el nombre de la directiva "Editar perfil"
    * `ida:ResetPasswordPolicyId` por el nombre de la directiva "Restablecer contraseña"



## <a name="requesting-and-saving-an-access-token"></a>Solicitud y guardado de un token de acceso

### <a name="specify-the-permissions"></a>Especificación de los permisos

Para realizar la llamada a la API web, deberá autenticar el usuario (con la directiva de registro o inicio de sesión) y [recibir un token de acceso](active-directory-b2c-access-tokens.md) de Azure AD B2C. Para recibir un token de acceso, primero debe especificar los permisos que le gustaría que concediera el token de acceso. Los parámetros se especifican en el parámetro `scope` cuando hace la solicitud al punto de conexión `/authorize`. Por ejemplo, para adquirir un token de acceso con el permiso de "lectura" a la aplicación de recursos que tiene el URI de id. de aplicación de `https://contoso.onmicrosoft.com/tasks`, el ámbito sería `https://contoso.onmicrosoft.com/tasks/read`.

Para especificar el ámbito del ejemplo, abra el archivo `App_Start\Startup.Auth.cs` y defina la variable `Scope` en OpenIdConnectAuthenticationOptions.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Intercambio del código de autorización por un token de acceso

Después de que un usuario completa la experiencia de registro o inicio de sesión, la aplicación recibirá un código de autorización de Azure AD B2C. El middleware de OpenID Connect de OWIN almacenará el código, pero no lo intercambiará por un token de acceso. Puede usar la [biblioteca de MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) para hacer el intercambio. En el ejemplo, configuramos una devolución de llamada de notificación en el middleware de OpenID Connect para cada vez que se reciba un código de autorización. En la devolución de llamada, usamos MSAL para intercambiar el código por un token y guardar el token en la caché.

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>Llamada a la API web

En esta sección se describe cómo usar el token recibido durante el registro o el inicio de sesión con Azure AD B2C para tener acceso a la API web.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Recuperación del token guardado en los controladores

`TasksController` es responsable de comunicarse con la API web y de enviar solicitudes HTTP a la API para leer, crear y eliminar tareas. Debido a que la API está protegida por Azure AD B2C, debe recuperar primero el token que guardó en el paso anterior.

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Leer tareas de la API web

Cuando tenga un token, puede adjuntarlo a la solicitud `GET` HTTP en el encabezado `Authorization` para llamar de forma segura a `TaskService`:

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Creación y eliminación de tareas de la API web

Siga el mismo patrón al enviar las solicitudes `POST` y `DELETE` a la API web, mediante el uso de MSAL para recuperar el token de acceso desde la caché.

## <a name="run-the-sample-app"></a>Ejecutar la aplicación de ejemplo

Por último, compile y ejecute ambas aplicaciones. Regístrese o inicie sesión en la aplicación y cree tareas para el usuario que ha iniciado sesión. Cierre la sesión y iníciela con otro usuario diferente. Cree tareas para ese usuario. Observe cómo se almacenan las tareas por usuario en la API, puesto que la API extrae la identidad del usuario del token que recibe. Intente también jugar con los ámbitos. Quite el permiso para "escribir" y, luego, intente agregar una tarea. Solo debe asegurarse de cerrar sesión cada vez que cambia el ámbito.

