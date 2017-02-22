---
title: "Introducción a Xamarin de Azure AD | Microsoft Docs"
description: "Creación de una aplicación Xamarin que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
services: active-directory
documentationcenter: xamarin
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: 4852939cb577d0b41a606f0d94897edfb367c4d6


---
# <a name="integrate-azure-ad-into-a-xamarin-app"></a>Integración de Azure AD en una aplicación Xamarin
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Xamarin le permite escribir aplicaciones móviles en C# que se pueden ejecutar en iOS, Android y Windows (PC y dispositivos móviles). Si está creando una aplicación con Xamarin, Azure AD le facilita la autenticación de sus usuarios con sus cuentas de Active Directory. También permite a la aplicación consumir con seguridad cualquier API web protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para las aplicaciones Xamarin que necesitan tener acceso a recursos protegidos, Azure AD proporciona la biblioteca de autenticación de Active Directory o ADAL. El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo sencillo que es, crearemos a continuación una aplicación de "buscador de directorios" que:

* Se ejecute en iOS, Android, Escritorio de Windows, Windows Phone y Tienda Windows.
* Utilice una biblioteca de clases portables (PCL)  única  para autenticar los usuarios y obtener tokens para Azure AD Graph API.
* Busque un directorio para los usuarios con un UPN determinado.

Para crear la aplicación de trabajo completa, deberá:

1. Configuración de su entorno de desarrollo de Xamarin
2. Registrar la aplicación con Azure AD
3. Instalar y configurar ADAL
4. Usar ADAL para obtener tokens de Azure AD.

