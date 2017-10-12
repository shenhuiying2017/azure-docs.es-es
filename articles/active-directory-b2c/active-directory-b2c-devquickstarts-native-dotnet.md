---
title: Azure Active Directory B2C | Microsoft Docs
description: "Creación de una aplicación de escritorio de Windows que incluye tareas de registro, inicio de sesión y administración de perfiles mediante Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 9da14362-8216-4485-960e-af17cd5ba3bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.openlocfilehash: 8e2b5c704230ee2ba1395dc76a1551aaa8e7af7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C: creación de una aplicación de escritorio de Windows
Con Azure Active Directory (Azure AD) B2C, puede agregar eficaces características de administración de identidades autoservicio a sus aplicaciones de escritorio en pocos pasos. En este artículo se le mostrará cómo crear una aplicación de "lista de tareas pendientes" de Windows Presentation Foundation (WPF) para .NET que incluya tareas de registro, inicio de sesión y administración de perfiles de usuarios. La aplicación permitirá registrarse e iniciar sesión mediante un nombre de usuario o un correo electrónico. También será posible registrarse e iniciar sesión con cuentas sociales, como Facebook y Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C
Para poder usar Azure AD B2C, debe crear un directorio o inquilino.  Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar con esta guía.

## <a name="create-an-application"></a>Creación de una aplicación
A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md).  Asegúrese de:

* Incluir un **cliente nativo** en la aplicación.
* Copie la **URI de redirección** `urn:ietf:wg:oauth:2.0:oob`. Es la dirección URL predeterminada para este ejemplo de código.
* Copiar el **id. de aplicación** asignado a la aplicación. Lo necesitará más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Crear sus directivas
En Azure AD B2C, cada experiencia de usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Este ejemplo de código contiene tres experiencias de identidad: registro, inicio de sesión y edición de perfil. Tendrá que crear una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

