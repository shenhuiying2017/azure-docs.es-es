<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-html" urlDisplayName="Validate Data - HTML5" pageTitle="User server scripts to validate and modify data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your HTML app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor
=============================================================================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

En este tema se muestra cómo aprovechar los scripts del servidor en Servicios móviles de Azure. Dichos scripts se registran en un servicio móvil y pueden usarse para realizar una gran variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. En este tutorial, definirá y registrará scripts de servidor que sirven para validar y modificar datos. Dado que el comportamiento de los scripts del servidor suele afectar al cliente, también actualizará la aplicación HTML para que se beneficie de estos nuevos comportamientos.

Este tutorial le guiará a través de estos pasos básicos:

1.  [Incorporación de la validación de longitud de cadena](#string-length-validation)
2.  [Actualización del cliente para admitir la validación](#update-client-validation)
3.  [Incorporación de una marca de tiempo al insertar](#add-timestamp)
4.  [Actualización del cliente para mostrar la marca de tiempo](#update-client-timestamp)

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-html). Antes de comenzar este tutorial, primero debe completar [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-html).

Incorporación de la validación
------------------------------

Siempre es conveniente validar la longitud de los datos enviados por los usuarios. En primer lugar, registre un script que valide la longitud de los datos de cadena enviados al servicio móvil y que rechace las cadenas demasiado largas, en este caso con más de 10 caracteres.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su aplicación.

  ![][0]

2.  Haga clic en la pestaña **Data** y, a continuación, haga clic en la tabla **TodoItem**.

  ![][1]

3.  Haga clic en **Script** y, a continuación, seleccione la operación **Insert**.

  ![][2]

4.  Sustituya el script existente por la siguiente función y, a continuación, haga clic en **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, {
                     error: "Text cannot exceed 10 characters"
                 });
             } else {
                 request.execute();
             }
         }

    Este script comprueba la longitud de la propiedad **TodoItem.text** y envía una respuesta de error cuando esta sobrepasa los 10 caracteres. En caso de no sobrepasarlos, se llama a la función **execute** para completar la inserción.

    **Nota:**

    Puede quitar un script registrado en la pestaña **Script** haciendo clic en **Clear** y, a continuación, en **Save**.

Actualización del cliente
-------------------------

Ahora que el servicio móvil puede validar los datos y enviar respuestas de error, debe actualizar la aplicación para que pueda identificar los errores de la validación.

1.  Ejecute uno de los siguientes archivos de comando desde la subcarpeta **server** del proyecto que modificó cuando completó el tutorial [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-html).

    -   **launch-windows** (equipos con Windows)
    -   **launch-mac.command** (equipos con Mac OS X)
    -   **launch-linux.sh** (equipos con Linux)

    **Nota:**

    En un equipo con Windows, escriba "R" cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.

    De este modo se inicia un servidor web en su equipo local para hospedar la aplicación.

2.  Abra el archivo app.js y reemplace el controlador de eventos **$('\#add-item').submit()** por el código siguiente:

        $('#add-item').submit(function(evt) {
            var textbox = $('#new-item-text'),
                itemText = textbox.val();
            if (itemText !== '') {
                todoItemTable.insert({ text: itemText, complete: false })
                    .then(refreshTodoItems, function(error){
                    alert(JSON.parse(error.request.responseText).error);
                });
            }
            textbox.val('').focus();
            evt.preventDefault();
        });

3.  En un explorador web, navegue a <http://localhost:8000/>, escriba algún texto en **Add new task** y haga clic en **Add**.

  Observe que la operación presenta un error y que el control de errores muestra la respuesta de error en un cuadro de diálogo.

Incorporación de una marca de tiempo
------------------------------------

Hemos aplicado las tareas anteriores para validar e insertar y, en consecuencia, aceptar o rechazar. Ahora, actualizará los datos insertados mediante un script de servidor que agrega una propiedad de marca de tiempo al objeto antes de insertarse.

**Nota:**

