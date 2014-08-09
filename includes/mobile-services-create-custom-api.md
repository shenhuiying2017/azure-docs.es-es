1.  Inicie sesión en el [Portal de administración de Azure][1], haga clic en **Servicios móviles** y, a continuación, en su aplicación.
    
    ![](./media/mobile-services-create-custom-api/mobile-services-selection.png)

2.  Haga clic en la pestaña **API** y, a continuación, haga clic en **Create a custom API**.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-create.png)
    
    Esto muestra el cuadro de diálogo **Create a new custom API**.

3.  Escriba *completeall* en **API name** y, a continuación, haga clic en el botón de comprobación.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png)
    
    De esta manera, se crea la API nueva.


   
    > [WACOM.NOTE] Los permisos predeterminados están establecidos, lo que significa que cualquier usuario de la aplicación puede llamar a la API personalizada. No obstante, la clave de la aplicación no se distribuye ni almacena de forma segura y no se puede considerar una credencial segura. Por ello, debe considerar restringir el acceso solo a los usuarios autenticados en las operaciones que modifican datos o afectan al servicio móvil.

4.  Haga clic en la entrada nueva **completeall** en la tabla de API.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-select2.png)

5.  Haga clic en la pestaña **Script**, reemplace el código existente por el código siguiente y, a continuación, haga clic en **Save**:
    
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
    
    Este código usa el [objeto mssql][2] para obtener acceso a la tabla **todoitem** directamente a fin de establecer la marca completada en todos los elementos. Debido a que se usa la función **exports.post**, los clientes envían una solicitud POST para realizar la operación. La cantidad de filas cambiadas se devuelve al cliente como un valor entero.

> [WACOM.NOTE] 
El valor [request (objeto)][3] y [response (objeto)][4] suministrado a las funciones de API personalizada se
implementan mediante la [biblioteca de Express.js (en inglés)][5]. Para obtener más información,
consulte [API personalizado][6].

A continuación, podrá modificar la aplicación de inicio rápido para agregar un botón y código nuevos que llame de forma asincrónica a la nueva API personalizada.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->



[1]: https://manage.windowsazure.com/
[2]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554212.aspx
[3]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554218.aspx
[4]: http://msdn.microsoft.com/es-es/library/windowsazure/dn303373.aspx
[5]: http://go.microsoft.com/fwlink/p/?LinkId=309046
[6]: http://msdn.microsoft.com/es-es/library/windowsazure/dn280974.aspx