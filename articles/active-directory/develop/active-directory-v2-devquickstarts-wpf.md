---
title: "Aplicación nativa .NET v2.0 de Azure Active Directory | Microsoft Azure"
description: "Cómo crear una aplicación nativa .NET con la que los usuarios pueden iniciar sesión utilizando tanto la cuenta personal de Microsoft como sus cuentas profesionales o educativas."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7389f55ee6fef9548abb0ca4ac1bbd0399868d47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>Agregar inicio de sesión a una aplicación de escritorio de Windows
Con el punto de conexión v2.0 puede agregar rápidamente la autenticación a sus aplicaciones de escritorio compatibles tanto con las cuentas personales de Microsoft como con las cuentas profesionales o educativas.  También permite que la aplicación se comunique de forma segura con una API web back-end, así como con [Microsoft Graph](https://graph.microsoft.io) y algunas de las [API unificadas de Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [!NOTE]
> No todas las características y escenarios de Azure Active Directory (AD) son compatibles con el punto de conexión v2.0.  Para determinar si debe usar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).
> 
> 

Para las [aplicaciones nativas .NET que se ejecutan en un dispositivo](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD proporciona la biblioteca de autenticación de identidades de Microsoft o MSAL.  El único propósito de MSAL es facilitar a su aplicación la obtención de tokens de acceso para llamar a servicios web.  Para demostrar lo fácil que es, crearemos aquí una aplicación de lista de tareas pendientes de .NET WPF que permita realizar las siguientes acciones:

* Iniciar la sesión del usuario y obtener token de acceso mediante el [protocolo de autenticación de OAuth 2.0](active-directory-v2-protocols.md).
* Llama a un servicio web de lista de tareas pendientes back-end, que también está protegido con OAuth 2.0.
* Cierra la sesión del usuario.

## <a name="download-sample-code"></a>Descarga de código de ejemplo
El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Para continuar, puede [descargar el esqueleto de la aplicación como un archivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) o clonar el esqueleto:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

La aplicación completa se ofrece también al final de este tutorial.

## <a name="register-an-app"></a>Registrar una aplicación
Cree una nueva aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o siga estos [pasos detallados](active-directory-v2-app-registration.md).  Asegúrese de que:

* Anotar el **Id. de aplicación** asignado a su aplicación; lo necesitará pronto.
* Agregar la plataforma **Móvil** a la aplicación.

## <a name="install--configure-msal"></a>Instalación y configuración de MSAL
Ahora que ya tiene una aplicación registrada en Microsoft, puede instalar MSAL y escribir código relacionado con identidades.  Para que MSAL pueda comunicarse con el punto de conexión v2.0, tiene que proporcionarle información sobre el registro de la aplicación.

* Comience agregando MSAL al proyecto TodoListClient con la Consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* En el proyecto TodoListClient, abra `app.config`.  Reemplace los valores de los elementos de la sección `<appSettings>` para que reflejen los valores especificados en el portal de registro de aplicaciones.  El código hará referencia a estos valores cada vez que use MSAL.
  
  * `ida:ClientId` es el **Id. de aplicación** de su aplicación que copió del portal.
* En el proyecto de servicio de TodoList, abra `web.config` en la raíz del proyecto.  
  
  * Reemplace el valor `ida:Audience` con el mismo **Id. de aplicación** desde el portal.

## <a name="use-msal-to-get-tokens"></a>Uso de MSAL para obtener tokens
El principio básico inherente a MSAL es que cada vez que la aplicación necesite un token de acceso, basta con llamar a `app.AcquireToken(...)`y MSAL se encarga del resto.  

* En el proyecto `TodoListClient`, abra `MainWindow.xaml.cs` y busque el método `OnInitialized(...)`.  El primer paso consiste en inicializar la clase principal de MSAL de `PublicClientApplication` de la aplicación que representa las aplicaciones nativas.  Este es el lugar en el que pasa a MSAL las coordenadas que necesita para comunicarse con Azure AD e indicarle cómo almacenar en caché los tokens.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* Cuando se inicia la aplicación, queremos comprobar y asegurarnos de si el usuario ya está registrado en la aplicación.  Sin embargo, no deseamos invocar una IU de inicio de sesión todavía, sino que haremos que el usuario haga clic en "Iniciar sesión" para hacerlo.  En el método `OnInitialized(...)` también ocurre lo siguiente:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* Si el usuario no ha iniciado sesión y hace clic en el botón "Iniciar sesión", deseamos invocar una IU de inicio de sesión y que el usuario escriba sus credenciales.  Implementar el controlador del botón Inicio de sesión:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

* Si el usuario inicia sesión correctamente, MSAL recibirá y almacenará en caché un token por usted y podrá proceder con la llamada al método `GetTodoList()` con confianza.  Lo único que queda para obtener las tareas del usuario es implementar el método `GetTodoList()` .

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Ejecute
¡Enhorabuena! Ya tiene una aplicación .NET WPF en funcionamiento con la capacidad de autenticar a usuarios y API web de llamadas de forma segura mediante OAuth 2.0.  Ejecute sus dos proyectos e inicie sesión con una cuenta de Microsoft personal o una cuenta profesional o educativa.  Agregue tareas a la lista de tareas pendientes de ese usuario.    Cierre sesión e iníciela de nuevo como otro usuario para ver su lista de tareas pendientes.  Cierre la aplicación y vuelva a ejecutarla.  Observe que la sesión del usuario permanece intacta. Esto se debe a que la aplicación captura tokens de un archivo local.

MSAL facilita la incorporación de las características de identidades comunes a la aplicación, tanto mediante las cuentas personales como las profesionales.  Hace el trabajo sucio por usted: administración en caché, compatibilidad con protocolo OAuth, presentación del usuario con una interfaz de usuario de inicio de sesión, actualización de tokens expirados, etc.  Todo lo que necesita saber es una única llamada de API, `app.AcquireTokenAsync(...)`.

Como referencia, el ejemplo finalizado (sin sus valores de configuración) [se proporciona en forma de archivo .zip aquí](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), aunque también puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Pasos siguientes
Ahora puede pasar a temas más avanzados.  Es posible que desee probar:

* [Proteger la API web TodoListService con el punto de conexión v2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Para obtener recursos adicionales, consulte:  

* [La guía del desarrollador de v2.0 &gt;&gt;](active-directory-appmodel-v2-overview.md)
* [Etiqueta "msal" de StackOverflow &gt;&gt;](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Obtención de actualizaciones de seguridad para nuestros productos
Le animamos a que obtenga notificaciones de los incidentes de seguridad que se produzcan; para ello, visite [esta página](https://technet.microsoft.com/security/dd252948) y suscríbase a las alertas de avisos de seguridad.

