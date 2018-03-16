
## <a name="add-the-applications-registration-information-to-your-app"></a>Adición de información de registro de la aplicación a su aplicación

En este paso, debe agregar el identificador de cliente a su proyecto.

1.  Abra `MainActivity` (en `app` > `java` > *`{host}.{namespace}`*)
2.  Reemplace la línea que empieza con `final static String CLIENT_ID` por:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Abra `app` > `manifests` > `AndroidManifest.xml`
4. Agregue la siguiente actividad al nodo `manifest\application`. De este modo se registra un `BrowserTabActivity` para permitir que el sistema operativo reanude la aplicación después de completar la autenticación:

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

