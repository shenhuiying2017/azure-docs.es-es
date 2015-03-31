

De manera predeterminada, todas las solicitudes a recursos de servicios móviles están restringidas a clientes que presentan la clave de aplicación, lo que no protege estrictamente el acceso a los recursos. Para proteger los recursos, debe restringir el acceso solo a los clientes autenticados.

1. En Visual Studio, abra el proyecto de servicio móvil, expanda la carpeta Controllers y abra **TodoItemController.cs**. La clase **TodoItemController** implementa el acceso a los datos para la tabla TodoItem. Agregue la siguiente instrucción `using`:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. Aplique el siguiente atributo _AuthorizeLevel_ a la clase **TodoItemController**. Esto garantiza que todas las operaciones frente a la tabla _TodoItem_ requieren un usuario autenticado.

		[AuthorizeLevel(AuthorizationLevel.User)]

	>[AZURE.NOTE]Aplique el atributo AuthorizeLevel a métodos individuales para establecer los niveles de autorización específicos en los métodos expuestos por el controlador.

3. Si desea depurar la autenticación localmente, expanda la carpeta  `App_Start`, abra **WebApiConfig.cs** y agregue el código siguiente al método **Registrar**.  

		config.SetIsHosted(true);

	Esto indica al proyecto del servicio móvil local que se ejecute como si estuviera hospedado en Azure, incluyendo la autorización a la configuración de  *AuthorizeLevel*. Sin esta configuración, todas las solicitudes HTTP a localhost se permiten sin autenticación, a pesar de la configuración de  *AuthorizeLevel*. 

4. Vuelva a publicar el proyecto.

<!--HONumber=47-->
