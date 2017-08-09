
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utilice la biblioteca de autenticación de Microsoft (MSAL) para obtener un token para la API Graph de Microsoft.

Agregue el código siguiente a su `index.html` dentro de la etiqueta `<body>`:

```html
<script type="text/javascript">

    var graphAPIMeEndpoint = "https://graph.microsoft.com/v1.0/me";
    var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

    // Initialize application
    var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID);

    // Set redirect URI
    userAgentApplication.redirectUri = msalconfig.redirectUri;

    displayUserInfo();

    function displayUserInfo() {
        var user = userAgentApplication.getUser();
        if (user) {
            // Display the user info
            var userInfoElement = document.getElementById("userInfo");
            userInfoElement.parentElement.classList.remove("hidden");
            userInfoElement.innerHTML = JSON.stringify(user, null, 4);

            // Show Sign-Out button
            document.getElementById("signOutButton").classList.remove("hidden");
        }
    }

    function callGraphAPI() {
        if (userAgentApplication.getAllUsers().length === 0) {
            userAgentApplication.loginPopup()
                .then(function (token) {
                    console.log(token);
                    displayUserInfo();
                    callGraphAPI();
                }, function (error) {
                    showError("login", error, document.getElementById("errorMessage"));
                });
        } else {
            var responseElement = document.getElementById("graphResponse");
            responseElement.parentElement.classList.remove("hidden");
            responseElement.innerText = "Calling Graph ...";
            callWebApiWithScope(graphAPIMeEndpoint,
                graphAPIScopes,
                responseElement,
                document.getElementById("errorMessage"),
                document.getElementById("accessToken"));
        }
    }

    function callWebApiWithScope(endpoint, scope, responseElement, errorElement, showTokenElement) {
        userAgentApplication.acquireTokenSilent(scope)
            .then(function (token) {
                callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
            }, function (error) {
                if (error.indexOf("interaction_required" !== -1)) {
                    userAgentApplication.acquireTokenPopup(scope).then(function(token) {
                            callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
                        },
                        function(error) {
                            showError(endpoint, error, errorElement);
                        });
                } else {
                    showError(endpoint, error, errorElement);
                }
            });
    }

    function showAPIResponse(data, token, responseElement, showTokenElement) {
        console.log(data);
        responseElement.innerHTML = JSON.stringify(data, null, 4);
        if (showTokenElement) {
            showTokenElement.parentElement.classList.remove("hidden");
            showTokenElement.innerHTML = token;
        }
    }

    function showError(endpoint, error, errorElement) {
        console.error(error);
        var formattedError = JSON.stringify(error, null, 4);
        if (formattedError.length < 3) {
            formattedError = error;
        }
        errorElement.innerHTML = "Error calling " + endpoint + ": " + formattedError;
    }
</script>
```

<!--start-collapse-->
### <a name="more-information"></a>Más información
#### <a name="sign-in-the-user"></a>Inicio de la sesión del usuario
Después de que un usuario hace clic en el botón 'Llamar a Microsoft Graph API' por primera vez, `loginPopup` se ejecuta para que inicie sesión. Este método genera una ventana en el explorador que pide al usuario que inicie sesión. Si el usuario inicia sesión correctamente, MSAL almacena en caché la información de usuario y se devuelve un token. Este token se conoce como el *token de identificador* y contiene información básica sobre el usuario, como su nombre. Si piensa utilizar los datos proporcionados por este token para algún propósito, debe asegurarse de que el servidor backend lo valide para garantizar que el token se emitió a un usuario válido para la aplicación.

La instancia de SPA generada por esta guía no usa directamente el token de identificador: en su lugar, llama a `acquireTokenSilent` o a `acquireTokenPopup` para adquirir un *token de acceso* que se usa para consultar la interfaz Microsoft Graph API. Si necesita obtener un ejemplo que valide el token de identificador, eche un vistazo a [esta aplicación de ejemplo](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "ejemplo Github active-directory-javascript-singlepageapp-dotnet-webapi-v2") en GitHub (usa una ASP.NET WEB API para la validación del token).