* Elegir **User ID sign-up** (Registro con id. de usuario) o **Email sign-up** (Registro con correo electrónico) en la hoja de proveedores de identidades.
* Seleccionar **Nombre para mostrar** y otros atributos de registro en la directiva de registro.
* Elegir las notificaciones **Nombre para mostrar** e **Id. de objeto** como notificaciones de aplicación en todas las directivas. Puede elegir también otras notificaciones.
* Copiar el **nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`.  Necesitará estos nombres de directiva más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Cuando haya creado correctamente las tres directivas, estará listo para crear su aplicación.

## <a name="download-the-code"></a>Descargar el código
El código de este tutorial [se mantiene en GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Para generar el ejemplo a medida que avanza, puede [descargar un proyecto de esqueleto como archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). También puede clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

La aplicación completada también estará [disponible como archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Una vez descargado el código de ejemplo, abra el archivo .sln de Visual Studio para empezar. El proyecto `TaskClient` es una aplicación web MVC con la que el usuario interactúa. Para los fines de este tutorial, llama a una tarea de back-end de API web, hospedada en Azure, que almacena la lista de tareas pendientes de cada usuario.  No es necesario crear la API web, ya que se ejecuta automáticamente.

Para saber de qué forma una API web autentica de forma segura las solicitudes con Azure AD B2C, consulte nuestro [artículo de introducción a la API web](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Ejecución de directivas
Su aplicación se comunica con Azure AD B2C mediante el envío de solicitudes de autenticación HTTP que especifican la directiva que desea ejecutar como parte de la solicitud. Para aplicaciones de escritorio .NET, puede usar la versión preliminar de la Biblioteca de autenticación de Microsoft (MSAL) para enviar mensajes de autenticación de OAuth 2.0, ejecutar directivas y obtener tokens para llamar a las API web.

### <a name="install-msal"></a>Instalación de MSAL
Agregue MSAL al proyecto `TaskClient` mediante la Consola del Administrador de paquetes de Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Escribir los detalles de B2C
Abra el archivo `Globals.cs` y reemplace cada uno de los valores de propiedad por los suyos propios. Esta clase se usa en todo `TaskClient` para hacer referencia a valores de uso frecuente.

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>Creación de PublicClientApplication
La clase principal de MSAL es `PublicClientApplication`. Esta clase representa la aplicación en el sistema de Azure AD B2C. Cuando se inicializa la aplicación, cree una instancia de `PublicClientApplication` en `MainWindow.xaml.cs`. Esta puede usarse en toda la ventana.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Inicio de un flujo de registro
Cuando un usuario decide registrarse, quiere iniciar un flujo de registro que usa la directiva de registro que ha creado. Con MSAL, simplemente llama a `pca.AcquireTokenAsync(...)`. Los parámetros que pasa a `AcquireTokenAsync(...)` determinan el token que recibe, la directiva usada en la solicitud de autenticación, etc.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>Inicio de un flujo de inicio de sesión
Puede iniciar un flujo de inicio de sesión de la misma manera que inicia un flujo de registro. Cuando un usuario inicia sesión, realiza la misma llamada a MSAL, pero esta vez usando la directiva de inicio de sesión:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Inicio de un flujo de edición de perfiles
De nuevo, puede ejecutar una directiva de edición de perfiles de la misma manera:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

En todos estos casos, MSAL devuelve un token en `AuthenticationResult` o genera una excepción. Cada vez que reciba un token de MSAL, puede usar el objeto `AuthenticationResult.User` para actualizar los datos de usuario en la aplicación, como la interfaz de usuario. ADAL también almacena en caché el token para usarlo en otras partes de la aplicación.

### <a name="check-for-tokens-on-app-start"></a>Búsqueda de tokens en el inicio de la aplicación
También puede utilizar MSAL para realizar el seguimiento del estado de inicio de sesión del usuario.  En esta aplicación, queremos que el usuario continúe con la sesión, incluso después de haber cerrado la aplicación y de volver a abrirla.  De vuelta a la invalidación de `OnInitialized`, utilice el método `AcquireTokenSilent` de MSAL para buscar tokens en caché:

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>Llamada a la API de tarea
Ya hemos usado MSAL para ejecutar directivas y obtener tokens.  Si desea usar uno estos tokens para llamar a la API de la tarea, puede usar nuevamente el `AcquireTokenSilent` método de MSAL para buscar tokens en caché:

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

Cuando la llamada a `AcquireTokenSilentAsync(...)` se realiza correctamente y hay un token en la memoria caché, puede agregarlo al encabezado `Authorization` de la solicitud HTTP. La API web de la tarea usará este encabezado para autenticar la solicitud para leer la lista de tareas pendientes del usuario:

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Cerrar la sesión del usuario
Finalmente, puede usar MSAL para finalizar la sesión de un usuario en la aplicación cuando el usuario selecciona **Cerrar sesión**.  Con MSAL, esto se logra borrando todos los tokens de la caché de tokens:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Ejecutar la aplicación de ejemplo
Finalmente, compile y ejecute el ejemplo.  Regístrese en la aplicación con una dirección de correo electrónico o un nombre de usuario. Cierre la sesión y vuelva a iniciarla como el mismo usuario. Edite el perfil de ese usuario. Cierre la sesión y regístrese con otro usuario diferente.

## <a name="add-social-idps"></a>Agregar IDP sociales
Actualmente, la aplicación solo admite registros e inicios de sesión de usuarios que usan **cuentas locales**. Se trata de cuentas almacenadas en el directorio B2C que utilizan un nombre de usuario y una contraseña. Con Azure AD B2C, puede agregar compatibilidad con otros proveedores de identidades (IDP) sin cambiar el código.

Para agregar proveedores de identidades sociales a su aplicación, comience siguiendo las instrucciones detalladas en estos artículos. Para cada proveedor de identidades que desee admitir, necesitará registrar una aplicación en ese sistema y obtener un identificador de cliente.

* [Configurar Facebook como una IDP](active-directory-b2c-setup-fb-app.md)
* [Configurar Google como una IDP](active-directory-b2c-setup-goog-app.md)
* [Configurar Amazon como una IDP](active-directory-b2c-setup-amzn-app.md)
* [Configurar LinkedIn como una IDP](active-directory-b2c-setup-li-app.md)

Después de agregar los proveedores de identidades a su directorio B2C, tendrá que editar cada una de las tres directivas para incluir los nuevos proveedores de identidades, tal y como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md). Después de guardar las directivas, vuelva a ejecutar la aplicación. Debería ver los proveedores de identidades nuevos agregados como opciones de inicio de sesión y registro en cada una de sus experiencias de identidad.

Puede experimentar con las directivas y observar los efectos en la aplicación de ejemplo. Agregue o quite proveedores de identidades, manipule notificaciones de aplicación o cambie atributos de registro. Experimente para ver cómo se combinan directivas, solicitudes de autenticación y MSAL.

Como referencia, el ejemplo completo [se proporciona como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). También puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
