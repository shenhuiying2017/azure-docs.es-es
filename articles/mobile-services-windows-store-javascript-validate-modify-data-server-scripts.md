<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-js" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Windows Store JavaScript app." metaCanonical="http://www.windowsazure.com/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="" solutions="" manager="" editor="" />

Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor
=============================================================================================

[C\# para Tienda Windows](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "C# para Tienda Windows") [JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-js "JavaScript para Tienda Windows") [Windows Phone](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone")[iOS](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android") [HTML](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML") [Xamarin.iOS](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

[Back-end de .NET](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Back-end de .NET") | [Back-end de JavaScript](es-es/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "Back-end de JavaScript")

En este tema se muestra cómo aprovechar los scripts del servidor en Servicios móviles de Azure. Dichos scripts se registran en un servicio móvil y pueden usarse para realizar una gran variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. En este tutorial, definirá y registrará scripts de servidor que sirven para validar y modificar datos. Dado que el comportamiento de los scripts del servidor suele afectar al cliente, también actualizará la aplicación de la Tienda Windows para que se beneficie de estos nuevos comportamientos.

Este tutorial le guiará a través de estos pasos básicos:

1.  [Incorporación de la validación de longitud de cadena](#string-length-validation)
2.  [Actualización del cliente para admitir la validación](#update-client-validation)
3.  [Incorporación de una marca de tiempo al insertar](#add-timestamp)
4.  [Actualización del cliente para mostrar la marca de tiempo](#update-client-timestamp)

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-js). Antes de comenzar este tutorial, primero debe completar [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-js).

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
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 request.execute();
             }
         }

    Este script comprueba la longitud de la propiedad **TodoItem.text** y envía una respuesta de error cuando esta sobrepasa los 10 caracteres. En caso de no sobrepasarlos, se llama al método **execute** para completar la inserción.

    **Nota:**

    Puede quitar un script registrado en la pestaña **Script** haciendo clic en **Clear** y, a continuación, en **Save**.

Actualización del cliente
-------------------------

Ahora que el servicio móvil puede validar los datos y enviar respuestas de error, debe actualizar la aplicación para que pueda identificar los errores de la validación.

1.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que modificó cuando completó el tutorial [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-js).

2.  Presione la tecla **F5** para ejecutar la aplicación, escriba un texto que sobrepase los 10 caracteres en **Insert a TodoItem** y haga clic en **Save**.

  Observe que la aplicación produce un error no controlado como resultado de la respuesta 400 (solicitud incorrecta) devuelta por el servicio móvil.

3.  Abra el archivo default.js y, a continuación, sustituya el método **InsertTodoItem** con lo siguiente:

        var insertTodoItem = function (todoItem) {
            // Inserta una fila nueva en la base de datos. Cuando la operación se completa
            // y Servicios móviles dispone de un identificador, el elemento se agrega a la lista de enlaces.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            }, function (error) {
                // Crear el cuadro de diálogo de mensaje de error y configurar su contenido para el mensaje de error
                // contenido en la respuesta.
                var msg = new Windows.UI.Popups.MessageDialog(
                    error.request.responseText);
                msg.showAsync();
            });
        };

  Esta versión del método incluye control de errores, que muestra la respuesta de error en un cuadro de diálogo.

Incorporación de una marca de tiempo
------------------------------------

Hemos aplicado las tareas anteriores para validar e insertar y, en consecuencia, aceptar o rechazar. Ahora, actualizará los datos insertados mediante un script de servidor que agrega una propiedad de marca de tiempo al objeto antes de insertarse.

**Nota:**

La propiedad de marca de tiempo **createdAt** que se muestra en este ejemplo es redundante en este caso. Servicios móviles crea automáticamente una propiedad del sistema **\_\_createdAt** para cada tabla.

1.  En la pestaña **Scripts** del [Portal de administración](https://manage.windowsazure.com/), reemplace el script **Insert** actual por la siguiente función y, a continuación, haga clic en **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 item.createdAt = new Date();
                 request.execute();
             }
         }

    Esta función aumenta el script de inserción anterior mediante la incorporación de una nueva propiedad de marca de tiempo **createdAt** al objeto antes de que la llamada a **request**.**execute** lo inserte.

    **Nota:**

    El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna **createdAt** a la tabla **TodoItem** en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación en la Tienda Windows.

2.  En Visual Studio, presione la tecla **F5** para ejecutar la aplicación y, a continuación, escriba un texto (con menos de 10 caracteres) en **Insert a TodoItem** y haga clic en **Guardar**.

  Observe que la nueva marca de tiempo no aparece en la interfaz de usuario de la aplicación.

3.  Nuevamente en el Portal de administración, haga clic en la pestaña **Browse** en la tabla **todoitem**.

  Observe que ahora aparece una columna **createdAt** y el nuevo elemento insertado tiene un valor de marca de tiempo.

A continuación, debe actualizar la aplicación de la Tienda Windows para que muestre esta nueva columna.

Nueva actualización del cliente
-------------------------------

El cliente Servicios móviles omitirá los datos de cualquier respuesta que no pueda serializar en propiedades en el tipo definido. El paso final consiste en actualizar el cliente para que muestre estos nuevos datos.

1.  En Visual Studio, abra el archivo default.html y, a continuación, agregue el siguiente elemento HTML en la cuadrícula TemplateItem:

         <div style="-ms-grid-column: 4; -ms-grid-row-align: center; margin-left: 5px" 
             data-win-bind="innerText: createdAt"></div>  

  Así se muestra la nueva propiedad **createdAt**. 

2.  Presione la tecla **F5** para ejecutar la aplicación.

  Observe que la marca de tiempo solo se muestra para los elementos insertados después de haber actualizado el script de inserción.

3.  En el archivo default.js, reemplace el método **RefreshTodoItems** existente por el siguiente código:

         var refreshTodoItems = function () {
             // Consulta más avanzada que filtra los elementos completados. 
             todoTable.where(function () {
                 return (this.complete === false && this.createdAt !== null);
             })
             .read()
             .done(function (results) {
                 todoItems = new WinJS.Binding.List(results);
                 listItems.winControl.itemDataSource = todoItems.dataSource;
             });
         };

  Este método actualiza la consulta de forma que se filtren también los elementos que no tengan un valor de marca de tiempo.

4.  Presione la tecla **F5** para ejecutar la aplicación.

  Observe que todos los elementos creados sin un valor de marca de tiempo desaparecen de la interfaz de usuario.

Ha completado este tutorial de trabajo con datos.

Pasos siguientes
----------------

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación](/es-es/develop/mobile/tutorials/add-paging-to-data-js).

Los scripts de servidor también se usan al autorizar usuarios y para enviar notificaciones de inserción. Para obtener más información, consulte los siguientes tutoriales:

* [Autorización de usuarios con scripts](/es-es/develop/mobile/tutorials/authorize-users-in-scripts-js)
  <br/>Aprenda a filtrar datos basándose en el identificador de un usuario autenticado.

* [Introducción a las notificaciones de inserción](/es-es/develop/mobile/tutorials/get-started-with-push-js)
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Obtenga más información acerca del registro y uso de scripts de servidor.


<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-insert-script-users.png