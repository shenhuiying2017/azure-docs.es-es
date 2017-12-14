
## <a name="register-your-application"></a>Registrar su aplicación

Hay numerosas maneras de crear una aplicación, seleccione una de ellas:

### <a name="option-1-register-your-application-express-mode"></a>Opción 1: Registro de la aplicación (modo Rápido)
Ahora tiene que registrar la aplicación en el *Portal de registro de aplicaciones de Microsoft*:

1.  Registre la aplicación en el [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Escriba el nombre de la aplicación y su correo electrónico.
3.  Asegúrese de que está activada la opción de *configuración paso a paso*.
4.  Siga las instrucciones para obtener el identificador de aplicación y péguelo en el código.

### <a name="option-2-register-your-application-advanced-mode"></a>Opción 2: Registro de la aplicación (modo Avanzado)

1. Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar una aplicación.
2. Escriba el nombre de la aplicación y su correo electrónico. 
3. Asegúrese de que está desactivada la opción de *configuración paso a paso*.
4.  Haga clic en `Add Platform` y luego en `Web`.
5. Agregue la `Redirect URL` que corresponda a la dirección URL de la aplicación basada en el servidor web. Consulte las secciones siguientes para obtener instrucciones sobre cómo establecer y obtener la dirección URL de redireccionamiento en Visual Studio y Python.
6. Haga clic en *Guardar*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Instrucciones en Visual Studio para obtener la dirección URL de redireccionamiento
> Siga las instrucciones para obtener la dirección URL de redireccionamiento:
> 1.    En el *Explorador de soluciones*, seleccione el proyecto y fíjese en la ventana `Properties` (si no ve una ventana de propiedades, presione `F4`)
> 2.    Copie el valor de `URL` en el Portapapeles:<br/> ![Propiedades del proyecto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Vuelva al *Portal de registro de aplicaciones*, pegue el valor como `Redirect URL` y haga clic en "Guardar".

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Configuración de la dirección URL de redireccionamiento para Python
> Para Python, puede establecer el puerto de servidor web a través de una línea de comandos. Esta configuración paso a paso usa el puerto 8080 como referencia, pero puede usar cualquier otro puerto que esté disponible. Cualquiera sea el caso, siga las instrucciones siguientes para configurar una dirección URL de redireccionamiento en la información de registro de aplicación:<br/>
> - Vuelva al *Portal de registro de aplicaciones* y establezca `http://localhost:8080/` como `Redirect URL`, o utilice `http://localhost:[port]/` si usa un puerto TCP personalizado (donde *[port]* es el número de puerto TCP personalizado) y haga clic en "Guardar".


#### <a name="configure-your-javascript-spa"></a>Configuración de JavaScript SPA

1.  Cree un archivo denominado `msalconfig.js` que contenga la información de registro de la aplicación. Si se usa Visual Studio, seleccione el proyecto (carpeta raíz del proyecto), haga clic con el botón derecho y seleccione: `Add` > `New Item` > `JavaScript File`. Asígnele el nombre `msalconfig.js`.
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
