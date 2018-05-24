---
title: Introducción a UWP de Azure AD v2 | Microsoft Docs
description: Cómo pueden llamar las aplicaciones de la Plataforma universal de Windows (XAML) a una API que requiera tokens de acceso mediante el punto de conexión de Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 390559922b3b8fb293d1c8b38f36dfd0a1df9ebd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763380"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Llamar a Microsoft Graph API desde una aplicación de la Plataforma universal de Windows (UWP)

En esta guía se muestra cómo una aplicación nativa de la Plataforma universal de Windows (XAML) puede obtener un token de acceso y usarlo para llamar a Microsoft Graph API u otras API que requieran tokens de acceso desde un punto de conexión de Azure Active Directory v2.

Al final de esta guía, la aplicación podrá llamar a una API protegida utilizando cuentas personales (como outlook.com, live.com y otras), así como cuentas profesionales y educativas de cualquier empresa u organización que tenga Azure Active Directory.  

> En esta guía es necesario instalar el desarrollo de Visual Studio 2017 con Universal Windows Platform. Consulte este [artículo: ](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "Set up Visual Studio for UWP") (Configure Visual Studio para UWP) para obtener instrucciones sobre cómo descargar y configurar Visual Studio para desarrollar las aplicaciones de la Plataforma universal de Windows.

### <a name="how-this-guide-works"></a>Funcionamiento de esta guía

