
Para proteger los extremos, debe restringir el acceso a solo los clientes autenticados.

1. En el [Portal de administración de Azure](https://manage.windowsazure.com/), vaya al servicio móvil, haga clic en **Datos** > el nombre de la tabla (**TodoItem**) > **Permisos**. 

2. Establezca todos los permisos de operación de la tabla en **Solo usuarios autenticados**.

	 De esta forma, se garantiza que todas las operaciones contra la tabla requieren un usuario autenticado, que es necesario para este tutorial. Puede establecer permisos diferentes en cada operación para admitir su escenario concreto.

<!---HONumber=Oct15_HO3-->