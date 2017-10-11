---
title: "Introducción a .NET de Azure AD | Microsoft Docs"
description: "Creación de una aplicación de escritorio .NET de Windows que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
services: active-directory
documentationcenter: .net
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7a252e0e5243c7b7489373845531cb913ca1f6aa
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Integración de Azure AD en una aplicación WPF de escritorio de Windows
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Si está desarrollando una aplicación de escritorio, Azure AD le facilita la autenticación de sus usuarios mediante sus cuentas de Active Directory.  También permite a la aplicación consumir con seguridad cualquier API web protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para los clientes nativos .NET que necesitan tener acceso a recursos protegidos, Azure AD proporciona la Biblioteca de autenticación de Active Directory (ADAL).  El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso.  Para demostrar lo fácil que es, crearemos aquí una aplicación de lista de tareas pendientes (ToDo) de .NET WPF que permita realizar las siguientes acciones:

* Obtener tokens de acceso para llamar a la API Graph de Azure AD utilizando el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)
* Buscar un directorio para los usuarios con un alias determinado
* Cerrar la sesión de los usuarios

Para crear la aplicación de trabajo completa, deberá realizar estas acciones:

1. Registrar la aplicación con Azure AD
2. Instalar y configurar ADAL
3. Usar ADAL para obtener tokens de Azure AD.

Para empezar, [descargue el esquema de la aplicación](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  También necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación.  Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. Registro de la aplicación DirectorySearcher
Para habilitar la aplicación para obtener tokens, primero deberá registrarla en su inquilino de Azure AD y concederle permiso de acceso a la API Graph de Azure AD:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra superior, haga clic en su cuenta y, en la lista **Directorio**, elija el inquilino de Active Directory en el que desee registrar la aplicación.
3. Haga clic en **Más servicios** en el panel de navegación izquierdo y elija **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y elija **Agregar**.
5. Siga las indicaciones y cree una nueva **Aplicación de cliente nativa**.
  * El **nombre** de la aplicación servirá de descripción de la aplicación para los usuarios finales.
  * El **URI de redirección** es una combinación de esquema y cadena que utilizará Azure AD para devolver las respuestas de los tokens.  Escriba un valor específico para la aplicación, por ejemplo, `http://DirectorySearcher`.
6. Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de aplicación único.  Necesitará este valor en las secciones siguientes, así que cópielo de la página de la aplicación.
7. En la página **Configuración**, elija **Permisos necesarios** y luego **Agregar**. Seleccione **Microsoft Graph** como API y agregue el permiso **Leer datos de directorio** en **Permisos delegados**.  Esto permitirá a su aplicación consultar la API Graph para los usuarios.

## <a name="2-install--configure-adal"></a>2. Instalar y configurar ADAL
Ahora que tiene una aplicación en Azure AD, puede instalar ADAL y escribir el código relacionado con la identidad.  Para que ADAL pueda comunicarse con Azure AD, hay que proporcionarle información sobre el registro de la aplicación.

* Comience agregando ADAL al proyecto DirectorySearcher con la Consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* En el proyecto de buscador de directorios, abra `app.config`.  Reemplace los valores de los elementos de la sesión `<appSettings>` para que reflejen los valores especificados en el Portal de Azure.  El código hará referencia a estos valores siempre que use ADAL.
  * `ida:Tenant` es el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
  * `ida:ClientId` es el identificador de cliente de la aplicación que copió del portal.
  * El `ida:RedirectUri` es la URL de redirección que ha registrado en el portal.

## <a name="3----use-adal-to-get-tokens-from-aad"></a>3.    Usar ADAL para obtener tokens de AAD
El principio básico inherente a ADAL es que cada vez que la aplicación necesita un token de acceso, simplemente llama a  `authContext.AcquireTokenAsync(...)` y ADAL se encarga del resto.  

* En el proyecto `DirectorySearcher`, abra `MainWindow.xaml.cs` y busque el método `MainWindow()`.  El primer paso consiste en inicializar el `AuthenticationContext` de la aplicación, que es la clase principal de ADAL.  Este es el lugar en el que se pasan a ADAL las coordenadas necesarias para comunicarse con Azure AD e indicarle cómo almacenar en caché los tokens.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Ahora busque el método `Search(...)` , que se invoca cuando el usuario hace clic en el botón de búsqueda en la interfaz de usuario de la aplicación.  Este método realiza una solicitud GET a la API Graph de Azure AD para realizar una consulta sobre los usuarios cuyo UPN comienza con el término de búsqueda especificado.  Sin embargo, para realizar una consulta a la API Graph, tiene que incluir un access_token en el encabezado `Authorization` de la solicitud, que es donde entra ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
* Cuando la aplicación solicita un token mediante una llamada a `AcquireTokenAsync(...)`, ADAL intentará devolver un token sin solicitar al usuario las credenciales.  Si ADAL determina que el usuario debe iniciar sesión para obtener un token, mostrará un cuadro de diálogo de inicio de sesión, recopilará las credenciales del usuario y devolverá un token tras una autenticación correcta.  Si ADAL no puede devolver un token por alguna razón, mostrará una `AdalException`.
* Observe que el objeto `AuthenticationResult` contiene un objeto `UserInfo` que puede usarse para recopilar información puede necesitar la aplicación.  En el DirectorySearcher, se usa `UserInfo` para personalizar la interfaz de usuario de la aplicación con el identificador del usuario.
* Cuando el usuario hace clic en el botón "Cerrar sesión", queremos asegurarnos de que la próxima llamada a `AcquireTokenAsync(...)` solicite al usuario que inicie sesión.  Con ADAL, esto es tan fácil como borrar la caché de tokens:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* Sin embargo, si el usuario no hace clic en el botón "Cerrar sesión", puede que desee mantener la sesión del usuario para la próxima vez que ejecute DirectorySearcher.  Cuando se inicie la aplicación, puede comprobar la caché de tokens de ADAL para un token existente y actualizar la interfaz de usuario en consecuencia.  En el método `CheckForCachedToken()`, realice otra llamada a `AcquireTokenAsync(...)`, pero esta vez pasando en el parámetro `PromptBehavior.Never`.  `PromptBehavior.Never` indicará a ADAL que no se debe solicitar al usuario que inicie sesión. En lugar de ello, ADAL debe iniciar una excepción si no puede devolver un token.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

¡Enhorabuena! Ahora tiene una aplicación de WPF de .NET operativa que tiene la capacidad de autenticar usuarios, realizar llamadas seguras a las API web que usan OAuth 2.0 y obtener información básica sobre el usuario.  Si todavía no lo ha hecho, ahora es el momento de completar el inquilino con algunos usuarios.  Ejecute la aplicación DirectorySearcher e inicie sesión con uno de esos usuarios.  Busque otros usuarios según su UPN.  Cierre la aplicación y vuelva a ejecutarla.  Observe cómo la sesión del usuario permanece intacta.  Cierre la sesión y vuelva a iniciarla como otro usuario.

ADAL facilita la incorporación de todas estas características comunes de identidad en la aplicación.  Hace el trabajo sucio por usted: administración en caché, compatibilidad con protocolo OAuth, presentación del usuario con una interfaz de usuario de inicio de sesión, actualización de tokens expirados, etc.  Todo lo que necesita saber es una única llamada de API, `authContext.AcquireTokenAsync(...)`.

Como referencia, [aquí](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)puede ver el ejemplo finalizado (sin sus valores de configuración).  Ahora puede pasar a otros escenarios.  Es posible que desee probar:

[Protección de una API Web .NET con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

