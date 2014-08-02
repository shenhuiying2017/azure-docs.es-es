

1.  En Visual Studio, abra el proyecto que ha modificado al completar el tutorial **Introducción a los datos**.

2.  Presione la tecla **F5** para ejecutar la aplicación y, a continuación, escriba texto en **Insert a TodoItem** y haga clic en **Guardar**.

3.  Repita el paso anterior al menos tres veces de manera que disponga de más de tres elementos almacenados en la tabla TodoItem.

4.  En el archivo default.js, reemplace el método **RefreshTodoItems** por el siguiente código:

         var refreshTodoItems = function () {
             // Definir una consulta filtrada que devuelva los tres primeros elementos.
             todoTable.where({ complete: false })
                 .take(3)
                 .read()
                 .done(function (results) {
                     todoItems = new WinJS.Binding.List(results);
                     listItems.winControl.itemDataSource = todoItems.dataSource;
                 });
         };

	Cuando se ejecuta durante el enlace de datos, esta consulta devuelve los tres elementos principales que no están marcados como completados.

1.  Presione la tecla **F5** para ejecutar la aplicación.

	Observe que solo se muestran los tres primeros resultados de la tabla TodoItem.

1.  (Opcional) Vea el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler](http://go.microsoft.com/fwlink/?LinkID=262412).

	Observe que el método **take(3)** se ha traducido a la opción de consulta **$top=3** en el URI de la consulta.

2.  Actualice el método **RefreshTodoItems** una vez más con el código siguiente:

         var refreshTodoItems = function () {
             // Definir una consulta filtrada que omite los tres primeros elementos y, a continuación, 
             // devuelve los 3 elementos siguientes.
             todoTable.where({ complete: false })
                 .skip(3)
                 .take(3)
                 .read()
                 .done(function (results) {
                     todoItems = new WinJS.Binding.List(results);
                     listItems.winControl.itemDataSource = todoItems.dataSource;
                 });
         };

	Esta consulta omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

    **Nota:**

    Este tutorial usa un escenario simplificado para pasar valores de paginación codificados de forma rígida a los métodos **Take** y **Skip**. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores. También puede llamar al método **includeTotalCount** para obtener el recuento total de elementos disponibles en el servidor, junto con los datos paginados.

3.  (Opcional) Vea de nuevo el URI de la solicitud enviada al servicio móvil.

	Observe que el método **skip(3)** se ha traducido a la opción de consulta **$skip=3** en el URI de la consulta.


