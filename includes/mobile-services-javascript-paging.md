1.  En Visual Studio, abra el proyecto que ha modificado al completar el tutorial **Introducción a los datos**.

2.  Presione la tecla **F5** para ejecutar la aplicación y, a continuación, escriba texto en **Insert a TodoItem** y haga clic en **Guardar**.

3.  Repita el paso anterior al menos tres veces de manera que disponga de más de tres elementos almacenados en la tabla TodoItem.

4.  En el archivo default.js, reemplace el método **RefreshTodoItems** por el siguiente código:

        var refreshTodoItems = function () {
            // Define a filtered query that returns the top 3 items.
            todoTable.where({ complete: false })
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

    Cuando se ejecuta durante el enlace de datos, esta consulta devuelve los tres elementos principales que no están marcados como completados.

5.  Presione la tecla **F5** para ejecutar la aplicación.

    Observe que solo se muestran los tres primeros resultados de la tabla TodoItem.

6.  (Opcional) Vea el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler][Fiddler].

    Observe que el método **take(3)** se ha traducido en la opción de consulta **$top=3** en el URI de la consulta.

7.  Actualice el método **RefreshTodoItems** una vez más con el código siguiente:

        var refreshTodoItems = function () {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
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

    <div class="dev-callout"><b>Nota:</b>
<p>Este tutorial usa un escenario simplificado para pasar valores de paginaci&oacute;n codificados de forma r&iacute;gida a los m&eacute;todos <strong>Take</strong> y <strong>Skip</strong>. En una aplicaci&oacute;n en tiempo real, puede usar consultas similares a las anteriores con un control de paginaci&oacute;n o interfaz de usuario comparable para permitir a los usuarios desplazarse a las p&aacute;ginas anteriores y posteriores.  Tambi&eacute;n puede llamar al m&eacute;todo <strong>includeTotalCount</strong> para obtener el recuento total de elementos disponibles en el servidor, junto con los datos paginados.</p>
</div>

8.  (Opcional) Vea de nuevo el URI de la solicitud enviada al servicio móvil.

    Observe que el método **skip(3)** se ha traducido en la opción de consulta **$skip=3** en el URI de la consulta.

<!-- URLs -->

  [Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412
