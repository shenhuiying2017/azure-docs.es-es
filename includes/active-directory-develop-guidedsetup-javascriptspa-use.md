## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Use la biblioteca de autenticación de Microsoft (MSAL) para iniciar la sesión del usuario

1.  Cree un archivo llamado `app.js`. Si se usa Visual Studio, seleccione el proyecto (carpeta raíz del proyecto), haga clic con el botón derecho y seleccione: `Add` > `New Item` > `JavaScript File`.
2.  Agregue el siguiente código al archivo `app.js`:

```javascript
// Graph API endpoint to show user profile
var graphApiEndpoint = "https://graph.microsoft.com/v1.0/me";

// Graph API scope used to obtain the access token to read user profile
var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

// Initialize application
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});

//Previous version of msal uses redirect url via a property
if (userAgentApplication.redirectUri) {
    userAgentApplication.redirectUri = msalconfig.redirectUri;
}

window.onload = function () {
    // If page is refreshed, continue to display user info
    if (!userAgentApplication.isCallback(window.location.hash) && window.parent === window && !window.opener) {
        var user = userAgentApplication.getUser();
        if (user) {
            callGraphApi();
        }
    }
}

/**
 * Call the Microsoft Graph API and display the results on the page. Sign the user in if necessary
 */
function callGraphApi() {
    var user = userAgentApplication.getUser();
    if (!user) {
        // If user is not signed in, then prompt user to sign in via loginRedirect.
        // This will redirect user to the Azure Active Directory v2 Endpoint
        userAgentApplication.loginRedirect(graphAPIScopes);
        // The call to loginRedirect above frontloads the consent to query Graph API during the sign-in.
        // If you want to use dynamic consent, just remove the graphAPIScopes from loginRedirect call.
        // As such, user will be prompted to give consent when requested access to a resource that 
        // he/she hasn't consented before. In the case of this application - 
        // the first time the Graph API call to obtain user's profile is executed.
    } else {
        // If user is already signed in, display the user info
        var userInfoElement = document.getElementById("userInfo");
        userInfoElement.parentElement.classList.remove("hidden");
        userInfoElement.innerHTML = JSON.stringify(user, null, 4);

        // Show Sign-Out button
        document.getElementById("signOutButton").classList.remove("hidden");

        // Now Call Graph API to show the user profile information:
        var graphCallResponseElement = document.getElementById("graphResponse");
        graphCallResponseElement.parentElement.classList.remove("hidden");
        graphCallResponseElement.innerText = "Calling Graph ...";

        // In order to call the Graph API, an access token needs to be acquired.
        // Try to acquire the token used to query Graph API silently first:
        userAgentApplication.acquireTokenSilent(graphAPIScopes)
            .then(function (token) {
                //After the access token is acquired, call the Web API, sending the acquired token
                callWebApiWithToken(graphApiEndpoint, token, graphCallResponseElement, document.getElementById("accessToken"));

            }, function (error) {
                // If the acquireTokenSilent() method fails, then acquire the token interactively via acquireTokenRedirect().
                // In this case, the browser will redirect user back to the Azure Active Directory v2 Endpoint so the user 
                // can reenter the current username/ password and/ or give consent to new permissions your application is requesting.
                // After authentication/ authorization completes, this page will be reloaded again and callGraphApi() will be executed on page load.
                // Then, acquireTokenSilent will then get the token silently, the Graph API call results will be made and results will be displayed in the page.
                if (error) {
                    userAgentApplication.acquireTokenRedirect(graphAPIScopes);
                }
            });
    }
}

/**
 * Callback method from sign-in: if no errors, call callGraphApi() to show results.
 * @param {string} errorDesc - If error occur, the error message
 * @param {object} token - The token received from login
 * @param {object} error - The error string
 * @param {string} tokenType - The token type: For loginRedirect, tokenType = "id_token". For acquireTokenRedirect, tokenType:"access_token".
 */
function loginCallback(errorDesc, token, error, tokenType) {
    if (errorDesc) {
        showError(msal.authority, error, errorDesc);
    } else {
        callGraphApi();
    }
}

/**
 * Show an error message in the page
 * @param {string} endpoint - the endpoint used for the error message
 * @param {string} error - Error string
 * @param {string} errorDesc - Error description
 */
function showError(endpoint, error, errorDesc) {
    var formattedError = JSON.stringify(error, null, 4);
    if (formattedError.length < 3) {
        formattedError = error;
    }
    document.getElementById("errorMessage").innerHTML = "An error has occurred:<br/>Endpoint: " + endpoint + "<br/>Error: " + formattedError + "<br/>" + errorDesc;
    console.error(error);
}

```

<!--start-collapse-->
### <a name="more-information"></a>Más información

