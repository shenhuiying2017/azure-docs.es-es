
De forma predeterminada, se pueden invocar API en un back-end de aplicación móvil de forma anónima. A continuación, deberá restringir el acceso a solo los clientes autenticados.

1. En su equipo, abra el proyecto de servidor en Visual Studio y vaya a **Controladores** > **TodoItemController.cs**.

2. Agregue el atributo `[Authorize]` a la clase **TodoItemController** como sigue. Esto requiere que un usuario autenticado realice todas las operaciones con la tabla TodoItem. Para restringir el acceso solo a determinados métodos, también puede aplicar este atributo solo a esos métodos en lugar de la clase.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>
   
    Esto requiere que un usuario autenticado realice todas las operaciones con la tabla TodoItem. Para restringir el acceso solo a determinados métodos, también puede aplicar este atributo solo a esos métodos en lugar de la clase.
   
3. Vuelva a publicar el proyecto del servidor.

<!---HONumber=August15_HO8-->