
## <a name="create-an-application-express"></a>Creación de una aplicación (proceso rápido)
Ahora tiene que registrar la aplicación en el *Portal de registro de aplicaciones de Microsoft*:
1. Registre la aplicación en el [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  Escriba el nombre de la aplicación y su correo electrónico.
3.  Asegúrese de que está activada la opción de configuración paso a paso.
4.  Siga las instrucciones para obtener el identificador de aplicación y péguelo en el código.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adición de la información de registro de la aplicación a la solución (avanzado)
Ahora tiene que registrar la aplicación en el *Portal de registro de aplicaciones de Microsoft*:
1. Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar una aplicación.
2. Escriba el nombre de la aplicación y su correo electrónico. 
3. Asegúrese de que está desactivada la opción de configuración paso a paso.
4. Haga clic en `Add Platform`, a continuación, seleccione `Native Application` y haga clic en Guardar.
5.  Abra `MainActivity` (en `app` > `java` > *`{host}.{namespace}`*)
6.  Reemplace *[escriba el id. de aplicación aquí]* en la línea que empieza con `final static String CLIENT_ID` por el identificador de aplicación que acaba de registrar:

```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Abra `AndroidManifest.xml` (en `app` > `manifests`) Agregue la actividad siguiente al nodo `manifest\application`. De este modo se registra un `BrowserTabActivity` para permitir que el sistema operativo reanude la aplicación después de completar la autenticación:
</li>
</ol>

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        
        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```
<!-- Workaround for Docs conversion bug -->
<ol start="8">
<li>
En `BrowserTabActivity`, reemplace `[Enter the application Id here]` por el identificador de aplicación.
</li>
</ol>