Después de que un usuario hace clic en el botón *"Call Microsoft Graph API"* (Llamar a la API de Microsoft Graph) por primera vez, el método `callGraphApi` llama a `loginRedirect` para iniciar la sesión del usuario. Este método hace que se redireccione al usuario al *punto de conexión de Microsoft Azure Active Directory v2* para pedir y validar las credenciales del usuario. Como resultado de un inicio de sesión correcto, se redirige al usuario a la página *index.html* original y se recibe un token, el que `msal.js` procesa, y se almacena en caché la información que contiene el token. Este token se conoce como el *token de identificador* y contiene información básica sobre el usuario, como su nombre. Si piensa utilizar los datos proporcionados por este token para algún propósito, debe asegurarse de que el servidor backend lo valide para garantizar que el token se emitió a un usuario válido para la aplicación.

La instancia de SPA generada por esta guía no usa directamente el token de identificador: en su lugar, llama a `acquireTokenSilent` o a `acquireTokenRedirect` para adquirir un *token de acceso* que se usa para consultar Microsoft Graph API. Si necesita obtener un ejemplo que valide el token de identificador, eche un vistazo a [esta aplicación de ejemplo](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "ejemplo Github active-directory-javascript-singlepageapp-dotnet-webapi-v2") en GitHub (usa una ASP.NET WEB API para la validación del token).

#### <a name="getting-a-user-token-interactively"></a>Obtención de un token de usuario interactivamente

Después del inicio de sesión inicial, no desea pedirle a los usuarios que se reautentiquen cada vez que tengan que solicitar un token para acceder a un recurso, por lo que *acquireTokenSilent* se debería usar la mayor parte del tiempo para adquirir los tokens. Sin embargo, hay situaciones en las que resulta necesario forzar a que los usuarios interactúen con el punto de conexión de Azure Active Directory v2. Algunos ejemplos de esto son los siguientes:
-   Es posible que los usuarios deban volver a escribir las credenciales porque la contraseña expiró
-   La aplicación solicita acceso a un recurso para el cual el usuario necesita consentimiento
-   Se requiere la autenticación en dos fases

Llamar a *acquireTokenRedirect(scope)* genera que se redirija a los usuarios al punto de conexión de Azure Active Directory v2 (o *acquireTokenPopup(scope)* genera una ventana emergente) donde los usuarios deben interactuar, ya sea confirmando las credenciales, dándole el consentimiento al recurso requerido o completando la autenticación en dos fases.

#### <a name="getting-a-user-token-silently"></a>Obtención de un token de usuario en silencio
El método ` acquireTokenSilent` controla la renovación y las adquisiciones de tokens sin la interacción del usuario. Después de ejecutar `loginRedirect` (o `loginPopup`) por primera vez, `acquireTokenSilent` es el método usado habitualmente para obtener los tokens empleados para acceder a recursos protegidos en las llamadas posteriores, ya que las llamadas para solicitar o renovar los tokens se realizan en modo silencioso.
`acquireTokenSilent` puede generar errores en algunos casos, por ejemplo, si la contraseña del usuario expiró. La aplicación puede abordar esta excepción de dos maneras:

1.  Realizar una llamada a `acquireTokenRedirect` inmediatamente, lo que ocasiona que el usuario tenga que iniciar sesión. Este patrón se da comúnmente en aplicaciones en línea en las que no hay ningún contenido no autenticado en la aplicación disponible para el usuario. El ejemplo generado por esta instalación guiada usa este patrón.

2. Las aplicaciones también pueden hacer una indicación visual al usuario de que se requiere un inicio de sesión interactivo, de manera que el usuario pueda seleccionar el momento adecuado para iniciar sesión, o la aplicación puede reintentar `acquireTokenSilent` en un momento posterior. Esto se usa habitualmente cuando el usuario puede utilizar otras funciones de la aplicación sin que se le interrumpa: por ejemplo, hay contenido no autenticado disponible en la aplicación. En este caso, el usuario puede decidir cuando desea iniciar sesión para acceder al recurso protegido o para actualizar la información obsoleta.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Llamada a Microsoft Graph API con el token que acaba de obtener

Agregue el siguiente código al archivo `app.js`:

```javascript
/**
 * Call a Web API using an access token.
 * @param {any} endpoint - Web API endpoint
 * @param {any} token - Access token
 * @param {object} responseElement - HTML element used to display the results
 * @param {object} showTokenElement = HTML element used to display the RAW access token
 */
function callWebApiWithToken(endpoint, token, responseElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        console.log(data);
                        responseElement.innerHTML = JSON.stringify(data, null, 4);
                        if (showTokenElement) {
                            showTokenElement.parentElement.classList.remove("hidden");
                            showTokenElement.innerHTML = token;
                        }
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response as error in the page
                        showError(endpoint, data);
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error);
        });
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Más información acerca de cómo realizar una llamada de REST a una API protegida

En la aplicación de ejemplo creada en esta guía, el método `callWebApiWithToken()` se usa para realizar una solicitud HTTP `GET` a un recurso protegido que requiere un token y, a continuación, devolver el contenido al autor de la llamada. Este método agrega el token adquirido al *encabezado de autorización HTTP*. En este ejemplo de aplicación creada en esta guía, el recurso es el punto de conexión *me* de Microsoft Graph API, que muestra información del perfil del usuario.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adición de un método para cerrar la sesión del usuario

Agregue el siguiente código al archivo `app.js`:

```javascript
/**
 * Sign-out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