![Funcionamiento de esta guía](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

La aplicación de ejemplo que se puede crear con esta guía permite que una aplicación de UWP consulte una Microsoft Graph API o Web API que acepte tokens desde un punto de conexión de Azure Active Directory v2. En este escenario, se agrega un token a las solicitudes HTTP a través del encabezado de autorización. La adquisición y la renovación de los tokens se realiza por medio de la biblioteca de autenticación de Microsoft (MSAL).

### <a name="nuget-packages"></a>Paquetes de NuGet

Esta guía utiliza los siguientes paquetes NuGet:

|Biblioteca|DESCRIPCIÓN|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de autenticación de Microsoft (MSAL)|


## <a name="set-up-your-project"></a>Configurar su proyecto

En esta sección se proporcionan instrucciones paso a paso sobre cómo integrar una aplicación .NET del escritorio de Windows (XAML) con *Iniciar sesión en Microsoft*, de manera que pueda consultar las Web API que requieran un token, como Microsoft Graph API.

La aplicación que se puede crear siguiendo esta guía muestra un botón que se utiliza para consultar Graph API, un botón para cerrar la sesión y cuadros de texto que muestran los resultados de las llamadas.

> ¿Prefiere descargar este proyecto de Visual Studio de ejemplo en su lugar? [Descargue un proyecto](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) y vaya al paso [Registro de aplicaciones](#register-your-application "Paso de registro de aplicaciones") para configurar el código de ejemplo antes de ejecutarlo.


### <a name="create-your-application"></a>Creación de la aplicación
1. En Visual Studio, haga clic en **Archivo** > **Nuevo** > **Proyecto**.<br/>
2. En *Plantillas*, seleccione **Visual C#**.
3. Seleccione **Aplicación vacía (Windows universal)**.
4. Asígnele un nombre y haga clic en "Aceptar".
5. Si se le solicita, puede seleccionar cualquier versión de tipo *Destino* y *Mínima* y hacer clic en "Aceptar":<br/><br/>![Versiones mínima y de destino](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Adición de la biblioteca de autenticación de Microsoft (MASL) a su proyecto
1. En Visual Studio, haga clic en **Herramientas** > **Administrador de paquetes NuGet** > **Consola del administrador de paquetes**.
2. Copie y pegue el siguiente comando en la ventana de la consola del administrador de paquetes:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> El paquete anterior instala la [biblioteca de autenticación de Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL controla la adquisición, el almacenamiento en caché y la actualización de los tokens de usuario que se utilizan para obtener acceso a las API que protege Azure Active Directory v2.

## <a name="initialize-msal"></a>Inicializar MSAL
En este paso le ayudaremos a crear una clase para controlar la interacción con la biblioteca de MSAL como, por ejemplo, el control de tokens.

1. Abra el archivo **App.xaml.cs** y, a continuación, agregue la referencia de la biblioteca de MSAL a la clase:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Agregue las dos líneas siguientes a la clase de la aplicación (dentro del bloque <code>sealed partial class App : Application</code>):

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Creación de la IU de la aplicación

Debe crearse un archivo **MainPage.xaml** automáticamente como parte de la plantilla de proyecto. Abra este archivo y siga las instrucciones:

1.  Reemplace el nodo **<Grid>** de la aplicación con:

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utilice la biblioteca de autenticación de Microsoft (MSAL) para obtener un token para Microsoft Graph API.

En esta sección se muestra cómo usar MSAL para obtener un token de Microsoft Graph API.

1.  En **MainPage.xaml.cs**, agregue la referencia de la biblioteca de MSAL a la clase:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Reemplace el código de la clase <code>MainPage</code> con:

    ```csharp
    public sealed partial class MainPage : Page
    {
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
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
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

### <a name="more-information"></a>Más información
#### <a name="get-a-user-token-interactively"></a>Obtención de un token de usuario interactivamente
La llamada a `AcquireTokenAsync` genera una ventana que pide al usuario que inicie sesión. Las aplicaciones suelen requerir a los usuarios que inicien sesión de forma interactiva la primera vez que necesitan acceder a un recurso protegido. Es posible que también deban iniciar sesión cuando se produce un error en una operación silenciosa para adquirir un token (por ejemplo, si ha caducado la contraseña de usuario).

#### <a name="get-a-user-token-silently"></a>Obtención de un token de usuario en silencio
El método `AcquireTokenSilentAsync` controla las renovaciones y las adquisiciones de tokens sin la interacción del usuario. Después de que `AcquireTokenAsync` se ejecute por primera vez, `AcquireTokenSilentAsync` es el método habitual que se utiliza para obtener los tokens empleados para acceder a recursos protegidos en las llamadas posteriores, ya que las llamadas para solicitar o renovar los tokens se realizan en modo silencioso.

En última instancia, se producirá un error en el método `AcquireTokenSilentAsync`. El error puede deberse a que el usuario haya cerrado sesión o cambiado su contraseña en otro dispositivo. Si MSAL detecta que el problema puede solucionarse requiriendo una acción interactiva, desencadena una excepción `MsalUiRequiredException`. La aplicación puede abordar esta excepción de dos maneras:

* Puede realizar una llamada en `AcquireTokenAsync` inmediatamente. Esta llamada provoca que se solicite al usuario que inicie sesión. Este patrón se da normalmente en aplicaciones en línea en las que no hay ningún contenido sin conexión disponible para el usuario. La aplicación de ejemplo generada en esta instalación guiada utiliza este patrón, que puede verse en acción la primera vez que se ejecuta la aplicación. 
    * Dado que ningún usuario ha usado la aplicación, `PublicClientApp.Users.FirstOrDefault()` contendrá un valor NULL y se iniciará una excepción `MsalUiRequiredException`. 
    * El código del ejemplo trata entonces la excepción llamando a `AcquireTokenAsync`, lo que provoca que se pida al usuario que inicie sesión.

* En su lugar, puede presentar una indicación visual a los usuarios para señalar que es necesario iniciar sesión de manera interactiva, lo que les permitirá escoger el momento oportuno para iniciar sesión. Asimismo, la aplicación puede volver a probar el método `AcquireTokenSilentAsync` más tarde. Este patrón se utiliza con frecuencia cuando los usuarios pueden usar otras funciones de aplicación sin interrupciones; por ejemplo, cuando hay contenido sin conexión disponible en la aplicación. En este caso, los usuarios pueden decidir cuándo desean iniciar sesión para acceder al recurso protegido o para actualizar la información obsoleta. Como alternativa, la aplicación puede decidir probar el método `AcquireTokenSilentAsync` de nuevo cuando se restablezca la red después de no haber estado disponible temporalmente.

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Llamada a Microsoft Graph API con el token que acaba de obtener

1. Añada el siguiente método nuevo al archivo **MainPage.xaml.cs**. El método se utiliza para realizar una solicitud de `GET` a Graph API con un encabezado de autorización:

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
            // Add the token in Authorization header
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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Más información acerca de cómo realizar una llamada de REST a una API protegida

En esta aplicación de ejemplo, el método `GetHttpContentWithToken` se usa para realizar una solicitud HTTP `GET` a un recurso protegido que requiere un token y, a continuación, devolver el contenido al autor de la llamada. Este método agrega el token adquirido al *encabezado de autorización HTTP*. En este ejemplo, el recurso es el punto de conexión *me* de Microsoft Graph API, que muestra información de perfil del usuario.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adición de un método para cerrar la sesión del usuario

1. Para cerrar la sesión del usuario, agregue el método siguiente a **MainPage.xaml.cs**:

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

### <a name="more-info-on-sign-out"></a>Más información sobre el cierre de sesión

El método `SignOutButton_Click` quita al usuario de la caché de usuario de MSAL; esta acción indicará a MSAL de manera eficaz que olvide al usuario actual, de manera que una solicitud futura para adquirir un token solo podrá completarse correctamente si se realiza para ser interactiva.
Aunque la aplicación en este ejemplo es compatible con un solo usuario, MSAL admite escenarios en los que es posible iniciar sesión en varias cuentas al mismo tiempo: un ejemplo es una aplicación de correo electrónico donde un usuario tiene varias cuentas.

## <a name="display-basic-token-information"></a>Visualización de información de token básica

1. Para mostrar información básica sobre el token, agregue el método siguiente al archivo **MainPage.xaml.cs**:

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

### <a name="more-information"></a>Más información

Los id. de token adquiridos a través de *OpenID Connect* también contienen un pequeño subconjunto de información relativa al usuario. `DisplayBasicTokenInfo` muestra información básica contenida en el token: por ejemplo, el nombre para mostrar del usuario y su identificador, así como la fecha de expiración del token y la cadena que representa al propio token de acceso. Esta información se muestra para que la pueda ver. Puede presionar varias veces el botón **Call Microsoft Graph API** (Llamar a la API Graph de Microsoft) y ver que el mismo token se reutilizó para solicitudes posteriores. También puede ver que la fecha de expiración se amplía si MSAL decide que es el momento de renovar el token.

## <a name="register-your-application"></a>Registrar su aplicación

Ahora tiene que registrar la aplicación en el *Portal de registro de aplicaciones de Microsoft*:
1. Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar una aplicación.
2. Escriba un nombre para la aplicación 
3. Asegúrese de que está desactivada la opción de configuración paso a paso.
4. Seleccione **Agregar plataforma**, **Aplicación nativa** y, a continuación, seleccione Guardar.
5. Copie el GUID en el id. de aplicación, vuelva a Visual Studio, abra **App.xaml.cs** y reemplace `your_client_id_here` con el id. de aplicación que acaba de registrar:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Habilitar la autenticación integrada en dominios federados (opcional)

Para habilitar la autenticación integrada de Windows cuando se utiliza con un dominio federado de Azure Active Directory, el manifiesto de la aplicación debe habilitar funcionalidades adicionales:

1. Haga doble clic en **Package.appxmanifest**.
2. Seleccione la pestaña **Funcionalidades** y asegúrese de que las siguientes opciones de configuración estén habilitadas:

    - Autenticación empresarial
    - Redes privadas (cliente y servidor)
    - Certificados de usuario compartidos 

3. A continuación, abra **App.xaml.cs** y agregue la siguiente línea en el constructor de aplicaciones:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> La autenticación integrada de Windows no está configurada de manera predeterminada para este ejemplo, porque las aplicaciones que solicitan las funcionalidades *Autenticación empresarial* o *Certificados de usuario compartidos* requieren un mayor nivel de verificación por parte de Windows Store, y no todos los desarrolladores quieren realizar un mayor nivel de verificación. Habilite esta opción solo si necesita usar la autenticación integrada de Windows con un dominio de Azure Active Directory federado.


## <a name="test-your-code"></a>Prueba del código

Para probar la aplicación, presione `F5` para ejecutar el proyecto en Visual Studio. Aparecerá la ventana principal:

![Interfaz de usuario de la aplicación](media/active-directory-uwp-v2.md/testapp-ui.png)

Cuando esté listo para realizar la prueba, haga clic en *Call Microsoft Graph API* (Llamar a la API Graph de Microsoft) y use una cuenta de Microsoft Azure Active Directory (cuenta profesional) o una cuenta Microsoft (live.com, outlook.com) para iniciar sesión. Si es la primera vez, verá una ventana que solicita al usuario que inicie sesión:

![Página de inicio de sesión](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Consentimiento
La primera vez que inicie sesión en la aplicación, verá una pantalla de consentimiento similar a la siguiente, donde debe aceptar lo siguiente:

![Pantalla de consentimiento](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Resultados esperados
Debería ver la información de perfil de usuario que devolvió la llamada de Microsoft Graph API en la pantalla de resultados de llamadas de la API:

![Pantalla de resultados](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

También debería aparecer información básica sobre el token obtenido a través de `AcquireTokenAsync` o `AcquireTokenSilentAsync` en el cuadro de información de token:

|Propiedad  |Formato  |DESCRIPCIÓN |
|---------|---------|---------|
|**Name** |Nombre completo del usuario |Nombre y apellidos del usuario.|
|**Nombre de usuario** |<span>user@domain.com</span> |Nombre de usuario que se usa para identificar al usuario.|
|**Expiración del token** |Datetime |Hora a la que expira el token. MSAL amplía la fecha de expiración al renovar el token según sea necesario.|
|**Token de acceso** |string |Cadena de token que se envía a las solicitudes HTTP que requieran un *encabezado de autorización*.|

#### <a name="see-what-is-in-the-access-token-optional"></a>Ver el contenido del token de acceso (opcional)
Si quiere, puede copiar el valor en "Token de acceso" y pegarlo en https://jwt.ms para descodificarlo y ver la lista de notificaciones.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Más información sobre los ámbitos y permisos delegados

Microsoft Graph API requiere el ámbito *user.read* para leer el perfil del usuario. Este ámbito se agrega automáticamente de forma predeterminada en todas las aplicaciones que se van a registrar en el Portal de registro de aplicaciones. Otras API de Microsoft Graph, así como las API personalizadas para el servidor back-end, pueden requerir ámbitos adicionales. Microsoft Graph API requiere el ámbito *Calendars.read* para mostrar los calendarios del usuario.

Para tener acceso a los calendarios del usuario en el contexto de una aplicación, agregue el permiso delegado *Calendars.Read* a la información del registro de la aplicación. A continuación, agregue el ámbito *Calendars.Read* a la llamada `acquireTokenSilent`. 

> [!NOTE]
> Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

## <a name="known-issues"></a>Problemas conocidos

### <a name="issue-1"></a>Problema 1:
Puede recibir uno de los siguientes errores al iniciar sesión en la aplicación en un dominio federado de Azure Active Directory:
 - No se encontró un certificado de cliente válido en la solicitud.
 - No se encontraron certificados válidos en el almacén de certificados del usuario.
 - Vuelva a intentarlo mediante un método de autenticación diferente.

**Causa:** las funcionalidades de Enterprise y los certificados no están habilitados.

**Solución**: siga los pasos en [autenticación integrada en dominios federados](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problema 2:
Después de habilitar la [autenticación integrada en dominios federados](#enable-integrated-authentication-on-federated-domains-optional) e intentar usar Windows Hello en un equipo Windows 10 para iniciar sesión en un entorno con la autenticación multifactor configurada, se muestra la lista de certificados; sin embargo, si decide usar el PIN, la ventana del PIN nunca se muestra.

**Causa:** limitación conocida con el agente de autenticación web en aplicaciones UWP que se ejecutan en el escritorio de Windows 10 (funciona bien en Windows 10 Mobile).

**Solución alternativa:** los usuarios deben seleccionar la opción para iniciar sesión con otras alternativas y elegir *Iniciar sesión con un nombre de usuario y contraseña*; a continuación deben seleccionar su contraseña y realizar la autenticación con el teléfono.

