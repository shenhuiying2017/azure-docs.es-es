La mayoría de las veces, los errores de autenticación proceden de opciones de configuración incorrectas o incoherentes. A continuación le indicamos algunas sugerencias específicas sobre los aspectos que deben comprobarse.

* Asegúrese de que no se haya olvidado el botón **Guardar** en alguna parte. Esto sucede a menudo y el resultado es que estará viendo los valores correctos en una página del portal, pero estos no se habrán guardado realmente en el entorno de Azure ni en la aplicación de Azure AD.
* Para las opciones configuradas en la hoja **Configuración de la aplicación** del Portal de Azure, asegúrese de seleccionar la aplicación de API o la aplicación web correcta cuando establezca la configuración. Además, asegúrese de que la configuración se haya definido en la sección **Configuración de la aplicación** y no en **Cadenas de conexión**, ya que el formato de las dos secciones es similar.
* Para la autenticación de un front-end de JavaScript, descargue el manifiesto de nuevo para comprobar que el valor `oauth2AllowImplicitFlow` se haya cambiado correctamente a `true`.
* Compruebe que haya usado HTTPS donde haya configurado direcciones URL:

	* En el código del proyecto
	* En CORS
	* En la configuración de la aplicación del entorno de Azure para cada aplicación de API y aplicación web
	* En la configuración de la aplicación de Azure AD
	
	Tenga en cuenta que, si copia una dirección URL de una aplicación de API desde el portal, a menudo contendrá el prefijo `http://`, que deberá cambiar manualmente por `https://`.

* Asegúrese de que todos los cambios de código se hayan implementado correctamente. Por ejemplo, en una solución de varios proyectos, es posible cambiar el código de un proyecto y elegir por error uno de los otros códigos al intentar implementar el cambio.
* Asegúrese de que va a direcciones URL HTTPS en el explorador, no a direcciones URL HTTP. De forma predeterminada, Visual Studio crea perfiles de publicación con direcciones URL HTTP, y eso es lo que se abre en el explorador después de implementar un proyecto.
* Para la autenticación de un front-end de JavaScript, asegúrese de que CORS esté configurado correctamente en la aplicación de API a la que llama el código JavaScript. Si tiene dudas acerca de si el problema está relacionado con CORS, pruebe a usar "*" como dirección URL de origen permitida.
* Para un front-end de JavaScript, abra la pestaña Consola de Developer Tools del explorador para obtener más información sobre los errores y examinar las solicitudes HTTP en la red. Sin embargo, los mensajes de error de la consola pueden ser confusos. Si recibe un mensaje que indica que se ha producido un error de CORS, el problema real puede ser la autenticación. Puede comprobar si es así ejecutando la aplicación y deshabilitando la autenticación temporalmente.
* Para una aplicación de API de .NET, asegúrese de que obtiene tanta información como sea posible en los mensajes de error estableciendo el [modo customErrors como Off (desactivado)](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Para una aplicación de API de . NET, inicie una [sesión de depuración remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) y examine los valores de las variables que se pasan al código que usa ADAL para adquirir un token de portador o al código que comprueba las reclamaciones dirigidas al identificador de entidad de servicio esperado. Tenga en cuenta que el código puede tomar valores de configuración de muchos orígenes diferentes, por lo que es posible encontrarse sorpresas como esta. Por ejemplo, si escribe `ida:ClientId` de forma incorrecta como `ida:ClientID` al configurar el entorno del Servicio de aplicaciones de Azure, el código podría obtener el valor `ida:ClientId` que está buscando en el archivo Web.config y omitir la configuración del Servicio de aplicaciones de Azure. 
* Si los procesos no funcionan en una ventana normal de Internet Explorer, podría estar interfiriendo un inicio de sesión existente. Inténtelo con una sesión de exploración de InPrivate o pruébelo en Chrome o Firefox.

<!---HONumber=AcomDC_0309_2016-->