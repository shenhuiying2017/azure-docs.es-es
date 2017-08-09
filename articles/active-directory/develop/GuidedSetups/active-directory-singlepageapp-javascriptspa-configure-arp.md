
## <a name="add-the-applications-registration-information-to-your-app"></a>Adición de información de registro de la aplicación a su aplicación

En este paso, debe configurar la URL de redireccionamiento de la información de registro de su aplicación y, a continuación, agregar el identificador de la aplicación a la aplicación JavaScript SPA.

### <a name="configure-redirect-url"></a>Configuración de la URL de redireccionamiento

Configure el campo `Redirect URL` superior con la dirección URL de su página index.html basada en su servidor web y, a continuación, haga clic en *Actualizar*.

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>Instrucciones en Visual Studio para obtener la URL de redireccionamiento mediante SSL
> Si usa Visual Studio, configure el proyecto para usar SSL y, a continuación, use la dirección URL de SSL para configurar la información de registro de la aplicación siguiendo las instrucciones a continuación:
> 1.    En el Explorador de soluciones, seleccione el proyecto y fíjese en la ventana `Properties` (si no ve una ventana de propiedades, presione F4).
> 2.    Cambie `SSL Enabled` a `True`
> 3.    Copie el valor de `SSL URL` en el Portapapeles:<br/> ![Propiedades del proyecto](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    Seleccione el menú `Project` y, a continuación, seleccione `{Project} Properties...`, donde {Project} es el nombre del proyecto
> 5.    Abra la pestaña `Web`
> 6.    Pegue el valor de `SSL URL` en el campo `Project Url`
> 7.    Pegue también el valor en el campo `Redirect URL` en la parte superior de esta página; a continuación, haga clic en *Actualizar*


### <a name="configure-your-javascript-spa-application"></a>Configuración de la aplicación JavaScript SPA

1.  Cree un archivo denominado `msalconfig.js` que contenga la información de registro de la aplicación. Si se utiliza Visual Studio, seleccione el proyecto (carpeta raíz del proyecto), haga clic con el botón derecho y seleccione: `Add` > `New Item` > `JavaScript File`. Asígnele el nombre `msalconfig.js`.
2.  Agregue el siguiente código al archivo `msalconfig.js`:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
