## <a name="test-your-code"></a>Prueba del código

1. Implemente el código en el dispositivo/emulador.

2. Cuando esté listo para probar la aplicación, use una cuenta de Azure Active Directory (cuenta profesional o educativa) o una cuenta Microsoft (live.com, outlook.com) para iniciar sesión. 

    ![Prueba de la aplicación](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Introducción del nombre de usuario y contraseña](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>Consentimiento para el acceso a la aplicación
La primera vez que inicie sesión en su aplicación, también se le pedirá que dé su consentimiento para permitir que la aplicación acceda a su perfil e inicie sesión, como se muestra a continuación: 

![Consentimiento para el acceso a la aplicación](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>Visualización de los resultados de la aplicación
Después de iniciar la sesión, debería ver los resultados devueltos por la llamada a Microsoft Graph API. La llamada al punto de conexión **me** de Microsoft Graph API devuelve el [perfil de usuario](https://graph.microsoft.com/v1.0/me). Para obtener una lista de puntos de conexión comunes de Microsoft Graph, consulte la [documentación para desarrolladores de Microsoft Graph API](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Más información sobre los ámbitos y permisos delegados

Microsoft Graph API requiere el ámbito *user.read* para leer el perfil del usuario. Este ámbito se agrega automáticamente de forma predeterminada en todas las aplicaciones que se van a registrar en el Portal de registro de aplicaciones. Otras API de Microsoft Graph, así como las API personalizadas para el servidor back-end, pueden requerir ámbitos adicionales. Microsoft Graph API requiere el ámbito *Calendars.read* para mostrar los calendarios del usuario. 

Para tener acceso a los calendarios del usuario en el contexto de una aplicación, agregue el permiso delegado *Calendars.Read* a la información del registro de la aplicación. A continuación, agregue el ámbito *Calendars.Read* a la llamada `acquireTokenSilent`. 

>[!NOTE]
>Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

<!--end-collapse-->

[!INCLUDE  [Help and support](active-directory-develop-help-support-include.md)]
