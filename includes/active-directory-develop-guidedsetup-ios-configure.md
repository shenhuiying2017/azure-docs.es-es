
## <a name="create-an-application-express"></a>Creación de una aplicación (proceso rápido)
Ahora tiene que registrar la aplicación en el *Portal de registro de aplicaciones de Microsoft*:
1. Registre la aplicación en el [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure).
2.  Escriba el nombre de la aplicación y su correo electrónico.
3.  Asegúrese de que está activada la opción de configuración paso a paso.
4.  Siga las instrucciones para obtener el identificador de aplicación y péguelo en el código.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adición de la información de registro de la aplicación a la solución (avanzado)

1.  Vaya al [portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2.  Escriba el nombre de la aplicación y su correo electrónico.
3.  Asegúrese de que está desactivada la opción de configuración paso a paso.
4.  Haga clic en `Add Platform`, seleccione `Native Application` y haga clic en `Save`.
5.  Vuelva a Xcode. En `ViewController.swift`, reemplace la línea que empieza por '`let kClientID`' por el identificador de aplicación que acaba de registrar:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Presione Control y haga clic en <code>Info.plist</code> para mostrar el menú contextual y, a continuación, haga clic en: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
Agregue lo siguiente bajo el nodo raíz <code>dict</code>:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Reemplace <i><code>[Your_Application_Id_Here]</code></i> por el identificador de aplicación que acaba de registrar.
</li>
</ol>
