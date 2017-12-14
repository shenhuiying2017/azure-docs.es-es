
## <a name="create-an-application-express"></a>Creación de una aplicación (proceso rápido)
Ahora tiene que registrar la aplicación en el *Portal de registro de aplicaciones de Microsoft*:
1. Registre la aplicación en el [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).
2.  Escriba el nombre de la aplicación y su correo electrónico.
3.  Asegúrese de que está activada la opción de configuración paso a paso.
4.  Siga las instrucciones para obtener el identificador de aplicación y péguelo en el código.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adición de la información de registro de la aplicación a la solución (avanzado)
Ahora tiene que registrar la aplicación en el *Portal de registro de aplicaciones de Microsoft*:
1. Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar una aplicación.
2. Escriba el nombre de la aplicación y su correo electrónico. 
3. Asegúrese de que está desactivada la opción de configuración paso a paso.
4. Haga clic en `Add Platform`, a continuación, seleccione `Native Application` y haga clic en Guardar.
5. Copie el GUID en Id. de aplicación, vuelva a Visual Studio, abra `App.xaml.cs` y reemplace `your_client_id_here` por el identificador de aplicación que acaba de registrar:

```csharp
private static string ClientId = "your_application_id_here";
```
