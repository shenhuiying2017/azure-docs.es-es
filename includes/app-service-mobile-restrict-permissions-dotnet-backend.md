

De forma predeterminada, los extremos definidos en su aplicación móvil se exponen públicamente. Para proteger los recursos, debe restringir el acceso únicamente a los clientes autenticados.

1. En Visual Studio, abra el proyecto que contiene el código de la aplicación móvil. 

2. En el Explorador de soluciones, expanda la carpeta Controladores y abra el archivo de proyecto TodoItemController.cs.

	La clase **TodoItemController** implementa el acceso a los datos para la tabla TodoItem.

3. Aplique el atributo `Authorize` a la clase **TodoItemController**:

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

	De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado.

	>[AZURE.NOTE]Aplique el atributo Authorize a métodos individuales para establecer los niveles de autorización específicos en los métodos expuestos por el controlador.

4. Volver a publicar un proyecto de aplicación móvil.

<!---HONumber=July15_HO4-->