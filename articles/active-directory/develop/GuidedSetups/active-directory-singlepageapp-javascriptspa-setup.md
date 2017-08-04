
## <a name="setting-up-your-web-server-or-project"></a>Configuración del servidor web o proyecto

> ¿Prefiere descargar este proyecto de Visual Studio de ejemplo en su lugar? [Descargue un proyecto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/master.zip) y vaya al [paso de configuración](#create-an-application-express) para configurar el código de ejemplo antes de ejecutar.

## <a name="prerequisites"></a>Requisitos previos
Un servidor web local como [http-server](https://www.npmjs.com/package/http-server/), [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) o la integración de IIS Express con [Visual Studio 2017](https://www.visualstudio.com/downloads/) son necesarios para ejecutar esta configuración guiada. 

Las instrucciones de esta guía están basadas en Visual Studio 2017, pero no dude en usar cualquier otro entorno de desarrollo o editor de JavaScript/HTML.


## <a name="create-your-project-visual-studio-only"></a>Cree su proyecto (solo en Visual Studio)

Si usa Visual Studio y crea un nuevo proyecto, siga estos pasos para crear una nueva solución de Visual Studio:
1.  En Visual Studio:  `File` > `New` > `Project`
2.  En `Visual C#\Web`, seleccione `ASP.NET Web Application (.NET Framework)`
3.  Asigne un nombre a la aplicación y haga clic en *Aceptar*.
4.  En `New ASP.NET Web Application`, seleccione `Empty`


## <a name="create-your-single-page-applications-ui"></a>Cree la interfaz de usuario de la aplicación de una sola página
1.  Cree un archivo index.html para JavaScript SPA. Si se utiliza Visual Studio, seleccione el proyecto (carpeta raíz del proyecto), haga clic con el botón derecho y seleccione: `Add` > `New Item` > `HTML page` y asígnele el nombre index.html
2.  Agregue el siguiente código a su página:
```html
<!DOCTYPE html>
<html>
    <head>
        <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
        <title>JavaScript SPA Guided Setup</title>
    </head>
    <body style="margin: 40px">
        <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphAPI()">Call Microsoft Graph API</button>
        <div id="errorMessage" class="text-danger"></div>
        <div class="hidden">
            <h3>Graph API Call Response</h3>
            <pre class="well" id="graphResponse"></pre>
        </div>
        <div class="hidden">
            <h3>Access Token</h3>
            <pre class="well" id="accessToken"></pre>
        </div>
        <div class="hidden">
            <h3>ID Token Claims</h3>
            <pre class="well" id="userInfo"></pre>
        </div>
        <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

        <script src="//secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>
        <script type="text/javascript" src="msalconfig.js"></script>
    
        <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
        <script src="//cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
        <script src="//cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>
    </body>
</html>
````
