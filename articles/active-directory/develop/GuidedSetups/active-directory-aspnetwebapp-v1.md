---
title: "Introducción al servidor web ASP.NET de Azure AD v1 1 Microsoft Docs"
description: "Implementación de inicio de sesión de Microsoft en una solución ASP.NET con una aplicación basada en un explorador web tradicional mediante el estándar OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/08/2017
ms.author: andret
ms.openlocfilehash: b23afd26f7ac1828381a0410d2455206c8f43c88
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
<!--start-intro-->
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Adición de inicio de sesión con Microsoft a una aplicación web ASP.NET

Esta guía muestra cómo implementar el inicio de sesión con Microsoft mediante una solución MVC de ASP.NET con una aplicación basada en explorador web tradicional mediante OpenID Connect. 

Al final de esta guía, la aplicación aceptará inicios de sesión de cuentas profesionales y educativas de organización que se han integrado con Azure Active Directory.

> [!NOTE]
> Esta configuración paso a paso ayuda a habilitar los inicios de sesión de cuentas profesionales y educativas en la aplicación ASP.NET. Si le interesa habilitar los inicios de sesión para las cuentas personales además de hacerlo para las cuentas profesionales y educativas, puede usar el [punto de conexión v2](../active-directory-v2-compare.md). Consulte [esta configuración paso a paso de ASP.NET para el punto de conexión v2](./active-directory-aspnetwebapp.md) al igual que [este documento](../active-directory-v2-limitations.md) donde se explican las limitaciones actuales del punto de conexión v2.
<br/><br/>

<!--separator-->