Para empezar, [descargue el proyecto del esquema](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Cualquiera de los dos es una solución de Visual Studio 2013. También necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## <a name="0-set-up-your-xamarin-development-environment"></a>0. Configuración de su entorno de desarrollo de Xamarin
Como este tutorial incluye proyectos de iOS, Android y Windows, necesitará tanto Visual Studio como Xamarin. Para crear el entorno necesario, siga las instrucciones completas que se detallan en [Configuración e instalación](https://msdn.microsoft.com/library/mt613162.aspx) en MSDN. Estas instrucciones incluyen material que puede examinar para obtener más información acerca de Xamarin mientras espera a que finalicen los instaladores. 

Una vez que haya completado la configuración necesaria, abra la solución en Visual Studio para comenzar. Encontrará seis proyectos: cinco proyectos específicos de la plataforma y una biblioteca de clases portable que se compartirá entre todas las plataformas, `DirectorySearcher.cs`.

## <a name="1-register-the-directory-searcher-application"></a>1. Registro de la aplicación de buscador de directorios
Para habilitar la aplicación para obtener tokens, primero deberá registrarla en su inquilino de Azure AD y concederle permiso de acceso a la API de gráficos de Azure AD:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra superior, haga clic en su cuenta y, en la lista **Directorio**, elija el inquilino de Active Directory en el que desee registrar la aplicación.
3. Haga clic en **Más servicios** en el panel de navegación izquierdo y elija **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y elija **Agregar**.
5. Siga las indicaciones y cree una nueva **Aplicación de cliente nativa**.
  * El **nombre** de la aplicación servirá de descripción de la aplicación para los usuarios finales.
  * El **URI de redirección** es una combinación de esquema y cadena que utilizará Azure AD para devolver las respuestas de los tokens. Escriba un valor, por ejemplo `http://DirectorySearcher`.
6. Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de aplicación único. Necesitará este valor en las secciones siguientes, así que cópielo de la pestaña de la aplicación.
7. En la página **Configuración**, elija **Permisos necesarios** y luego **Agregar**. Seleccione **Microsoft Graph** como API y agregue el permiso **Leer datos de directorio** en **Permisos delegados**.  Esto permitirá a su aplicación consultar la API Graph para los usuarios.

## <a name="2-install--configure-adal"></a>2. Instalar y configurar ADAL
Ahora que tiene una aplicación en Azure AD, puede instalar ADAL y escribir el código relacionado con la identidad. Para que ADAL pueda comunicarse con Azure AD, hay que proporcionarle información sobre el registro de la aplicación.

* Comience agregando ADAL a cada uno de los proyectos de la solución mediante la Consola del Administrador de paquetes.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

* Debe observar que se agregan dos referencias de biblioteca a cada proyecto: la parte PCL de ADAL y una parte específica de la plataforma.
* En el proyecto DirectorySearcherLib, abra `DirectorySearcher.cs`. Cambie los valores del miembro de clase para que reflejen los valores especificados en el Portal de Azure. El código hará referencia a estos valores siempre que use ADAL.
  
  * `tenant` es el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
  * `clientId` es el identificador de cliente de la aplicación que copió del portal.
  * `returnUri` es el valor redirectUri que especificó en el portal, por ejemplo, `http://DirectorySearcher`.

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Usar ADAL para obtener tokens de AAD
*Casi* toda la lógica de autenticación de la aplicación se encuentra en `DirectorySearcher.SearchByAlias(...)`. Todo lo necesario en los proyectos específicos de la plataforma es pasar un parámetro contextual al PCL `DirectorySearcher` .

* En primer lugar, abra `DirectorySearcher.cs` y agregue un nuevo parámetro al método `SearchByAlias(...)`. `IPlatformParameters` es el parámetro contextuales que encapsula los objetos específicos de la plataforma que ADAL necesita para realizar la autenticación.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

* A continuación, inicialice `AuthenticationContext` : la clase principal de ADAL. Este es el lugar en el que pasa a ADAL las coordenadas que necesita para comunicarse con Azure AD. A continuación, llame a `AcquireTokenAsync(...)`, que acepta el objeto `IPlatformParameters` y que invocará el flujo de autenticación necesario para devolver un token a la aplicación.

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
* `AcquireTokenAsync(...)` primero intentará devolver un token al recurso solicitado (Graph API en este caso) sin solicitar al usuario que escriba sus credenciales (mediante el almacenamiento en caché o la actualización de tokens antiguos). Solo si es necesario, mostrará al usuario la página de inicio de sesión de Azure AD antes de adquirir el token solicitado.
* También puede adjuntar el token de acceso a la solicitud de Graph API en el encabezado de autorización:

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Eso es todo para el PCL `DirectorySearcher` y el código relacionado con la identidad de la aplicación.  Todo lo que queda es llamar al método `SearchByAlias(...)` en cada vista de plataforma, y cuando se necesario, agregar código para la administración correcta del ciclo de vida de la interfaz de usuario.

#### <a name="android"></a>Android:
* En `MainActivity.cs`, agregue una llamada a `SearchByAlias(...)` en el administrador de clics de botones:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
* También necesitará reemplazar el método de ciclo de vida `OnActivityResult` para volver a enviar redirecciones de autenticación al método apropiado.  ADAL proporciona un método auxiliar para esto en Android:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

#### <a name="windows-desktop"></a>Escritorio de Windows:
* En `MainWindow.xaml.cs`, simplemente realice una llamada a `SearchByAlias(...)` pasando un `WindowInteropHelper` en el objeto `PlatformParameters` de escritorio:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS:
* En `DirSearchClient_iOSViewController.cs`, el objeto `PlatformParameters` de iOS simplemente toma una referencia para el Controlador de vista:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="windows-universal"></a>Windows Universal:
* En Windows Universal, abra `MainPage.xaml.cs` e implemente el método `Search` , que utiliza un método auxiliar en un proyecto compartido para actualizar la interfaz de usuario según sea necesario.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

¡Enhorabuena! Ahora dispone de una aplicación de Xamarin en funcionamiento que tiene la capacidad de autenticar usuarios y llamar de forma segura a las API de Web mediante OAuth 2.0 en cinco plataformas distintas. Si no lo ha hecho ya, ahora es el momento de completar el inquilino con algunos usuarios. Ejecute la aplicación DirectorySearcher e inicie sesión con uno de esos usuarios. Busque otros usuarios según su UPN.

ADAL facilita la incorporación de características comunes de identidad en la aplicación. Hace el trabajo sucio por usted: administración en caché, compatibilidad con protocolo OAuth, presentación del usuario con una interfaz de usuario de inicio de sesión, actualización de tokens expirados, etc. Todo lo que necesita saber es una única llamada de API, `authContext.AcquireToken*(…)`.

Como referencia, se proporciona el ejemplo finalizado (sin sus valores de configuración) [aquí](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Ahora puede trasladarse a escenarios de identidad adicionales. Es posible que desee probar:

[Protección de una API Web .NET con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]




<!--HONumber=Jan17_HO3-->


