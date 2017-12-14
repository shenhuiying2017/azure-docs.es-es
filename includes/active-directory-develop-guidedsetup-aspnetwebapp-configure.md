
## <a name="create-an-application-express"></a>Creación de una aplicación (proceso rápido)
Ahora tiene que registrar la aplicación en el *Portal de registro de aplicaciones de Microsoft*:
1. Registre la aplicación en el [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2.  Escriba el nombre de la aplicación y su correo electrónico.
3.  Asegúrese de que está activada la opción de configuración paso a paso.
4.  Siga las instrucciones para agregar una dirección URL de redireccionamiento a la aplicación.

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adición de la información de registro de la aplicación a la solución (avanzado)
Ahora tiene que registrar la aplicación en el *Portal de registro de aplicaciones de Microsoft*:
1. Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar una aplicación.
2. Escriba el nombre de la aplicación y su correo electrónico. 
3.  Asegúrese de que está desactivada la opción de configuración paso a paso.
4.  Haga clic en `Add Platform` y luego en `Web`.
5.  Vuelva a Visual Studio y, en el Explorador de soluciones, seleccione el proyecto y fíjese en la ventana de propiedades (si no ve una ventana de propiedades, presione F4).
6.  Cambie SSL habilitado a `True`.
7.  Copie la dirección URL de SSL y agréguela a la lista de direcciones URL de redireccionamiento en la lista del Portal de registro de direcciones URL de redireccionamiento:<br/><br/>![Propiedades del proyecto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
8.  Agregue lo siguiente en `web.config`, ubicado en la carpeta raíz en la sección `configuration\appSettings`:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
Reemplace `ClientId` por el identificador de aplicación que acaba de registrar.
</li>
<li>
Reemplace `redirectUri` por la dirección URL de SSL del proyecto.
</li>
</ol>
<!-- End Docs -->
