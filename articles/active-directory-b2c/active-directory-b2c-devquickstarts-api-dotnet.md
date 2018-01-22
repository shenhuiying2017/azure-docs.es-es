---
title: "Protección de una API web (ASP.NET): Azure Active Directory B2C | Microsoft Docs"
description: "Creación de una API web de .NET con Azure Active Directory B2C, protegida con tokens de acceso de OAuth 2.0 para autenticación."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: mtillman
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 01/14/2018
ms.author: parakhj
ms.custom: seohack1
ms.openlocfilehash: 68c0f50e20b5a1a44ff2cb1bc5df35d60928b911
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: Creación de una API web de .NET

Con Azure Active Directory (Azure AD) B2C, puede proteger una API web mediante el uso de tokens de acceso de OAuth 2.0. Estos tokens permiten a las aplicaciones cliente autenticarse en la API. En este artículo se muestra cómo crear una API de "lista de tareas pendientes" de MVC de .NET que permite a los usuarios de la aplicación cliente realizar tareas de creación, lectura, actualización y eliminación. La API web se protege con Azure AD B2C y solo permite a los usuarios autenticados administrar sus listas de tareas pendientes.

## <a name="create-an-azure-ad-b2c-directory"></a>Creación de un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar con esta guía.

> [!NOTE]
> La API de web y la aplicación de cliente deben usar el mismo directorio de Azure AD B2C.
>

## <a name="create-a-web-api"></a>Creación de una API web

Después, debe crear una aplicación de API web en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

* Incluir una **aplicación web** o una **API web** en la aplicación.
* Use el **URI de redirección** `https://localhost:44332/` para la aplicación web. Se trata de la ubicación predeterminada del cliente de aplicación web de este ejemplo de código.
* Copiar el **identificador de aplicación** asignado a la aplicación. Lo necesitará más adelante.
* Escriba un identificador de la aplicación en **URI de id. de aplicación**. Copie el **URI de id. de aplicación** completo. Lo necesitará más adelante.
* Agregue los permisos mediante el menú **Ámbitos publicados**.

## <a name="create-your-policies"></a>Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Debe crear una directiva para comunicarse con Azure AD B2C. Se recomienda usar la directiva de registro y de inicio de sesión combinada, tal como se describe en el [artículo de referencia de la directiva](active-directory-b2c-reference-policies.md). Al crear la directiva, tenga en cuenta lo siguiente:

* Seleccionar **Nombre para mostrar** y otros atributos de registro en la directiva.
* Elegir las notificaciones **Nombre para mostrar** e **Id. de objeto** como notificaciones de aplicación en todas las directivas. Puede elegir también otras notificaciones.
* Copiar el **nombre** de cada directiva después de crearla. Necesitará el nombre de la directiva más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Cuando haya creado correctamente la directiva, estará listo para crear su aplicación.

## <a name="download-the-code"></a>Descargar el código

El código de este tutorial se conserva [en GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Puede clonar el ejemplo al ejecutar lo siguiente:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Una vez descargado el código de ejemplo, abra el archivo .sln de Visual Studio para empezar. El archivo de solución contiene dos proyectos: `TaskWebApp` y `TaskService`. `TaskWebApp` es una aplicación web MVC con la que el usuario interactúa. `TaskService` es la API web del back-end de la aplicación que almacena la lista de tareas pendientes de cada usuario. En este artículo se describe únicamente la aplicación `TaskService`. Para más información sobre cómo crear `TaskWebApp` con Azure AD B2C, consulte [nuestro tutorial de aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Actualización de la configuración de Azure AD B2C

Nuestro ejemplo está configurado para usar las directivas y el identificador de cliente de nuestro inquilino de demostración. Si desea usar su propio inquilino, debe hacer lo siguiente:

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
    * `api:ApiIdentifier` con su "URI de id. de aplicación"


## <a name="secure-the-api"></a>Proteger la API

Si tiene un cliente que llama a la API, puede proteger la API (por ejemplo `TaskService`) con tokens de portador de OAuth 2.0. Esto garantiza que cada solicitud a la API solo será válida si la solicitud tiene un token de portador. La API puede aceptar y validar tokens de portador mediante la biblioteca de Open Web Interface for .NET (OWIN) de Microsoft.

### <a name="install-owin"></a>Instalar OWIN

Empiece instalando la canalización de autenticación OAuth de OWIN mediante la consola del Administrador de paquetes de Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

Este modo se instalará el middleware de OWIN que aceptará y validará los tokens de portador.

### <a name="add-an-owin-startup-class"></a>Agregar una clase de inicio de OWIN

Agregue una clase de inicio OWIN a la API llamada `Startup.cs`.  Haga clic con el botón derecho en el proyecto, seleccione **Agregar** y **Nuevo elemento**; después, busque OWIN. El middleware OWIN invocará el método `Configuration(…)` al iniciarse la aplicación.

En nuestro ejemplo, hemos cambiado la declaración de clase a `public partial class Startup` y hemos implementado la otra parte de la clase en `App_Start\Startup.Auth.cs`. En el método `Configuration`, se agrega una llamada a `ConfigureAuth`, que se define en `Startup.Auth.cs`. Después de las modificaciones, `Startup.cs` es similar a lo siguiente:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Configurar la autenticación de OAuth 2.0

Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(...)`. Por ejemplo, podría ser similar al siguiente:

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>Proteger el controlador de la tarea

Cuando la aplicación esté configurada para usar la autenticación de OAuth 2.0, puede proteger su API web agregando una etiqueta `[Authorize]` al controlador de tareas. Este es el controlador donde tiene lugar cualquier manipulación de la lista de tareas pendientes, por lo que debe proteger todo el controlador en el nivel de clase. También puede agregar la etiqueta `[Authorize]` a determinadas acciones para un control específico.

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Obtener la información del usuario del token

`TasksController` almacena las tareas en una base de datos donde cada tarea tiene un usuario asociado que la "posee". El propietario se identifica por el **Id. de objeto** del usuario. (por eso necesita agregar el Id. de objeto como una notificación de aplicación en todas sus directivas).

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>Validación de los permisos en el token

Un requisito común de las API web es validar los "ámbitos" presentes en el token. De esta forma se garantiza que el usuario ha dado su consentimiento a los permisos necesarios para acceder al servicio de lista de tareas pendientes.

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>Ejecutar la aplicación de ejemplo

Por último, compile y ejecute `TaskWebApp` y `TaskService`. Cree algunas tareas en la lista de tareas del usuario y observe cómo se conservan en la API incluso después de detener y reiniciar el cliente.

## <a name="edit-your-policies"></a>Editar sus directivas

Una vez que haya protegido una API mediante el uso de Azure AD B2C, puede experimentar con la directiva de inicio de sesión y registro, y ver los efectos (o la ausencia de estos) en la API. Puede manipular las notificaciones de aplicación en las directivas y modificar la información de usuario que está disponible en la API web. Las notificaciones que agregue estarán disponibles para la API web de MVC de .NET en el objeto `ClaimsPrincipal` , como se describió antes en este artículo.
