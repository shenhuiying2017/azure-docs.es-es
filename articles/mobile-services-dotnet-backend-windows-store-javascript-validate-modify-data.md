<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-javascript" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Javascript Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Validación y modificación de datos en los Servicios móviles mediante el back-end de .NET

<div class="dev-center-tutorial-selector sublanding">

[C# para Tienda Windows][] [JavaScript para Tienda Windows][] [Windows Phone][][iOS][] [Android][] [HTML][][Xamarin.iOS][] [Xamarin.Android][]

</div>

<div class="dev-center-tutorial-subselector">

[Back-end de .NET][] | [Back-end de JavaScript][]

</div>

Este tema muestra cómo usar el código en Servicios móviles de Azure del back-end de .NET para validar y modificar los datos. El servicio de back-end de .NET es un servicio HTTP compilado con el marco API Web. Si está familiarizado con la clase `ApiController`, definida con el marco API Web, la clase `TableController` que la solución Servicios móviles proporciona le resultará muy intuitiva. `TableController` proviene de la clase `ApiController` y proporciona funcionalidad adicional para la creación de la interfaz de la tabla de base de datos. Se puede utilizar para realizar operaciones en los datos que se van a insertar y actualizar, lo que incluye la validación y modificación de los datos que se presentan en este tutorial.

Este tutorial le guiará a través de estos pasos básicos:

1.  [Incorporación de la validación de longitud de cadena][]
2.  [Actualización del cliente para admitir la validación][]
3.  [Prueba de validación de la longitud][]
4.  [Incorporación de un campo de marca de tiempo para CompleteDate][]
5.  [Actualización del cliente para mostrar el valor de CompleteDate][]

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior, [Introducción a los Servicios móviles][] o [Introducción a los datos][]. Antes de comenzar este tutorial, primero debe completar el tutorial [Introducción a los Servicios móviles][] o [Introducción a los datos][].

## <a name="string-length-validation"></a>Incorporación de código de validación al servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation][]]

## <a name="update-client-validation"></a>Actualización del cliente

Ahora que el servicio móvil está configurado para validar los datos y enviar respuestas de error para una longitud de texto no válida, debe actualizar la aplicación para que pueda identificar las respuestas de error de la validación. El error se detectará desde la llamada de la aplicación cliente a `IMobileServiceTable<TodoItem].InsertAsync()`.

1.  En la ventana del Explorador de soluciones de Visual Studio, desplácese al proyecto de cliente de JavaScript y expanda la carpeta **js**. A continuación, abra el archivo default.js.

2.  En default.js, reemplace la función **insertTodoItem** existente por la siguiente definición de función:

        var insertTodoItem = function (todoItem) {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the Binding List
            todoTable.insert(todoItem)
                .then(function (item) {
                  todoItems.push(item);
                },
                function (error) {
                  var msgDialog =
                    new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                    error.request.statusText + "(" + error.request.status + ")");
                  msgDialog.showAsync();
                });
        };

    Esta versión de la función incluye control de errores y muestra un cuadro de diálogo `MessageDialog` con el mensaje de error de la respuesta, así como el texto y el código relativos al estado.

## <a name="test-length-validation"></a>Prueba de validación de la longitud

1.  En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de la aplicación cliente de JavaScript y, a continuación, haga clic en **Depurar**, **Iniciar nueva instancia**.

2.  Escriba el texto para un nuevo elemento TodoItem con una longitud superior a 10 caracteres y, a continuación, haga clic en **Guardar**.

    ![][]

3.  Se mostrará un cuadro de diálogo de mensaje similar al siguiente en respuesta al texto no válido.

    ![][1]

## <a name="add-timestamp"></a>Incorporación de un campo de marca de tiempo para CompleteDate

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate][]]

## <a name="update-client-timestamp"></a>Actualización del cliente para mostrar el valor de completeDate

El paso final consiste en actualizar el cliente para que muestre los nuevos datos de **completeDate**.

1.  En el Explorador de soluciones de Visual Studio, en el proyecto de cliente de JavaScript, abra el archivo default.html. Reemplace el elemento de etiqueta `div` de la plantilla de enlace por la definición que se indica a continuación. A continuación, guarde el archivo. De esta forma, se agrega una etiqueta `div` con la propiedad innerText enlazada a **completeDate**.

        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: 3">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; 
              dataContext: this" />
            <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px" 
              data-win-bind="innerText: text">
            </div>
            <div style="-ms-grid-column: 3; -ms-grid-row-align: center; margin-left: 10px" 
              data-win-bind="innerText: completeDate">
            </div>
          </div>
        </div>

2.  En default.js, quite la función de la cláusula `.Where` de la función **refreshTodoItems** existente para que los elementos todoitems completados se incluyan en los resultados.

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };

3.  En default.js, actualice la función **updateCheckedTodoItem** de la siguiente forma para que los elementos se actualicen tras una actualización y los elementos completados no se quiten de la lista. A continuación, guarde el archivo.

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };

4.  En las ventanas del Explorador de soluciones de Visual Studio, haga clic con el botón secundario en **Solución** y, a continuación, en **Volver a generar solución** para recompilar el cliente y el servicio back-end de .NET. Compruebe que el proyecto se compile sin errores.

    ![][2]

5.  Presione la tecla **F5** para ejecutar la aplicación y el servicio cliente localmente. Agregue algunos elementos nuevos y haga clic para marcar algunos elementos completados con el fin de comprobar que la marca de tiempo **CompleteDate** se esté actualizando.

    ![][3]

6.  En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de servicio TodoList y haga clic en **Publicar**. Publique el servicio back-end de .NET en Microsoft Azure con el archivo de configuración de publicación que descargó del portal de Azure.

7.  Actualice el archivo default.js para el proyecto de cliente quitando las marcas de comentario de la conexión a la dirección del servicio móvil. Pruebe la aplicación en el back-end de .NET que está hospedado en su cuenta de Azure.

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación][].

Los scripts de servidor también se usan al autorizar usuarios y para enviar notificaciones de inserción. Para obtener más información, consulte los siguientes tutoriales:

-   [Autorización de servicios de usuarios][]
    
	Aprenda a filtrar datos por el identificador de un usuario autenticado.

-   [Introducción a las notificaciones de inserción][]
    
	Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia conceptual de Servicios móviles con .NET][]
    
	Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [C# para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone"
  [iOS]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "Back-end de JavaScript"
  [Incorporación de la validación de longitud de cadena]: #string-length-validation
  [Actualización del cliente para admitir la validación]: #update-client-validation
  [Prueba de validación de la longitud]: #test-length-validation
  [Incorporación de un campo de marca de tiempo para CompleteDate]: #add-timestamp
  [Actualización del cliente para mostrar el valor de CompleteDate]: #update-client-timestamp
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
  [mobile-services-dotnet-backend-add-validation]: ../includes/mobile-services-dotnet-backend-add-validation.md
  []: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [mobile-services-dotnet-backend-add-completedate]: ../includes/mobile-services-dotnet-backend-add-completedate.md
  [2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png
  [Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Autorización de servicios de usuarios]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library
