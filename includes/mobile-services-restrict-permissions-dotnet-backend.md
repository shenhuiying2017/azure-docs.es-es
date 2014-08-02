

De manera predeterminada, todas las solicitudes a recursos de servicios móviles están restringidas a clientes que presentan la clave de aplicación, lo que no protege estrictamente el acceso a los recursos. Para proteger los recursos, debe restringir el acceso únicamente a los clientes autenticados.

1.  En Visual Studio, abra el proyecto que contiene el servicio móvil.

2.  En el Explorador de soluciones, expanda la carpeta Controladores y abra el archivo de proyecto TodoItemController.cs.

    La clase **TodoItemController** implementa el acceso de datos a la tabla TodoItem.

3.  Agregue la siguiente instrucción `using` en la parte superior de la página de código:

         using Microsoft.WindowsAzure.Mobile.Service.Security;

4.  Aplique el atributo AuthorizeLevel siguiente a la clase **TodoItemController**:

         [AuthorizeLevel(AuthorizationLevel.User)] 

    De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado.

    > [WACOM.NOTE]Aplique el atributo AuthorizeLevel a métodos individuales para establecer los niveles de autorización específicos en los métodos expuestos por el controlador.

5.  Expanda la carpeta App\_Start, abra el archivo de proyecto WebApiConfig.cs y, a continuación, agregue el código siguiente al método **Register**:

         config.SetIsHosted(true);

    Esto indica al proyecto del servicio móvil local que se ejecute como si estuviera hospedado en Azure, incluyendo la autorización a la configuración de AuthorizeLevel. Sin esta configuración, todas las solicitudes HTTP a *localhost* se permiten sin autenticación, a pesar de la configuración de AuthorizeLevel.

6.  Vuelva a publicar el proyecto de servicio.


