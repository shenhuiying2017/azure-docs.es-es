
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Uso de MSAL para obtener un token de la API de Microsoft Graph

En esta sección, usará MSAL para obtener un token de la API de Microsoft Graph.

1.  En el archivo *MainWindow.xaml.cs*, añada la referencia de MSAL a la clase:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Sustituya el código de clase `MainWindow` por lo siguiente:

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
#### <a name="get-a-user-token-interactively"></a>Obtención de un token de usuario interactivamente
La llamada a `AcquireTokenAsync` genera una ventana que pide al usuario que inicie sesión. Las aplicaciones suelen requerir a los usuarios que inicien sesión de forma interactiva la primera vez que necesitan acceder a un recurso protegido. Es posible que también deban iniciar sesión cuando se produce un error en una operación silenciosa para adquirir un token (por ejemplo, si ha caducado la contraseña de usuario).

#### <a name="get-a-user-token-silently"></a>Obtención de un token de usuario en silencio
El método `AcquireTokenSilentAsync` controla las renovaciones y las adquisiciones de tokens sin la interacción del usuario. Después de que `AcquireTokenAsync` se ejecute por primera vez, `AcquireTokenSilentAsync` es el método habitual que se utiliza para obtener los tokens empleados para acceder a recursos protegidos en las llamadas posteriores, ya que las llamadas para solicitar o renovar los tokens se realizan en modo silencioso.

En última instancia, se producirá un error en el método `AcquireTokenSilentAsync`. El error puede deberse a que el usuario haya cerrado sesión o cambiado su contraseña en otro dispositivo. Si MSAL detecta que el problema puede solucionarse requiriendo una acción interactiva, desencadena una excepción `MsalUiRequiredException`. La aplicación puede abordar esta excepción de dos maneras:

* Puede realizar una llamada en `AcquireTokenAsync` inmediatamente. Esta llamada provoca que se solicite al usuario que inicie sesión. Este patrón se da normalmente en aplicaciones en línea en las que no hay ningún contenido disponible sin conexión para el usuario. La aplicación de ejemplo generada en esta instalación guiada utiliza este patrón, que puede verse en acción la primera vez que se ejecuta la aplicación. 
    * Dado que ningún usuario ha usado la aplicación, `PublicClientApp.Users.FirstOrDefault()` contendrá un valor NULL y se iniciará una excepción `MsalUiRequiredException`. 
    * El código del ejemplo trata entonces la excepción llamando a `AcquireTokenAsync`, lo que provoca que se pida al usuario que inicie sesión.

* En su lugar, puede presentar una indicación visual a los usuarios para señalar que es necesario iniciar sesión de manera interactiva, lo que les permitirá escoger el momento oportuno para iniciar sesión. Asimismo, la aplicación puede volver a probar el método `AcquireTokenSilentAsync` más tarde. Este patrón se utiliza con frecuencia cuando los usuarios pueden usar otras funciones de aplicación sin interrupciones; por ejemplo, cuando hay contenido sin conexión disponible en la aplicación. En este caso, los usuarios pueden decidir cuándo desean iniciar sesión para acceder al recurso protegido o para actualizar la información obsoleta. Como alternativa, la aplicación puede decidir probar el método `AcquireTokenSilentAsync` de nuevo cuando se restablezca la red después de no haber estado disponible temporalmente.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Llamada a la API de Microsoft Graph con el token que acaba de obtener

Añada el siguiente método nuevo a su archivo `MainWindow.xaml.cs`. El método se utiliza para realizar una solicitud de `GET` a Graph API con un encabezado de autorización:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Más información acerca de cómo realizar una llamada de REST a una API protegida

En esta aplicación de ejemplo, el método `GetHttpContentWithToken` se usa para realizar una solicitud HTTP `GET` a un recurso protegido que requiere un token y, a continuación, devolver el contenido al autor de la llamada. Este método agrega el token adquirido al encabezado de autorización HTTP. En este ejemplo, el recurso es el punto de conexión *me* de la API de Microsoft Graph, que muestra información del perfil del usuario.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Adición de un método para cerrar la sesión de un usuario

Añada el método siguiente a su archivo `MainWindow.xaml.cs` para cerrar la sesión de un usuario:

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
### <a name="more-information-about-user-sign-out"></a>Más información sobre cerrar la sesión de un usuario

El método `SignOutButton_Click` anterior quita a los usuarios de la caché de usuario MSAL, lo que de hecho indicará a MSAL que olvide al usuario actual de forma que una solicitud futura para adquirir un token solo se completará correctamente si se realiza para ser interactiva.

Aunque la aplicación en este ejemplo es compatible con usuarios individuales, MSAL admite escenarios en los que es posible iniciar sesión en varias cuentas al mismo tiempo. Un ejemplo de esto es una aplicación de correo electrónico donde un usuario tiene varias cuentas.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Visualización de información de token básica

Para mostrar información básica sobre el token, añada el método siguiente a su archivo *MainWindow.xaml.cs*:

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

Además del token de acceso que se usa para llamar a la API de Microsoft Graph, después de que el usuario inicie sesión, MSAL también obtiene un token de identificador. Este token contiene un pequeño subconjunto de información que es pertinente para los usuarios. El método `DisplayBasicTokenInfo` muestra la información básica que contiene el token. Por ejemplo, muestra el nombre para mostrar del usuario y su identificador, así como la fecha de expiración del token y la cadena que representa al propio token de acceso. Puede seleccionar varias veces el botón *Call Microsoft Graph API* (Llamar a la API de Microsoft Graph) y ver que el mismo token se reutilizó para solicitudes posteriores. También puede ver que la fecha de expiración se amplía si MSAL decide que es el momento de renovar el token.
<!--end-collapse-->

