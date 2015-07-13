

De manera predeterminada, todas las solicitudes a los recursos de la aplicación móvil del servicio de la aplicación están restringidas a los clientes que presenten la clave de aplicación, que no protege estrictamente el acceso a los recursos. Para proteger los recursos, debe restringir el acceso únicamente a los clientes autenticados.

1. En Visual Studio, abra el proyecto que contiene el código de la aplicación móvil. 

2. En el Explorador de soluciones, expanda la carpeta Controladores y abra el archivo de proyecto TodoItemController.cs.

	La clase **TodoItemController** implementa el acceso a los datos para la tabla TodoItem.

3. Agregue la siguiente instrucción `using` en la parte superior de la página de código:

		using Microsoft.Azure.Mobile.Security;

4. Aplique el siguiente atributo _AuthorizeLevel_ a la clase **TodoItemController**:

		[AuthorizeLevel(AuthorizationLevel.User)] 

	De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado.

	>[AZURE.NOTE]Aplique el atributo AuthorizeLevel a métodos individuales para establecer los niveles de autorización específicos en los métodos expuestos por el controlador.

5. Si desea depurar la autenticación localmente, expanda la carpeta App_Start, abra el archivo de proyecto WebApiConfig.cs y, a continuación, agregue el código siguiente al método **Register**:

		config.SetIsHosted(true);
	
	Esto indica al proyecto local que se ejecute como si estuviera hospedado en Azure, incluyendo la autorización a la configuración de AuthorizeLevel. Sin esta configuración, todas las solicitudes HTTP a *localhost* se permiten sin autenticación, a pesar de la configuración de AuthorizeLevel.

6. Volver a publicar un proyecto de aplicación móvil.

<!---HONumber=62-->