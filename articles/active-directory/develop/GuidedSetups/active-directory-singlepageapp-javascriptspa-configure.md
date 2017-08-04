
### <a name="create-an-application-express"></a>Creación de una aplicación (proceso rápido)
Ahora tiene que registrar la aplicación en el Portal de registro de aplicaciones de Microsoft:

1.  Registre la aplicación en el [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Escriba el nombre de la aplicación y su correo electrónico.
3.  Asegúrese de que está activada la opción de *configuración paso a paso*.
4.  Siga las instrucciones para obtener el identificador de aplicación y péguelo en el código.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adición de la información de registro de la aplicación a la solución (avanzado)

1. Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar una aplicación.
2. Escriba el nombre de la aplicación y su correo electrónico. 
3. Asegúrese de que está desactivada la opción de *configuración paso a paso*.
4.  Haga clic en `Add Platform` y luego en `Web`.
5. Agregue una dirección URL de redireccionamiento a la aplicación. Una dirección URL de redireccionamiento es la dirección URL de su página `index.html` basada en el servidor web
6. Haga clic en *Guardar*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>Instrucciones en Visual Studio para obtener la dirección URL de redireccionamiento mediante SSL
> Si usa Visual Studio, configure el proyecto para usar SSL y, a continuación, use la dirección URL de SSL para configurar la información de registro de la aplicación siguiendo las instrucciones a continuación:
> 1.    En el Explorador de soluciones, seleccione el proyecto y fíjese en la ventana `Properties` (si no ve una ventana de propiedades, presione F4).
> 2.    Cambie `SSL Enabled` a `True`.
> 3.    Copie el valor de `SSL URL` en el Portapapeles:<br/> ![Propiedades del proyecto](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    Seleccione el menú `Project` y, a continuación, seleccione `{Project} Properties...`, donde {Project} es el nombre del proyecto
> 5.    Abra la pestaña `Web`
> 6.    Pegue el valor de `SSL URL` en el campo `Project Url`
> 7.    Vuelva al Portal de registro de aplicaciones y pegue el valor de `Redirect URL` como dirección URL de redireccionamiento; a continuación, haga clic en *Guardar*


#### <a name="configure-your-javascript-spa-application"></a>Configuración de la aplicación JavaScript SPA

1.  Cree un archivo denominado `msalconfig.js` que contenga la información de registro de aplicación. Si se utiliza Visual Studio, seleccione el proyecto (carpeta raíz del proyecto), haga clic con el botón derecho y seleccione: `Add`  >  `New Item`  >  `JavaScript File`. Asígnele el nombre `msalconfig.js`.
2.  Agregue el siguiente código al archivo `msalconfig.js`:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Reemplace <code>Enter_the_Application_Id_here</code> por el identificador de aplicación que acaba de registrar.
</li>
</ol>