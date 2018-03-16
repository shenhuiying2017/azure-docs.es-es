
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Configuración de la aplicación web ASP.NET con la información de registro de la aplicación

En este paso, se configura el proyecto para usar SSL y, a continuación, se usa la dirección URL de SSL para configurar la información de registro de la aplicación. Después, se agrega la información de registro de la aplicación a la solución a través de *web.config*.

1.  En el Explorador de soluciones, seleccione el proyecto y fíjese en la ventana `Properties` (si no ve una ventana de propiedades, presione F4).
2.  Cambie `SSL Enabled` a `True`.
3.  Copie el valor de `SSL URL` anterior y péguelo en el campo `Redirect URL` en la parte superior de esta página; a continuación, haga clic en *Actualizar*:<br/><br/>![Propiedades del proyecto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4.  Agregue lo siguiente en el archivo `web.config` ubicado en la carpeta raíz, en la sección `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