#### <a name="getting-a-user-token-interactively"></a>Obtención de un token de usuario interactivamente
La llamada al método `acquireTokenPopup(scope)` genera una ventana en el explorador que pide al usuario que inicie sesión. Las aplicaciones suelen requerir que un usuario inicie sesión interactivamente la primera vez que requieren acceso a un recurso protegido, o cuando se produce un error en una operación silenciosa para adquirir un token (por ejemplo, si la contraseña de usuario caducó).

#### <a name="getting-a-user-token-silently"></a>Obtención de un token de usuario en silencio
El método ` acquireTokenSilent` controla la renovación y las adquisiciones de tokens sin la interacción del usuario. Después de ejecutar `loginPopup` por primera vez, `acquireTokenSilent` es el método usado habitualmente para obtener los tokens empleados para acceder a recursos protegidos en las llamadas posteriores, ya que las llamadas para solicitar o renovar los tokens se realizan en modo silencioso.
Es posible que `acquireTokenSilent` genere eventualmente un error; por ejemplo, si el usuario ha cambiado su contraseña en otro dispositivo. La aplicación puede abordar esta excepción de dos maneras:

1.  Realizar una llamada a `acquireTokenPopup` inmediatamente, lo que ocasiona que el usuario tenga que iniciar sesión. Este patrón se da normalmente en aplicaciones en línea en las que no hay ningún contenido sin conexión en la aplicación disponible para el usuario. El ejemplo generado por esta instalación guiada usa este patrón.
2. Las aplicaciones también pueden hacer una indicación visual al usuario de que se requiere un inicio de sesión interactivo, de manera que el usuario pueda seleccionar el momento adecuado para iniciar sesión, o la aplicación puede reintentar `acquireTokenSilent` en un momento posterior. Esto se utiliza normalmente cuando el usuario puede utilizar otras funciones de la aplicación sin que se le interrumpa: por ejemplo, hay contenido sin conexión disponible en la aplicación. En este caso, el usuario puede decidir el momento en que desea iniciar sesión para tener acceso al recurso protegido, o actualizar la información no actualizada; o puede decidir que la aplicación vuelva a intentar `acquireTokenSilent` cuando se restaure la red después de estar no disponible temporalmente.

Otro escenario donde `acquireTokenSilent` generará un error es cuando el usuario no ha dado aún el consentimiento en el ámbito solicitado por la llamada. Por ejemplo, la primera vez que un usuario ejecuta la aplicación generada con esta guía, el ámbito *user.read* se proporciona como un parámetro. Dado que el usuario que inició sesión nunca ha dado el consentimiento para leer su perfil, `acquireTokenSilent` generará una excepción que contiene `interaction_required` (lo que suele indicar que debe llamarse a `acquireTokenPopup` como modo de solucionar el problema). En este caso, se creará una ventana emergente para mostrar la pantalla de consentimiento para el ámbito necesario.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Llamada a la API Graph de Microsoft con el token que acaba de obtener

Agregue el código siguiente a su archivo index.html dentro de la etiqueta `<body>`:

```html
 <script type="text/javascript">
function callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    // Note that fetch API is not available in all browsers
    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showAPIResponse(data, token, responseElement, showTokenElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showError(endpoint, data, errorElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error, errorElement);
        });
}
</script>
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Más información acerca de cómo realizar una llamada de REST a una API protegida

En la aplicación de ejemplo creada en esta guía, el método `callWebApiWithToken()` se usa para realizar una solicitud HTTP `GET` a un recurso protegido que requiere un token y, a continuación, devolver el contenido al autor de la llamada. Este método agrega el token adquirido al *encabezado de autorización HTTP*. En este ejemplo de aplicación creada en esta guía, el recurso es el punto de conexión *me* de Microsoft Graph API, que muestra información del perfil del usuario.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adición de un método para cerrar la sesión del usuario

Agregue el código siguiente a su archivo index.html dentro de la etiqueta `<body>`:

```html
 <script type="text/javascript">
    function signOut() {
        userAgentApplication.logout();
    }
</script>
```
