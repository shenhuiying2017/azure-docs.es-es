<properties 
	pageTitle="Uso del back-end .Net para validar y modificar datos (Tienda Windows) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo validar, modificar y aumentar los datos para su aplicación Javascript de la Tienda Windows con back-end de .Net de Servicios móviles de Microsoft Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="wesmc"/>

# Validación y modificación de datos en los Servicios móviles mediante el back-end de .NET

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Este tema muestra cómo usar el código en Servicios móviles de Azure del back-end de .NET para validar y modificar los datos. El servicio de back-end de .NET es un servicio HTTP compilado con el marco API Web. Si está familiarizado con la clase  `ApiController` definida con el marco Web API, la clase  `TableController` proporcionada por los Servicios móviles resultará muy intuitiva. `TableController` se deriva de la clase  `ApiController` y proporciona funcionalidad adicional para interactuar con una tabla de base de datos. Se puede utilizar para realizar operaciones en los datos que se van a insertar y actualizar, lo que incluye la validación y modificación de los datos que se presentan en este tutorial. 

Este tutorial le guiará a través de estos pasos básicos:

1. [Incorporación de la validación de longitud de cadena]
2. [Actualización del cliente para admitir la validación]
3. [Prueba de validación de la longitud]
4. [Incorporación de un campo de marca de tiempo para CompleteDate]
5. [Actualización del cliente para mostrar el valor de CompleteDate]

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior, [Introducción a los Servicios móviles] o [Introducción a los datos](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Antes de comenzar este tutorial, primero debe completar el tutorial [Introducción] o [Introducción a los datos](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/).  

## <a name="string-length-validation"></a>Incorporación de código de validación al servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Actualización del cliente

Ahora que el servicio móvil está configurado para validar los datos y enviar respuestas de error para una longitud de texto no válida, debe actualizar la aplicación para que pueda identificar las respuestas de error de la validación. El error se detectará desde la llamada de la aplicación cliente a  `IMobileServiceTable<TodoItem].InsertAsync()`.

1. En la ventana del Explorador de soluciones de Visual Studio, desplácese al proyecto de cliente de JavaScript y expanda la carpeta **js**. A continuación, abra el archivo default.js.

2. En default.js, reemplace la función **insertTodoItem** existente por la siguiente definición de función:


        var insertTodoItem = function (todoItem) {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the Binding List
            todoTable.insert(todoItem)
                .then(function (item) {
                  todoIte
	ms.push(item);
                },
                function (error) {
                  var msgDialog =
                    new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                    error.request.statusText + "(" + error.request.status + ")");
                  msgDialog.showAsync();
                });
        };

   	Esta versión de la función incluye control de errores y muestra un cuadro de diálogo  `MessageDialog` con el mensaje de error de la respuesta, así como el texto y el código relativos al estado.

## <a name="test-length-validation"></a>Prueba de validación de la longitud

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto de la aplicación cliente de JavaScript y, a continuación, haga clic en **Depurar**, **Iniciar nueva instancia**.

2. Escriba el texto para un nuevo elemento TodoItem con una longitud superior a 10 caracteres y, a continuación, haga clic en **Guardar**.

    ![][1]

3. Se mostrará un cuadro de diálogo de mensaje similar al siguiente en respuesta al texto no válido.

    ![][2]

## <a name="add-timestamp"></a>Incorporación de un campo de marca de tiempo para CompleteDate CompleteDate


[AZURE.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>Actualización del cliente para mostrar el valor de completeDate

El paso final consiste en actualizar el cliente para que muestre los nuevos datos de **completeDate**. 


1. En el Explorador de soluciones de Visual Studio, en el proyecto de cliente de JavaScript, abra el archivo default.html. Reemplace el elemento de etiqueta  `div` de la plantilla de enlace por la definición que se indica a continuación. A continuación, guarde el archivo. Se agrega una etiqueta  `div` con la propiedad innerText enlazada a **completeDate**.
	      
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



2. En default.js, quite la función de la cláusula  `.Where` de la función **refreshTodoItems** existente para que los elementos todoitems completados se incluyan en los resultados.

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listIte
	ms.winControl.itemDataSource = todoIte
	ms.dataSource;
                    });
            };


3. En default.js, actualice la función **updateCheckedTodoItem** de la siguiente forma para que los elementos se actualicen tras una actualización y los elementos completados no se quiten de la lista. A continuación, guarde el archivo.	

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };


4. En las ventanas del Explorador de soluciones de Visual Studio, haga clic con el botón derecho en **Solución** y, a continuación, en **Volver a generar solución** para recompilar el cliente y el servicio back-end de .NET. Compruebe que el proyecto se compile sin errores.

    ![][3]
	
5. Presione la tecla **F5** para ejecutar la aplicación y el servicio cliente localmente. Agregue algunos elementos nuevos y haga clic para marcar algunos elementos completados con el fin de comprobar que la marca de tiempo **CompleteDate** se esté actualizando.

    ![][4]

6. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto de servicio TodoList y haga clic en **Publicar**. Publique el servicio back-end de .NET en Microsoft Azure con el archivo de configuración de publicación que descargó del portal de Azure.

7. Actualice el archivo default.js para el proyecto de cliente quitando las marcas de comentario de la conexión a la dirección del servicio móvil. Pruebe la aplicación en el back-end de .NET que está hospedado en su cuenta de Azure.




## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación].

Los scripts de servidor también se usan al autorizar usuarios y para enviar notificaciones de inserción. Para obtener más información, consulte los siguientes tutoriales:

* [Autorización en el servicio de usuarios]
  <br/>Aprenda cómo filtrar los datos según el identificador de un usuario autenticado.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda cómo enviar una notificación de inserción muy básica a la aplicación.

* [Referencia conceptual de Servicios móviles con .NET]
  <br/>Obtenga más información sobre el uso de Servicios móviles con .NET.

<!-- Anchors. -->
[Incorporación de la validación de longitud de cadena]: #string-length-validation
[Actualización del cliente para admitir la validación]: #update-client-validation
[Prueba de validación de la longitud]: #test-length-validation
[Incorporación de un campo de marca de tiempo para CompleteDate]: #add-timestamp
[Actualización del cliente para mostrar el valor de CompleteDate]: #update-client-timestamp
[Pasos siguientes]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[Introducción a los servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Autorización en el servicio de usuarios]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introducción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript y HTML]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Portal de administración]: https://manage.windowsazure.com/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
