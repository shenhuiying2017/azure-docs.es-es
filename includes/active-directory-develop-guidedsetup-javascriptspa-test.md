## <a name="test-your-code"></a>Prueba del código

### <a name="test-with-visual-studio"></a>Pruebas con Visual Studio
Si usa Visual Studio, pulse **F5** para ejecutar el proyecto. El explorador se abre en la ubicación http://<span></span>localhost:{port}, donde verá el botón **Llamar a Microsoft Graph API**.

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>Pruebas con Python u otro servidor web
Si no está utilizando Visual Studio, asegúrese de que el servidor web está activado. Configure el servidor para que escuche un puerto TCP que esté basado en la ubicación del archivo **index.html**. Si usa Python, puede empezar a escuchar el puerto al ejecutar el terminal del símbolo del sistema desde la carpeta de la aplicación:
 
```bash
python -m http.server 8080
```
A continuación, abra el explorador y escriba http://<span></span>localhost:8080 o http://<span></span>localhost:{port}, donde **port** corresponde al puerto que está escuchando el servidor web. Verá el contenido del archivo index.html y el botón **Llamar a Microsoft Graph API**.

## <a name="test-your-application"></a>Prueba de la aplicación

Una vez que el explorador cargue el archivo index.html, haga clic en el botón **Llamar a Microsoft Graph API**. Si es la primera vez que ejecute la aplicación, el explorador le redirigirá al punto de conexión de Microsoft Azure Active Directory (Azure AD) v2.0, donde se le pedirá que inicie sesión:
 
![Inicie sesión en la cuenta de la aplicación SPA de JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Consentimiento para el acceso a la aplicación

La primera vez que inicie sesión en la aplicación, también se le pedirá que dé su consentimiento para permitir que la aplicación obtenga acceso al perfil e inicie sesión automáticamente:

![Consentimiento para el acceso a la aplicación](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visualización de los resultados de la aplicación
Después de iniciar sesión, podrá ver la información del perfil de usuario en el cuadro **Graph API Call Response** (Respuesta a la llamada a API Graph).
 
![Resultados esperados de la llamada a Microsoft Graph API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

También verá información básica sobre el token adquirido en los cuadros **Token de acceso** e **ID Token Claims** (Notificaciones de token de identificador).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Más información sobre los ámbitos y permisos delegados

Microsoft Graph API requiere el ámbito **user.read** para leer el perfil del usuario. Este ámbito se agrega automáticamente de forma predeterminada en todas las aplicaciones que se registran en el portal de registro. Otras API de Microsoft Graph, así como las API personalizadas para el servidor back-end, pueden requerir ámbitos adicionales. Microsoft Graph API requiere el ámbito **Calendars.read** para mostrar los calendarios del usuario.

Para tener acceso a los calendarios del usuario en el contexto de una aplicación, agregue el permiso delegado **Calendars.Read** a la información del registro de la aplicación. A continuación, agregue el ámbito **Calendars.Read** a la llamada **acquireTokenSilent**. 

>[!NOTE]
>Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

Si una API de back-end no requiere un ámbito (no recomendado), puede usar el valor de **clientId** como ámbito en las llamadas **acquireTokenSilent** y **acquireTokenRedirect**.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