La propiedad de marca de tiempo **createdAt** que se muestra en este ejemplo es redundante en este caso. Servicios móviles crea automáticamente una propiedad del sistema **\_\_createdAt** para cada tabla.

1.  En la pestaña **Scripts** del [Portal de administración](https://manage.windowsazure.com/), reemplace el script **Insert** actual por la siguiente función y, a continuación, haga clic en **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, {
                     error: 'Text length must be under 10'
                 });
             } else {
                 item.createdAt = new Date();
                 request.execute();
             }
         }

    Esta función aumenta el script de inserción anterior mediante la incorporación de una nueva propiedad de marca de tiempo **createdAt** al objeto antes de que la llamada a **request**.**execute** lo inserte.

    **Nota:**

    El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna **createdAt** a la tabla **TodoItem** en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación.

2.  En el explorador web, vuelva a cargar la página, escriba un texto (con menos de 10 caracteres) en **Add new task** y haga clic en **Add**.

  Observe que la nueva marca de tiempo no aparece en la interfaz de usuario de la aplicación.

3.  Nuevamente en el Portal de administración, haga clic en la pestaña **Browse** en la tabla **todoitem**.

  Observe que ahora aparece una columna **createdAt** y el nuevo elemento insertado tiene un valor de marca de tiempo.

A continuación, debe actualizar la aplicación para que muestre esta nueva columna.

Nueva actualización del cliente
-------------------------------

El cliente Servicios móviles omitirá los datos de cualquier respuesta que no pueda serializar en propiedades en el tipo definido. El paso final consiste en actualizar el cliente para que muestre estos nuevos datos.

1.  En el editor, abra el archivo app.js y reemplace la función **refreshTodoItems** por el código siguiente:

         function refreshTodoItems() {
             var query = todoItemTable.where(function () {
                 return (this.complete === false && this.createdAt !== null);
             });

             query.read().then(function (todoItems) {
                 var listItems = $.map(todoItems, function(item) {
                     return $('<li>')
                         .attr('data-todoitem-id', item.id)
                         .append($('<button class="item-delete">Delete</button>'))
                         .append($('<input type="checkbox" class="item-complete">')
                             .prop('checked', item.complete))
                         .append($('<div>').append($('<input class="item-text">').val(item.text))
                         .append($('<span class="timestamp">' 
                             + (item.createdAt && item.createdAt.toDateString() + ' '
                             + item.createdAt.toLocaleTimeString() || '') 
                             + '</span>')));

                 });

                 $('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
                 $('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
             });
         }

  Esto muestra la parte de la fecha de la nueva propiedad **createdAt**. 

2.  En el editor, abra el archivo style.css y reemplace los estilos de la clase `item-text` por lo siguiente:

         .item-text { width: 70%; height: 26px; line-height: 24px; 
             border: 1px solid transparent; background-color: transparent; }
         .timestamp { width: 30%; height: 40px; font-size: .75em; }

    Con esto se modifica el tamaño del cuadro de texto y el estilo del texto de la nueva marca de tiempo.

3.  Vuelva a cargar la página.

  Observe que la marca de tiempo solo se muestra para los elementos insertados después de haber actualizado el script de inserción.

4.  Nuevamente en la función **refreshTodoItems**, reemplace la línea de código que define la consulta por lo siguiente:

          var query = todoItemTable.where(function () {
                 return (this.complete === false && this.createdAt !== null);
             });

  Esta función actualiza la consulta de forma que se filtren también los elementos que no tengan un valor de marca de tiempo.

5.  Vuelva a cargar la página.

  Observe que todos los elementos creados sin un valor de marca de tiempo desaparecen de la interfaz de usuario.

Ha completado este tutorial de trabajo con datos.

Pasos siguientes
----------------

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación](/en-us/develop/mobile/tutorials/add-paging-to-data-html).

Para obtener más información, consulte [Uso de scripts del servidor](/en-us/develop/mobile/how-to-guides/work-with-server-scripts) y [Referencia conceptual de Servicios móviles con HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client).

<!-- Images. -->
[0]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png