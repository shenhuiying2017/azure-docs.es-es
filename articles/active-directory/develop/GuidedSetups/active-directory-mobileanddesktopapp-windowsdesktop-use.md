---
title: "Introducción al escritorio de Windows en Azure AD v2: uso | Microsoft Docs"
description: "Cómo pueden llamar las aplicaciones .NET de escritorio de Windows (XAML) a una API que requiera tokens de acceso mediante el punto de conexión de Azure Active Directory v2"
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
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 826ba0a00b26993d4f37f0a8ce587d7bb77e7eb4
ms.contentlocale: es-es

---

## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utilice la biblioteca de autenticación de Microsoft (MSAL) para obtener un token para la API Graph de Microsoft.

En esta sección se muestra cómo usar MSAL para obtener un token de API Graph de Microsoft.

1.  En `MainWindow.xaml.cs`, agregue la referencia de la biblioteca MSAL a la clase:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Reemplace el código de clase <code>MainWindow</code> por:
</li>
</ol>

```csharp
public partial class MainWindow : Window
{
    //Set the API Endpoint to Graph 'me' endpoint
    string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

    //Set the scope for API call to user.read
    string[] _scopes = new string[] { "user.read" };


    public MainWindow()
    {
        InitializeComponent();
    }

    /// <summary>
    /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
    /// </summary>
    private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
    {
        AuthenticationResult authResult = null;

        try
        {
            authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
        }
        catch (MsalUiRequiredException ex)
        {
            // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
            System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
            }
            catch (MsalException msalex)
            {
                ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
            }
        }
        catch (Exception ex)
        {
            ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
            return;
        }

        if (authResult != null)
        {
            ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Más información
#### <a name="getting-a-user-token-interactive"></a>Obtención de un token de usuario interactivamente
La llamada a `AcquireTokenAsync` genera una ventana que pide al usuario que inicie sesión. Las aplicaciones suelen requerir que un usuario inicie sesión interactivamente la primera vez que requieren acceso a un recurso protegido, o cuando se produce un error en una operación silenciosa para adquirir un token (por ejemplo, si la contraseña de usuario caducó).

#### <a name="getting-a-user-token-silently"></a>Obtención de un token de usuario en silencio
`AcquireTokenSilentAsync` controla las adquisiciones y la renovación de tokens sin interacción del usuario. Después de que `AcquireTokenAsync` se ejecuta por primera vez, `AcquireTokenSilentAsync` es el método habitual que se utiliza para obtener los tokens empleados para tener acceso a recursos protegidos en las llamadas posteriores, ya que las llamadas para solicitar o renovar los tokens se realizan en modo silencioso.
Es posible que `AcquireTokenSilentAsync` genere eventualmente un error; por ejemplo, si el usuario ha cerrado la sesión o ha cambiado su contraseña en otro dispositivo. Si MSAL detecta que el problema puede solucionarse requiriendo una acción interactiva, desencadena una acción `MsalUiRequiredException`. La aplicación puede abordar esta excepción de dos maneras:

1.  Realizar una llamada a `AcquireTokenAsync` inmediatamente, lo que ocasiona que el usuario tenga que iniciar sesión. Este patrón se da normalmente en aplicaciones en línea en las que no hay ningún contenido sin conexión en la aplicación disponible para el usuario. El ejemplo generado en esta instalación guiada sigue este modelo: puede verlo en acción la primera vez que ejecute el ejemplo; dado que la aplicación no se ha utilizado nunca por ningún usuario, `PublicClientApp.Users.FirstOrDefault()` contendrá un valor nulo y se generará una excepción de tipo `MsalUiRequiredException`. El código en el ejemplo entonces trata la excepción mediante una llamada a `AcquireTokenAsync`, lo que resulta en una petición al usuario de que inicie sesión.

2.  Las aplicaciones también pueden hacer una indicación visual al usuario de que se requiere un inicio de sesión interactivo, de manera que el usuario pueda seleccionar el momento adecuado para iniciar sesión, o la aplicación puede reintentar `AcquireTokenSilentAsync` en un momento posterior. Esto se utiliza normalmente cuando el usuario puede utilizar otras funciones de la aplicación sin que se le interrumpa: por ejemplo, hay contenido sin conexión disponible en la aplicación. En este caso, el usuario puede decidir el momento en que desea iniciar sesión para tener acceso al recurso protegido, o actualizar la información no actualizada; o puede decidir que la aplicación vuelva a intentar `AcquireTokenSilentAsync` cuando se restaure la red después de estar no disponible temporalmente.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Llamada a la API Graph de Microsoft con el token que acaba de obtener

1. Agregue el siguiente método nuevo a su `MainWindow.xaml.cs`. El método se utiliza para realizar una solicitud de `GET` a API Graph con un encabezado de autorización:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```
<!--start-collapse-->
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Más información acerca de cómo realizar una llamada de REST a una API protegida

En esta aplicación de ejemplo, el método `GetHttpContentWithToken` se usa para realizar una solicitud HTTP `GET` a un recurso protegido que requiere un token y, a continuación, devolver el contenido al autor de la llamada. Este método agrega el token adquirido al *encabezado de autorización HTTP*. En este ejemplo, el recurso es el punto de conexión *me* de la API Graph de Microsoft, que muestra información de perfil del usuario.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adición de un método para cerrar la sesión del usuario

1. Agregue el método siguiente a su `MainWindow.xaml.cs` para cerrar la sesión del usuario:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Más información sobre el cierre de sesión

`SignOutButton_Click` quita al usuario de la caché de usuario MSAL: esto indicará a MSAL de manera eficaz que olvide al usuario actual de manera que una solicitud futura para adquirir un token solo se completará correctamente si se realiza para ser interactiva.
Aunque la aplicación en este ejemplo es compatible con un solo usuario, MSAL admite escenarios en los que es posible iniciar sesión en varias cuentas al mismo tiempo: un ejemplo es una aplicación de correo electrónico donde un usuario tiene varias cuentas.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Visualización de información de token básica

1. Agregue el método siguiente a su `MainWindow.xaml.cs` para que aparezca información básica sobre el token:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>Más información

Los tokens adquiridos a través de *OpenID Connect* también contienen un pequeño subconjunto de información relativa al usuario. `DisplayBasicTokenInfo` muestra información básica contenida en el token: por ejemplo, el nombre para mostrar del usuario y su identificador, así como la fecha de expiración del token y la cadena que representa al propio token de acceso. Esta información se muestra para que la pueda ver. Puede presionar varias veces el botón *Call Microsoft Graph API* (Llamar a la API Graph de Microsoft) y ver que el mismo token se reutilizó para solicitudes posteriores. También puede ver que la fecha de expiración se amplía si MSAL decide que es el momento de renovar el token.
<!--end-collapse-->


