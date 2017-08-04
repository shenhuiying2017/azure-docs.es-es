
## <a name="test-your-code"></a>Prueba del código

Si se usa Visual Studio, presione `F5` para ejecutar el proyecto. El explorador se abrirá y le dirigirá a *http://localhost:{port}* donde verá el botón *Call Microsoft Graph API* (Llamar a API de Microsoft Graph).

Si no se utiliza Visual Studio, asegúrese de que se inicia el servidor web y de que la carpeta que contiene la aplicación web de JavaScript está configurada en el servidor web. Abra el explorador y escriba *http://localhost: {puerto}/rutaDeAcceso*: donde el *puerto* corresponde al puerto que está escuchando el servidor web y la *rutaDeAcceso* es la ruta de acceso del archivo index.html.

Haga clic en el botón *Llamar a API de Microsoft Graph*. Si es la primera vez, se mostrará una ventana emergente en la que se pedirá al usuario que inicie sesión.
 
![Captura de pantalla de ejemplo](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Consentimiento
La primera vez que inicie sesión en la aplicación, verá una pantalla de consentimiento similar a la siguiente, en la que debe aceptar explícitamente:

 ![Pantalla de consentimiento](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)

Dado que está consultando la API de Microsoft Graph, puede ver otra página de consentimiento. Esto sucede debido al *consentimiento dinámico*, que hace que se requiera el consentimiento en cada ámbito solicitado por la aplicación. En la aplicación de ejemplo generada por esta guía, se proporciona el ámbito *user.read* y, por lo tanto, debe dar el consentimiento para que esta aplicación lea el perfil del usuario.

> [!IMPORTANT]
> Actualmente, debido a un problema conocido con *msal* javascript, debe deshabilitar el bloqueador de elementos emergentes en exploradores como Chrome y Firefox, para que la pantalla de *consentimiento dinámico* funcione correctamente. La primera vez que un usuario llama a la API de Microsoft Graph mediante `acquireTokenPopup`, es necesario deshabilitar el bloqueador de elementos emergentes.

### <a name="expected-results"></a>Resultados esperados
Debería ver la información del perfil de usuario devuelta por la respuesta a la llamada a la API de Microsoft Graph.
 
 ![Results](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

También verá información básica sobre el token adquirido en los cuadros *Token de acceso* e *ID Token Claims* (Notificaciones de token de identificador).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Más información sobre los ámbitos y permisos delegados

La API de Microsoft Graph requiere el ámbito `user.read` para leer el perfil del usuario. Este ámbito se agrega automáticamente de forma predeterminada en todas las aplicaciones que se van a registrar en nuestro portal de registro. Otras API de Microsoft Graph, así como las API personalizadas para el servidor back-end, pueden requerir ámbitos adicionales. Por ejemplo, para Microsoft Graph, se requiere el ámbito `Calendars.Read` para enumerar los calendarios del usuario. Para tener acceso al calendario del usuario en el contexto de una aplicación, debe agregar el permiso delegado `Calendars.Read` a la información del registro de la aplicación y, a continuación, agregar el ámbito `Calendars.Read` a la llamada a `acquireTokenSilent`. Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

Si una API de back-end no requiere un ámbito (no se recomienda), puede usar el valor de `clientId` como ámbito en las llamadas a `acquireTokenSilent` o `acquireTokenPopup`.

<!--end-collapse-->
