---
title: "Introducción a la API web de .NET de Azure AD | Microsoft Docs"
description: "Cómo crear una API web de .NET MVC que se integra con Azure AD para su autenticación y autorización."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: f44d75f45073a5d9aa9b1863ed227aba4efcf785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="help-protect-a-web-api-by-using-bearer-tokens-from-azure-ad"></a>Ayuda para proteger una API web con tokens portadores de Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Si va a crear una aplicación que proporciona acceso a recursos protegidos, debe saber cómo proteger dichos recursos frente a accesos injustificados.
Azure Active Directory (Azure AD) facilita la protección de una API web mediante tokens de acceso portadores de OAuth 2.0 con solo unas pocas líneas de código.

En las aplicaciones web ASP.NET, puede realizar esta protección con la implementación de Microsoft del middleware OWIN controlado por la comunidad incluido en .NET Framework 4.5. En este caso, usaremos OWIN para compilar una API web de "Lista de tareas pendientes" que lleve a cabo las siguientes acciones:

* Designar las API que están protegidas.
* Validar que las llamadas de API web contengan un token de acceso válido.

Para compilar la API de lista de tareas pendientes, primero debe:

1. Registrar una aplicación con Azure AD.
2. Configurar la aplicación para que use la canalización de autenticación OWIN.
3. Configurar una aplicación cliente para llamar a la API web.

Para empezar, [descargue el esquema de la aplicación](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Cualquiera de los dos es una solución de Visual Studio 2013. También necesitará a un inquilino de Azure AD en el que registrar la aplicación. Si aún no tiene uno, [descubra cómo conseguirlo](active-directory-howto-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Paso 1: Registro de una aplicación con Azure AD
Para proteger su aplicación, primero debe crear una aplicación en el inquilino y proporcionar a Azure AD algunos elementos de información clave.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En la barra superior, haga clic en su cuenta. En la lista **Directorio**, elija el inquilino de Azure AD donde desea registrar la aplicación.

3. Haga clic en **Más servicios** en el panel izquierdo y seleccione **Azure Active Directory**.

4. Haga clic en **Registros de aplicaciones** y seleccione **Agregar**.

5. Siga las indicaciones y cree una nueva **aplicación web y/o API web**.
  * **Nombre** describe la aplicación a los usuarios. Escriba **Servicio de lista de tareas pendientes**.
  * El **Uri de redirección** es una combinación de esquema y cadena que usa Azure AD para devolver los tokens solicitados por la aplicación. Escriba `https://localhost:44321/` para este valor.

6. En la página **Configuración** -> **Propiedades** de la aplicación, actualice el URI del identificador de la aplicación. Especifique un identificador específico del inquilino. Por ejemplo, escriba: `https://contoso.onmicrosoft.com/TodoListService`.

7. Guarde la configuración. Deje abierto el portal, ya que también deberá registrar su aplicación cliente en breve.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Paso 2: Configuración de la aplicación para que use la canalización de autenticación OWIN
Para validar solicitudes y tokens entrantes, debe configurar la aplicación para que se comunique con Azure AD.

1. Para comenzar, abra la solución y agregue los paquetes NuGet de middleware OWIN al proyecto TodoListService mediante la Consola del Administrador de paquetes.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Agregue una Clase de inicio OWIN al proyecto de ServicioListaTodo llamado `Startup.cs`.  Haga clic con el botón derecho en el proyecto, seleccione **Agregar** > **Nuevo elemento** y luego busque **OWIN**. El middleware OWIN invocará el método `Configuration(…)` al iniciarse la aplicación.

3. Cambie la declaración de clase a `public partial class Startup`. Ya hemos implementado parte de esta clase en otro archivo. En el método `Configuration(…)`, realice una llamada a `ConfgureAuth(…)` para configurar la autenticación para su aplicación web.

    ```C#
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(…)`. Los parámetros que proporciona en `WindowsAzureActiveDirectoryBearerAuthenticationOptions` servirán como coordenadas para que su aplicación se comunique con Azure AD.

    ```C#
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. Ahora puede usar atributos `[Authorize]` como ayuda para proteger los controladores y acciones con la autenticación de portador de JSON Web Token (JWT). Decore la clase `Controllers\TodoListController.cs` con una etiqueta Autorizar. Esto obligará al usuario a iniciar sesión antes de tener acceso a esa página.

    ```C#
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Cuando un autor de llamada autorizado invoca correctamente una de las API `TodoListController` , es posible que la acción deba tener acceso a información sobre este. OWIN proporciona acceso a las notificaciones dentro del token portador a través del objeto `ClaimsPrincpal` .  

6. Un requisito común de las API web es validar los "ámbitos" presentes en el token. De esta forma se garantiza que el usuario ha dado su consentimiento a los permisos necesarios para acceder al servicio de lista de tareas pendientes.

    ```C#
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Abra el archivo `web.config` en la raíz del proyecto TodoListService y escriba sus valores de configuración en la sección `<appSettings>`.
  * `ida:Tenant` es el nombre del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
  * `ida:Audience` es el URI de id. de aplicación de la aplicación que escribió en Azure Portal.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Paso 3: Configuración de una aplicación cliente y ejecución del servicio
Para poder ver el servicio de lista de tareas pendientes en acción, debe configurar el cliente de lista de tareas pendientes para que pueda obtener tokens de Azure AD y realizar llamadas al servicio.

1. Vuelva a [Azure Portal](https://portal.azure.com).

2. Cree una nueva aplicación en su inquilino de Azure AD y seleccione **Aplicación de cliente nativo** en la indicación resultante.
  * **Nombre** describe la aplicación a los usuarios.
  * Escriba `http://TodoListClient/` para el valor **Uri de redirección** .

3. Cuando termine el registro, Azure AD asignará un identificador de aplicación único a la aplicación. Necesitará este valor en las secciones siguientes, así que cópielo de la página de la aplicación.

4. En la página **Configuración**, seleccione **Permisos necesarios** y **Agregar**. Busque y seleccione el servicio de lista de tareas pendientes, agregue el permiso **Acceder a TodoListService** en **Permisos delegados** y luego haga clic en **Listo**.

5. En Visual Studio, abra `App.config` en el proyecto TodoListClient y escriba sus valores de configuración en la sección `<appSettings>`.

  * `ida:Tenant` es el nombre del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
  * `ida:ClientId` es el id. de aplicación que copió de Azure Portal.
  * `todo:TodoListResourceId` es el URI de id. de aplicación de la aplicación del servicio de lista de tareas pendientes que escribió en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes
Por último, limpie, compile y ejecute cada proyecto. Si aún no lo ha hecho, ahora es el momento de crear un nuevo usuario en su inquilino con un dominio *.onmicrosoft.com. Inicie sesión en el cliente de lista de tareas pendientes con ese usuario y agregue algunas tareas a la lista de tareas pendientes del usuario.

Como referencia, en [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip) está disponible el ejemplo finalizado (sin sus valores de configuración). Ahora puede continuar con más escenarios de identidad.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
