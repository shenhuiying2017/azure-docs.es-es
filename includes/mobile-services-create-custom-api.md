

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su aplicación.

	![](./media/mobile-services-create-custom-api/mobile-services-selection.png)

2. Haga clic en la pestaña **API** y, a continuación, haga clic en **Crear una API personalizada**.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-create.png)

	Esto muestra el cuadro de diálogo **Crear una API personalizada**.

3. Escriba _completeall_ en **nombre de la API** y haga clic en el botón de comprobación.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png)

	De esta manera, se crea la API nueva.

	> [AZURE.NOTE] Los permisos predeterminados están establecidos, lo que significa que cualquier usuario de la aplicación puede llamar a la API personalizada. No obstante, la clave de la aplicación no se distribuye ni almacena de forma segura y no se puede considerar una credencial segura. Por ello, debe considerar restringir el acceso solo a los usuarios autenticados en las operaciones que modifican datos o afectan al servicio móvil.

4. Haga clic en la entrada nueva **completeall** en la tabla de API.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-select2.png)

5. Haga clic en la pestaña **Script**, reemplace el código existente por el código siguiente y, a continuación, haga clic en **Save**:

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


	Este código usa el [objeto mssql] para obtener acceso a la tabla **todoitem** directamente a fin de establecer la marca completada en todos los elementos. Debido a que se usa la función **exports.post**, los clientes envían una solicitud POST para realizar la operación. La cantidad de filas cambiadas se devuelve al cliente como un valor entero.

> [AZURE.NOTE]
> Los objetos <a href="http://msdn.microsoft.com/es-es/library/windowsazure/jj554218.aspx" target="_blank">request</a> y <a href="http://msdn.microsoft.com/es-es/library/windowsazure/dn303373.aspx" target="_blank">response</a> que proporcionen las funciones de API personalizada se implementan mediante la <a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">biblioteca de Express.js</a>. Para obtener más información, consulte <a href="http://msdn.microsoft.com/es-es/library/windowsazure/dn280974.aspx" target="_blank">API personalizada</a>. 

A continuación, podrá modificar la aplicación de inicio rápido para agregar un botón y código nuevos que llame de forma asincrónica a la nueva API personalizada.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Portal de administración de Azure]: https://manage.windowsazure.com/
[objeto mssql]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554212.aspx

<!--HONumber=42-->
