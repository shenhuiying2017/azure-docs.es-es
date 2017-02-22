---
title: "Introducción a Tienda Windows de Azure AD | Microsoft Docs"
description: "Creación de una aplicación de Tienda Windows que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
services: active-directory
documentationcenter: windows
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: f1757379bdb8e04d3302dbd8a4528a1b8ed00f65


---
# <a name="integrate-azure-ad-with-a-windows-store-app"></a>Integración de Azure AD con una aplicación de la Tienda Windows
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Si está desarrollando una aplicación de Tienda Windows, Azure AD le facilita la autenticación de sus usuarios con sus cuentas de Active Directory.  También permite a la aplicación consumir con seguridad cualquier API web protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para las aplicaciones de escritorio de Tienda Windows que necesitan tener acceso a recursos protegidos, Azure AD proporciona la biblioteca de autenticación de Active Directory o ADAL.  El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso.  Para demostrar lo sencillo que es, crearemos a continuación una aplicación de Tienda Windows "buscador de directorios" que:

* Obtenga tokens de acceso para llamar a la API de gráficos de Azure AD utilizando el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Busque un directorio para los usuarios con un UPN determinado.
* Cerrar la sesión de los usuarios

Para crear la aplicación de trabajo completa, deberá:

1. Registrar la aplicación con Azure AD
2. Instalar y configurar ADAL
3. Usar ADAL para obtener tokens de Azure AD.

Para empezar, [descargue el proyecto del esquema](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  Cualquiera de los dos son una solución de Visual Studio 2015.  También necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación.  Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>1. Registro de la aplicación de buscador de directorios
Para habilitar la aplicación para obtener tokens, primero deberá registrarla en su inquilino de Azure AD y concederle permiso de acceso a la API de gráficos de Azure AD:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra superior, haga clic en su cuenta y, en la lista **Directorio**, elija el inquilino de Active Directory en el que desee registrar la aplicación.
3. Haga clic en **Más servicios** en el panel de navegación izquierdo y elija **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y elija **Agregar**.
5. Siga las indicaciones y cree una nueva **Aplicación de cliente nativa**.
  * El **nombre** de la aplicación servirá de descripción de la aplicación para los usuarios finales.
  * El **URI de redirección** es una combinación de esquema y cadena que utilizará Azure AD para devolver las respuestas de los tokens.  Escriba un valor de marcador de posición por ahora, por ejemplo, `http://DirectorySearcher`.  Reemplazaremos este valor más adelante.
6. Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de aplicación único.  Necesitará este valor en las secciones siguientes, así que cópielo de la pestaña de la aplicación.
7. En la página **Configuración**, elija **Permisos necesarios** y luego **Agregar**.  Para la aplicación "Azure Active Directory", seleccione **Microsoft Graph** como API y agregue el permiso **Acceder al directorio como usuario con sesión iniciada** en **Permisos delegados**.  Esto permitirá a su aplicación consultar la API Graph para los usuarios.

## <a name="2-install--configure-adal"></a>2. Instalar y configurar ADAL
Ahora que tiene una aplicación en Azure AD, puede instalar ADAL y escribir el código relacionado con la identidad.  Para que ADAL pueda comunicarse con Azure AD, hay que proporcionarle información sobre el registro de la aplicación.

* Comience agregando ADAL al proyecto DirectorySearcher con la Consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* En el proyecto de buscador de directorios, abra `MainPage.xaml.cs`.  Reemplace los valores de la región `Config Values` para que reflejen los valores especificados en el Portal de Azure.  El código hará referencia a estos valores siempre que use ADAL.
  * `tenant` es el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
  * `clientId` es el identificador de cliente de la aplicación que copió del portal.
* Ahora deberá detectar el URI de devolución de llamada para la aplicación de Windows Phone.  Establezca un punto de interrupción en esta línea en el método `MainPage` :

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
* Compile la solución y asegúrese de que se restauran todas las referencias de paquete.  Si faltan paquetes, abra el Administrador de paquetes de Nuget y restaure los paquetes.
* Ejecute la aplicación y copie aparte el valor de `redirectUri` cuando se alcance el punto de interrupción.  Debe tener un aspecto similar al siguiente.

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

* De nuevo en la pestaña **Configuración** de la aplicación en Azure Portal, agregue una propiedad **RedirectUri** con este valor.  

## <a name="3----use-adal-to-get-tokens-from-aad"></a>3.    Usar ADAL para obtener tokens de AAD
El principio básico inherente a ADAL es que cada vez que la aplicación necesita un token de acceso, simplemente llama a  `authContext.AcquireToken(…)` y ADAL se encarga del resto.  

* El primer paso consiste en inicializar `AuthenticationContext` de la aplicación: clase principal de ADAL.  Este es el lugar en el que pasa a ADAL las coordenadas que necesita para comunicarse con Azure AD e indicarle cómo almacenar en caché los tokens.

```C#
public MainPage()
{
    ...

    authContext = new AuthenticationContext(authority);
}
```

* Ahora busque el método `Search(...)` , que se invoca cuando el usuario hace clic en el botón de búsqueda de la interfaz de usuario de la aplicación.  Este método realiza una solicitud GET a la API Graph de Azure AD para realizar una consulta sobre los usuarios cuyo UPN comienza con el término de búsqueda especificado.  Sin embargo, para realizar una consulta a la API Graph, tiene que incluir un access_token en el encabezado `Authorization` de la solicitud, que es donde entra ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
        }
        return;
    }
    ...
}
```
* Cuando la aplicación solicita un token mediante una llamada a `AcquireTokenAsync(...)`, ADAL intentará devolver un token sin solicitar al usuario las credenciales.  Si ADAL determina que el usuario debe iniciar sesión para obtener un token, mostrará un cuadro de diálogo de inicio de sesión, recopilará las credenciales del usuario y devolverá un token tras una autenticación correcta.  Si ADAL no puede devolver un token por alguna razón, el estado `AuthenticationResult` será un error.
* Ahora es el momento de usar el access_token que acaba de adquirir.  También en el método `Search(...)`, adjunte el token a la solicitud GET de Graph API en el encabezado de autorización:

```C#
// Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

