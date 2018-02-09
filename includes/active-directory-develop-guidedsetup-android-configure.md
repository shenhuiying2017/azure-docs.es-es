
## <a name="register-your-application"></a>Registrar su aplicación
Puede registrar su aplicación de dos maneras distintas, como se describe en las dos secciones siguientes.

### <a name="option-1-express-mode"></a>Opción 1: Modo rápido
Puede registrar rápidamente la aplicación mediante estos pasos:
1. Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  En el cuadro**Application Name** (Nombre de la aplicación) escriba el nombre de su aplicación.

3. Asegúrese de que la casilla **Guided Setup** (Instalación guiada) esté activada y, luego, seleccione **Create** (Crear).

4. Siga las instrucciones para obtener el identificador de aplicación y péguelo en el código.

### <a name="option-2-advanced-mode"></a>Opción 2: Modo avanzado
Para registrar la aplicación y agregar la información de registro de aplicación a la solución, siga estos pasos:
1. Si aún no ha registrado la aplicación, vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. En el cuadro**Application Name** (Nombre de la aplicación) escriba el nombre de su aplicación. 

3. Asegúrese de que la casilla **Guided Setup** (Instalación guiada) esté desactivada y, luego, seleccione **Create** (Crear).

4. Seleccione **Add Platform** (Agregar plataforma), seleccione **Native Application** (Aplicación nativa) y, luego, seleccione **Save** (Guardar).

5. En **app** > **java** > **{host}.{namespace}**, abra `MainActivity`. 

6.  Reemplace *[escriba el identificador de aplicación aquí]* en la línea siguiente con el identificador de aplicación que acaba de registrar:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. En **app** > **manifests**, abra el archivo *AndroidManifest.xml*.

8. En el nodo `manifest\application`, agregue la actividad siguiente. Al hacerlo, se registra una actividad `BrowserTabActivity` que permite al sistema operativo reanudar la aplicación una vez completada la autenticación:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
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
9. En el nodo `BrowserTabActivity`, reemplace `[Enter the application Id here]` por el identificador de aplicación.