> Esta guía requiere Visual Studio 2015 Update 3 o Visual Studio 2017.  ¿No lo tiene?  [Descargue Visual Studio 2017 de manera gratuita](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Funcionamiento de esta guía

![Funcionamiento de esta guía](media/active-directory-aspnetwebapp-v1/aspnet-intro.png)

Esta guía se basa en un escenario en el que un explorador tiene acceso a un sitio web de ASP.NET y solicita a un usuario que se autentique a través de un botón de inicio de sesión. En esa situación, la mayoría del trabajo para representar la página web se produce en el servidor.

> [!NOTE]
> Esta configuración paso a paso muestra cómo iniciar sesión de los usuarios en una aplicación web ASP.NET a partir de una plantilla vacía e incluye pasos como agregar un botón de inicio de sesión y cada controlador y método, a la vez que se exploran también algunos conceptos. De manera alternativa, también puede crear un proyecto para iniciar sesión de los usuarios de Azure Active Directory (cuentas profesionales y educativas) mediante el uso de la [plantilla web de Visual Studio](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) y la selección de *cuentas de organización* y, luego, una de las opciones de la nube. Esta opción usa una plantilla enriquecida, con controladores, métodos y vistas adicionales.

## <a name="libraries"></a>Bibliotecas

En esta guía se usan los paquetes siguientes:

|Biblioteca|DESCRIPCIÓN|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware que permite a una aplicación que use OpenIDConnect para la autenticación|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware que permite a una aplicación que mantenga la sesión del usuario mediante cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Permite que aplicaciones basadas en OWIN se ejecuten en IIS mediante la canalización de solicitudes ASP.NET|


<!--end-intro-->

<!--start-setup-->

## <a name="set-up-your-project"></a>Configurar su proyecto

En esta sección se muestran los pasos necesarios para instalar y configurar la canalización de autenticación a través del middleware OWIN en un proyecto de ASP.NET con OpenID Connect. 

> ¿Prefiere descargar este proyecto de Visual Studio de ejemplo en su lugar? [Descargue un proyecto](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip) y vaya al [paso de configuración](#configure-your-webconfig-and-register-an-application) para configurar el código de ejemplo antes de ejecutarlo.

## <a name="create-your-aspnet-project"></a>Creación del proyecto de ASP.NET
1. En Visual Studio: `File` > `New` > `Project`<br/>
2. En *Visual C#\Web*, seleccione `ASP.NET Web Application (.NET Framework)`.
3. Asigne un nombre a la aplicación y haga clic en *Aceptar*.
4. Seleccione `Empty` y active la casilla de verificación para agregar referencias de `MVC`

## <a name="add-authentication-components"></a>Adición de componentes de autenticación

1. En Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Agregue los *paquetes de NuGet de middleware de OWIN* escribiendo lo siguiente en la ventana de la Consola del Administrador de paquetes:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>Información sobre estos paquetes
>Las bibliotecas anteriores habilitan el inicio de sesión único (SSO) utilizando OpenID Connect a través de la autenticación basada en cookies. Una vez que se completa la autenticación y se envía el token que representa al usuario a la aplicación, el middleware de OWIN crea una cookie de sesión. El explorador usa a continuación esta cookie en solicitudes posteriores para que el usuario no tenga que volver a autenticarse y no es necesaria ninguna comprobación adicional.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configuración de la canalización de autenticación
Los pasos siguientes se usan para crear una *clase de inicio* del middleware de OWIN para configurar la autenticación de OpenID Connect. Esta clase se ejecuta automáticamente.

> [!TIP]
> Si el proyecto no tiene un archivo `Startup.cs` en la carpeta raíz:<br/>
> 1. Haga clic con el botón derecho en la carpeta raíz del proyecto: >    `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Asígnele el nombre `Startup.cs`.<br/>
>
>> Asegúrese de que la clase seleccionada es una clase de inicio de OWIN y no una clase estándar de C#. Para confirmarlo, compruebe si ve `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` encima del espacio de nombres.


1. Agregue los espacios de nombres *OWIN* y *Microsoft.IdentityModel* a `Startup.cs`:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Reemplace la clase de inicio por el código siguiente:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=Startup "Startup.cs")]
    
<!--start-collapse-->
> [!NOTE]
> Los parámetros que se proporcionan en *OpenIDConnectAuthenticationOptions* sirven de coordenadas para que la aplicación se comunique con Azure AD. Dado el que middleware de OpenID Connect usa cookies, también debe configurar la autenticación con cookies como muestra el código anterior. El valor *ValidateIssuer* indica a OpenIdConnect que no restrinja el acceso a una organización específica.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Agregar un controlador para controlar las solicitudes de inicio de sesión y cierre de sesión

En este paso se muestra cómo crear un controlador para exponer los métodos de inicio de sesión y cierre de sesión.

1.  Haga clic con el botón derecho en la carpeta `Controllers` y seleccione `Add` > `Controller`
2.  Seleccione `MVC (.NET version) Controller – Empty`.
3.  Haga clic en *Agregar*.
4.  Asígnele el nombre `HomeController` y haga clic en *Aceptar*.
5.  Agregue espacios de nombres *OWIN* a la clase:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Agregue los métodos siguientes para controlar el inicio de sesión y el cierre de sesión en el controlador iniciando un desafío de autenticación a través de código:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=SigInAndSignOut "HomeController.cs")]
    
## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Creación de la página principal de la aplicación para que los usuarios inicien sesión mediante un botón de inicio de sesión

En Visual Studio, cree otra vista para agregarle el botón de inicio de sesión y mostrar información del usuario tras la autenticación:

1.  Haga clic con el botón derecho en la carpeta `Views\Home` y seleccione `Add View`
2.  Asígnele el nombre `Index`.
3.  Agregue el código HTML siguiente, que incluye el botón de inicio de sesión, al archivo:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
> [!NOTE]
> Esta página agrega un botón de inicio de sesión en formato SVG con un fondo negro:<br/>![Iniciar sesión en Microsoft](media/active-directory-aspnetwebapp-v1/aspnetsigninbuttonsample.png)<br/> Para más botones de inicio de sesión, vaya a [esta página](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Directrices de marca").
<!--end-collapse-->

## <a name="display-users-claims-by-adding-a-controller"></a>Agregar un controlador para mostrar las notificaciones del usuario
Este controlador muestra los usos del atributo `[Authorize]` para proteger un controlador. Este atributo restringe el acceso al controlador permitiendo únicamente usuarios autenticados. El código siguiente usa el atributo para mostrar notificaciones de usuario que se han recuperado como parte del inicio de sesión.

1.  Haga clic con el botón derecho en la carpeta `Controllers`: `Add` > `Controller`
2.  Seleccione `MVC {version} Controller – Empty`.
3.  Haga clic en *Agregar*.
4.  Asígnele el nombre `ClaimsController`.
5.  Reemplace el código de la clase de controlador por el código siguiente; el atributo `[Authorize]` se agrega a la clase:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> Debido al uso del atributo `[Authorize]`, todos los métodos de este controlador solo pueden ejecutarse si el usuario está autenticado. Si el usuario no se ha autenticado e intenta acceder al controlador, OWIN inicia un desafío de autenticación y obligará al usuario a autenticarse. El código anterior busca en la colección de notificaciones del usuario para los atributos específicos incluidos en el token del usuario. Estos atributos incluyen el nombre completo del usuario y el nombre de usuario, así como el firmante del identificador de usuario global. También contienen el *Id. del inquilino*, que representa el identificador de la organización del usuario. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Creación de una vista que muestre las notificaciones del usuario

En Visual Studio, cree otra vista para mostrar las notificaciones del usuario en una página web:

1.  Haga clic con el botón derecho en la carpeta `Views\Claims` y: `Add View`
2.  Asígnele el nombre `Index`.
3.  Agregue el código HTML siguiente al archivo:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]
    
<!--end-use-->

<!--start-configure-->
## <a name="configure-your-webconfig-and-register-an-application"></a>Configure *web.config* y registre una aplicación

1. En Visual Studio, agregue lo siguiente en `web.config` (ubicado en la carpeta raíz) en la sección `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
    ```
2. En el Explorador de soluciones, seleccione el proyecto y observe la ventana <i>Propiedades</i> (si no ve una ventana Propiedades, presione F4)
3. Cambie SSL habilitado a <code>True</code>.
4. Copie la dirección URL de SSL del proyecto en el Portapapeles:<br/><br/>![Propiedades del proyecto](media/active-directory-aspnetwebapp-v1/visual-studio-project-properties.png)<br />
5. En <code>web.config</code>, reemplace <code>Enter_the_Redirect_URL_here</code> por la dirección URL de SSL del proyecto 

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Registre la aplicación en Azure Portal y, luego, agregue su información a *web.config*

1. Vaya a [Microsoft Azure Portal: registros de aplicaciones](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) para registrar una aplicación
2. Seleccionar `New application registration`
3. Escriba un nombre para la aplicación
4. Pegue la *dirección URL de SSL* del proyecto de Visual Studio en `Sign-on URL` (esta dirección URL también se agrega a la lista de direcciones URL de respuesta de la aplicación que registra)
5. Haga clic en `Create` para registrar la aplicación. Esta acción lo devuelve a la lista de aplicaciones
6. Ahora, busque o seleccione la aplicación que acaba de crear para abrir sus propiedades
7. Copie el GUID que se encuentra en `Application ID` al Portapapeles
8. Vuelva a Visual Studio y, en `web.config`, reemplace `Enter_the_Application_Id_here` por el identificador de aplicación de la aplicación que acaba de registrar

> [!TIP]
> Si la cuenta está configurada para acceder a varios directorios, asegúrese de seleccionar el directorio correcto de la organización en la que desea registrar la aplicación. Para ello, haga clic en el nombre de la cuenta en la esquina superior derecha de Azure Portal y, luego, compruebe que el directorio seleccionado es el que se indica:<br/>![Selección del directorio correcto](media/active-directory-aspnetwebapp-v1/tenantselector.png)

## <a name="configure-sign-in-options"></a>Configuración de las opciones de inicio de sesión

Puede configurar la aplicación para que solo permita que los usuarios que pertenecen a la instancia de Azure Active Directory de una organización inicien sesión o para que acepte los inicios de sesión de usuarios que pertenecen a cualquier organización. Siga las instrucciones de una de estas opciones:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Configure la aplicación para que permita inicios de sesión de cuentas profesionales y educativas de cualquier empresa u organización (multiinquilino)

Siga estos pasos si desea aceptar inicios de sesión de cuentas profesionales y educativas de cualquier empresa u organización que se integró con Azure Active Directory. Este es un escenario común para las *aplicaciones de SaaS*:

1. Vuelva a [Microsoft Azure Portal: registros de aplicaciones](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) y ubique la aplicación que acaba de registrar
2. En `All Settings`, seleccione `Properties`
3. Cambie la propiedad `Multi-tenanted` a `Yes` y haga clic en `Save`

Para más información sobre esta configuración y el concepto de las aplicaciones multiinquilino, consulte [este artículo](../active-directory-devhowto-multi-tenant-overview.md "Introducción a multiinquilino").

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Restricción de los usuarios de una instancia de Active Directory de solo una organización para que inicien sesión en la aplicación (único inquilino)

Esta opción es un escenario común para las *aplicaciones de línea de negocio*: si desea que la aplicación acepte inicios de sesión solo de cuentas que pertenecen a una instancia específica de Azure Active Directory (incluidas las *cuentas invitadas* de esa instancia), reemplace el parámetro `Tenant` en *web.config* de `Common` por el nombre de inquilino de la organización, por ejemplo, *contoso.onmicrosoft.com*. Después, cambie el argumento `ValidateIssuer` de la [*Clase de inicio OWIN*](#configure-the-authentication-pipeline) a `true`.

Para solo permitir usuarios de una lista de organizaciones específicas, establezca `ValidateIssuer` en true y use el parámetro `ValidIssuers` para especificar una lista de las organizaciones.

Otra posibilidad es implementar un método personalizado para validar los emisores con el parámetro *IssuerValidator*. Para más información sobre `TokenValidationParameters`, consulte [este artículo de MSDN](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "artículo de MSDN sobre TokenValidationParameters").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](media/active-directory-aspnetwebapp-v1/vsprojectproperties.png)<br />
4.  Add the following in `web.config` file located in root’s folder, under section `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
```
-->
<!--end-configure-arp-->
<!--start-test-->
## <a name="test-your-code"></a>Prueba del código

Presione `F5` para ejecutar el proyecto en Visual Studio. El explorador se abre y le dirige a *http://localhost:{port}*, donde ve el botón *Iniciar sesión en Microsoft*. Continúe y haga clic en él para iniciar sesión.

Cuando esté listo para realizar una prueba, use una cuenta profesional (Azure Active Directory) para iniciar sesión. 

![Ventana Iniciar sesión en Microsoft del explorador](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin.png)

![Ventana Iniciar sesión en Microsoft del explorador](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Resultados esperados
Después de iniciar sesión, se redirige al usuario a la página principal del sitio web correspondiente a la dirección URL HTTPS especificada en la información de registro de la aplicación en el Portal de registro de aplicaciones de Microsoft. Esta página muestra ahora *Hola, {usuario}* y un vínculo para cerrar sesión, así como un vínculo para ver las notificaciones del usuario (que es un vínculo al controlador de autorización creado anteriormente).

### <a name="see-users-claims"></a>Ver notificaciones del usuario
Seleccione el hipervínculo para ver la notificaciones del usuario. Esta acción le lleva hasta el controlador y una vista que solo está disponible para los usuarios autenticados.

#### <a name="expected-results"></a>Resultados esperados
 Debe ver una tabla que contiene las propiedades básicas del usuario que ha iniciado sesión:

| Propiedad | Valor | DESCRIPCIÓN|
|---|---|---|
| NOMBRE | {Nombre completo del usuario} | Nombre y apellido del usuario
|Nombre de usuario | <span>user@domain.com</span>| Nombre de usuario utilizado para identificar al usuario
| Asunto| {Firmante}|Cadena que identifica de forma única el inicio de sesión de usuario en la web|
| Id. de inquilino| {Guid}| *guid* que representa de forma única la organización de Azure Active Directory del usuario.|

Además, ve una tabla con todas las notificaciones de usuario incluidas en la solicitud de autenticación. Para una lista de todas las notificaciones de un token de identificador y su explicación, consulte este [artículo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "Lista de notificaciones en token de identificador").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Probar el acceso a un método que tiene un atributo *[Authorize]* (opcional)
En este paso, prueba el acceso al controlador de notificaciones como usuario anónimo:<br/>
Seleccione el vínculo al cierre de sesión del usuario y complete el proceso de cierre de sesión.<br/>
Ya en el explorador, escriba http://localhost:{port}/claims para acceder al controlador que está protegido con el atributo `[Authorize]`

#### <a name="expected-results"></a>Resultados esperados
Debe recibir el mensaje que le pide que se autentique para ver la vista.

## <a name="additional-information"></a>Información adicional

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Proteger todo el sitio web
Para proteger todo el sitio web, agregue `AuthorizeAttribute` a `GlobalFilters` en el método `Global.asax` `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->