```
* También puede utilizar e objeto `AuthenticationResult` para mostrar información sobre el usuario en la aplicación, como el identificador de usuario:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
* Por último, también puede usar ADAL para cerrar la sesión del usuario en la aplicación.  Cuando el usuario hace clic en el botón "Cerrar sesión", queremos asegurarnos de que la próxima llamada a `AcquireTokenAsync(...)` muestre una vista de inicio de sesión.  Con ADAL, esto es tan fácil como borrar la caché de tokens:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

¡Enhorabuena! Ahora tiene una aplicación de Tienda Windows en funcionamiento que tiene la capacidad de autenticar usuarios, realizar llamadas seguras a las API Web que usan OAuth 2.0 e y obtener información básica sobre el usuario.  Si no lo ha hecho ya, ahora es el momento de completar el inquilino con algunos usuarios.  Ejecute la aplicación DirectorySearcher e inicie sesión con uno de esos usuarios.  Busque otros usuarios según su UPN.  Cierre la aplicación y vuelva a ejecutarla.  Observe cómo la sesión del usuario permanece intacta.  Cierre la sesión (haciendo clic con el botón derecho) y vuelva a iniciarla como otro usuario.

ADAL facilita la incorporación de todas estas características comunes de identidad en la aplicación.  Hace el trabajo sucio por usted: administración en caché, compatibilidad con protocolo OAuth, presentación del usuario con una interfaz de usuario de inicio de sesión, actualización de tokens expirados, etc.  Todo lo que necesita saber es una única llamada de API, `authContext.AcquireToken*(…)`.

Como referencia, se proporciona el ejemplo finalizado (sin sus valores de configuración) [aquí](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  Ahora puede trasladarse a escenarios de identidad adicionales.  Es posible que desee probar:

[Protección de una API Web .NET con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]




<!--HONumber=Jan17_HO3-->


