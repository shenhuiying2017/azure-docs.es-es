

1. Inicie sesión en el Portal de administración de Azure, haga clic en **Servicios móviles** y, a continuación, seleccione su servicio móvil.

2. Haga clic en la pestaña **API** y, a continuación, en **Crear**. Esto muestra el cuadro de diálogo **Crear una API personalizada**.

3. Escriba _completeall_ en el **nombre de la API** y, a continuación, haga clic en el botón de comprobación para crear la nueva API.

	> [AZURE.NOTE]Con los permisos predeterminados, cualquier usuario con la clave de la aplicación puede llamar a la API personalizada. Sin embargo, la clave de aplicación no se considera una credencial segura porque no se puede distribuir o almacenar de manera segura. Considere la posibilidad de restringir el acceso únicamente a usuarios autenticados para mayor seguridad.

4. Haga clic en **completeall** en la tabla de la API.

5. Haga clic en la pestaña **Script**, reemplace el código existente por el código siguiente y, a continuación, haga clic en **Guardar**. Este código usa el [objeto mssql] para acceder a la tabla **todoitem** directamente a fin de establecer la marca `complete` en todos los elementos. Debido a que se usa la función **exports.post**, los clientes envían una solicitud POST para realizar la operación. La cantidad de filas cambiadas se devuelve al cliente como un valor entero.


		exports.post = function(request, response) {
			var mssql = request.service.mssql;
			var sql = "UPDATE todoitem SET complete = 1 " +
                "WHERE complete = 0; SELECT @@ROWCOUNT as count";
			mssql.query(sql, {
				success: function(results) {
					if(results.length == 1)
						response.send(200, results[0]);
				}
			})
		};



> [AZURE.NOTE]Los objetos <a href="http://msdn.microsoft.com/library/windowsazure/jj554218.aspx" target="_blank">request</a> y <a href="http://msdn.microsoft.com/library/windowsazure/dn303373.aspx" target="_blank">response</a> que se proporcionan a las funciones de API personalizada se implementan mediante la <a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">biblioteca de Express.js</a>. Para obtener más información, consulte <a href="http://msdn.microsoft.com/library/windowsazure/dn280974.aspx" target="_blank">API personalizada</a>.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[objeto mssql]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx

<!---HONumber=July15_HO1-->