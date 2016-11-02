<properties
	pageTitle="Azure AD B2C | Microsoft Azure"
	description="Creación de una API web de .NET con Azure Active Directory B2C, protegida con tokens de acceso de OAuth 2.0 para autenticación."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="07/22/2016"
	ms.author="dastrock"/>

# Azure Active Directory B2C: Creación de una API web de .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con Azure Active Directory (Azure AD) B2C, puede proteger una API web mediante el uso de tokens de acceso de OAuth 2.0. Estos tokens permiten a las aplicaciones cliente que usan Azure AD B2C autenticarse en la API. En este artículo se muestra cómo crear una API de "lista de tareas pendientes" del tipo Model-View-Controller (MVC) de .NET que permite a los usuarios realizar tareas de creación, lectura, actualización y eliminación. La API web se protege con Azure AD B2C y solo permite a los usuarios autenticados administrar sus listas de tareas pendientes.

## Creación de un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar con esta guía.

## Creación de una aplicación

A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir una **aplicación web** o una **API web** en la aplicación.
- Usar el **identificador uniforme de recursos de redirección** `https://localhost:44316/` para la aplicación web. Se trata de la ubicación predeterminada del cliente de aplicación web de este ejemplo de código.
- Copiar el **id. de aplicación** asignado a la aplicación. Lo necesitará más adelante.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Crear sus directivas

En Azure AD B2C, cada experiencia de usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). El cliente de este ejemplo de código contiene tres experiencias de identidad: registro, inicio de sesión y edición de perfil. Tendrá que crear una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

- Elegir **User ID sign-up** (Registro con id. de usuario) o **Email sign-up** (Registro con correo electrónico) en la hoja de proveedores de identidades.
- Seleccionar **Nombre para mostrar** y otros atributos de registro en la directiva de registro.
- Elegir las notificaciones **Nombre para mostrar** e **Id. de objeto** como notificaciones de aplicación en todas las directivas. Puede elegir también otras notificaciones.
- Copiar el **Nombre** de cada directiva después de crearla. Necesitará estos nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Cuando haya creado correctamente las tres directivas, estará listo para crear su aplicación.

## Descargar el código

El código de este tutorial [se mantiene en GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Para generar el ejemplo a medida que avanza, puede [descargar un proyecto de esqueleto como archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). También puede clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

La aplicación completada también estará [disponible como archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Una vez descargado el código de ejemplo, abra el archivo .sln de Visual Studio para empezar. El archivo de solución contiene dos proyectos: `TaskWebApp` y `TaskService`. `TaskWebApp` es una aplicación web MVC con la que el usuario interactúa. `TaskService` es la API web del back-end de la aplicación que almacena la lista de tareas pendientes de cada usuario.

## Configurar la aplicación web de tarea

Cuando el usuario interactúa con `TaskWebApp`, el cliente envía solicitudes a Azure AD y recibe tokens que sirven para llamar a la API web `TaskService`. Para iniciar la sesión del usuario y obtener tokens, debe proporcionar a `TaskWebApp` algo de información sobre la aplicación. En el proyecto `TaskWebApp`, abra el archivo `web.config` en la raíz del proyecto y reemplace los valores de la sección `<appSettings>`. Puede dejar los valores `AadInstance`, `RedirectUri` y `TaskServiceUrl` como están.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

Este artículo no incluye la creación del cliente `TaskWebApp`. Para obtener información sobre cómo crear una aplicación web con Azure AD B2C, consulte [nuestro tutorial de aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Proteger la API

Si tiene un cliente que llama a la API en nombre de los usuarios, puede proteger `TaskService` con tokens de portador de OAuth 2.0. La API puede aceptar y validar tokens mediante la biblioteca de Open Web Interface for .NET (OWIN) de Microsoft.

### Instalar OWIN
Empiece instalando la canalización de autenticación de OAuth de OWIN:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### Escribir los detalles de B2C
Abra el archivo `web.config` en la raíz del proyecto `TaskService` y reemplace los valores de la sección `<appSettings>`. Estos valores se usarán en toda la API y la biblioteca de OWIN. Puede dejar el valor `AadInstance` sin modificar.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### Agregar una clase de inicio de OWIN
Agregue una clase de inicio OWIN llamada `Startup.cs` al proyecto `TaskService`. Haga clic con el botón derecho en el proyecto, seleccione **Agregar** y **Nuevo elemento**; después, busque OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
	// The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### Configurar la autenticación de OAuth 2.0
Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(...)`.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### Proteger el controlador de la tarea
Cuando la aplicación esté configurada para usar la autenticación de OAuth 2.0, puede proteger su API web agregando una etiqueta `[Authorize]` al controlador de tareas. Este es el controlador donde tiene lugar cualquier manipulación de la lista de tareas pendientes, por lo que debe proteger todo el controlador en el nivel de clase. También puede agregar la etiqueta `[Authorize]` a determinadas acciones para un control específico.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

### Obtener la información del usuario del token
`TasksController` almacena las tareas en una base de datos donde cada tarea tiene un usuario asociado que la "posee". El propietario se identifica por el **Id. de objeto** del usuario. (por eso necesita agregar el Id. de objeto como una notificación de aplicación en todas sus directivas).

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## Ejecutar la aplicación de ejemplo

Por último, compile y ejecute `TaskWebApp` y `TaskService`. Regístrese en la aplicación con una dirección de correo electrónico o un nombre de usuario. Cree algunas tareas en la lista de tareas del usuario y observe cómo se conservan en la API incluso después de detener y reiniciar el cliente.

## Editar sus directivas

Una vez que haya protegido una API mediante el uso de Azure AD B2C, puede experimentar con las directivas de la aplicación y ver los efectos (o la ausencia de estos) en la API. Puede manipular las notificaciones de aplicación en las directivas y modificar la información de usuario que está disponible en la API web. Las notificaciones que agregue estarán disponibles para la API web de MVC de .NET en el objeto `ClaimsPrincipal`, como se describió antes en este artículo.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0727_2016-->