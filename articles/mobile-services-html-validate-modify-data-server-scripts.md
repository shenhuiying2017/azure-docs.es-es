<properties urlDisplayName="Validate Data - HTML5" pageTitle="Uso de scripts de servidor para validar y modificar datos (HTML 5) | Centro de desarrollo móvil" metaKeywords="" description="Obtenga información acerca de cómo validar y modificar los datos enviados mediante scripts de servidor desde su aplicación HTML." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor 

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

En este tema se muestra cómo aprovechar los scripts de servidor en Servicios móviles de Azure. Dichos scripts se registran en un servicio móvil y pueden usarse para realizar una gran variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. En este tutorial, definirá y registrará scripts de servidor que sirven para validar y modificar datos. Dado que el comportamiento de los scripts de servidor suelen afectar al cliente, también actualizará la aplicación HTML para que se beneficie de estos nuevos comportamientos.

Este tutorial le guiará a través de estos pasos básicos:

1. [Incorporación de la validación de longitud de cadena]
2. [Actualización del cliente para admitir la validación]
3. [Incorporación de una marca de tiempo al insertar]
4. [Actualización del cliente para mostrar la marca de tiempo]

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos]. Antes de comenzar este tutorial, debe completar [Introducción a los datos].  

## <a name="string-length-validation"></a>Incorporación de la validación

Siempre es conveniente validar la longitud de los datos enviados por los usuarios. En primer lugar, registre un script que valide la longitud de los datos de cadena enviados al servicio móvil y que rechace las cadenas demasiado largas, en este caso con más de 10 caracteres.

1. Inicie sesión en el [Portal de administración de Azure], elija **Servicios móviles** y haga clic en su aplicación. 

   	![][0]

2. Haga clic en la pestaña **Datos** y elija la tabla **TodoItem**.

   	![][1]

3. Haga clic en **Script**, seleccione la operación **Insertar**.

   	![][2]

4. Reemplace el script existente por la siguiente función y haga clic en **Guardar**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, {
                    error: "Text cannot exceed 10 characters"
                });
            } else {
                request.execute();
            }
        }

    Este script comprueba la longitud de la propiedad **TodoItem.text** y envía una respuesta de error cuando la longitud supera los 10 caracteres. En caso contrario, se llama a la función **execute** para completar la inserción.

    <div class="dev-callout"> 
	<b>Nota:</b> 
	<p>Para quitar un script registrado en la pestaña <strong>Script</strong>, haga clic en <strong>Borrar</strong> y después en <strong>Guardar</strong>.</p></div>	

## <a name="update-client-validation"></a>Actualización del cliente

Ahora que el servicio móvil puede validar los datos y enviar respuestas de error, debe actualizar la aplicación para que pueda identificar los errores de la validación.

1. Ejecute uno de los siguientes archivos de comando desde la subcarpeta **server** del proyecto que modificó cuando completó el tutorial [Introducción a los datos].

	+ **launch-windows** (equipos con Windows) 
	+ **launch-mac.command** (equipos con Mac OS X)
	+ **launch-linux.sh** (equipos con Linux)

	<div class="dev-callout"><b>Nota:</b>
		<p>En un equipo con Windows, escriba `R` cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.</p>
	</div>

	De este modo se inicia un servidor web en su equipo local para hospedar la aplicación.

1. 	Abra el archivo app.js y reemplace el controlador de eventos **$('#add-item').submit()** por el código siguiente:

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

2. En un explorador web, navegue a <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>, escriba texto en **Agregar nueva tarea** y haga clic en **Agregar**.

   	Observe que la operación presenta un error y que el control de errores muestra la respuesta de error en un cuadro de diálogo.

## <a name="add-timestamp"></a>Incorporación de una marca de tiempo

Las tareas anteriores validaban una inserción y bien la aceptaban o rechazaban. Ahora, actualizará los datos insertados mediante un script de servidor que agrega una propiedad de marca de tiempo al objeto antes de insertarse.

<div class="dev-callout"><b>Nota:</b>
<p>La propiedad de marca de tiempo <b>createdAt</b> que se muestra en este ejemplo es redundante en este caso. Servicios móviles crea automáticamente una propiedad del sistema <b>__createdAt</b> para cada tabla.</p>
</div>

1. En la pestaña **Scripts** del [Portal de administración], reemplace el script **Insert** actual por la siguiente función y haga clic en **Guardar**.

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

    Esta función aumenta el script de inserción anterior mediante la incorporación de una nueva propiedad de marca de tiempo **createdAt** al objeto antes de su inserción con la llamada a **request**.**execute**. 

    <div class="dev-callout"><b>Nota:</b>
	<p>El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna <strong>createdAt</strong> a la tabla <strong>TodoItem</strong> en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación.</p>
    </div>

2. En el explorador web, recargue la página, escriba un texto (con menos de 10 caracteres) en **Agregar nueva tarea** y haga clic en **Agregar**.

   	Observe que la nueva marca de tiempo no aparece en la interfaz de usuario de la aplicación.

3. De nuevo en el Portal de administración, haga clic en la pestaña **Examinar** en la tabla **todoitem**.
   
   	Observe que ahora aparece una columna **createdAt** y el nuevo elemento insertado tiene un valor de marca de tiempo.
  
A continuación, debe actualizar la aplicación para que muestre esta nueva columna.

## <a name="update-client-timestamp"></a>Nueva actualización del cliente

El cliente de Servicios móviles omitirá los datos de cualquier respuesta que no pueda serializar en propiedades en el tipo definido. El paso final consiste en actualizar el cliente para que muestre estos nuevos datos.

1. En el editor, abra el archivo app.js y reemplace la función **refreshTodoItems** por el código siguiente:

		function refreshTodoItems() {
			var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

			query.read().then(function(todoItems) {
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

2. En el editor, abra el archivo style.css y reemplace los estilos de la clase `item-text` por el código siguiente:

		.item-text { width: 70%; height: 26px; line-height: 24px; 
			border: 1px solid transparent; background-color: transparent; }
		.timestamp { width: 30%; height: 40px; font-size: .75em; }

	Con esto se modifica el tamaño del cuadro de texto y el estilo del texto de la nueva marca de tiempo.
	
6. Vuelva a cargar la página. 	

   	Observe que la marca de tiempo solo se muestra para los elementos insertados después de haber actualizado el script de inserción.

7. De nuevo en la función **refreshTodoItems**, reemplace la línea de código que define la consulta por lo siguiente:

         var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

   	Esta función actualiza la consulta de forma que se filtren también los elementos que no tengan un valor de marca de tiempo.
	
8. Vuelva a cargar la página.

   	Observe que todos los elementos creados sin un valor de marca de tiempo desaparecen de la interfaz de usuario.

Ha completado este tutorial de trabajo con datos.

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación].

Para obtener más información, consulte [Trabajo con scripts de servidor] y [Documentación de referencia conceptual de Servicios móviles con HTML/JavaScript]


<!-- Anchors. -->
[Incorporación de la validación de longitud de cadena]: #string-length-validation
[Actualización del cliente para admitir la validación]: #update-client-validation
[Incorporación de una marca de tiempo al insertar]: #add-timestamp
[Actualización del cliente para mostrar la marca de tiempo]: #update-client-timestamp
[Pasos siguientes]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Trabajo con scripts de servidor]: /es-es/develop/mobile/how-to-guides/work-with-server-scripts
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-html
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-html
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-html
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-html
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-html

[Portal de administración]: https://manage.windowsazure.com/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Documentación de referencia conceptual de Servicios móviles con HTML/JavaScript]: /es-es/develop/mobile/how-to-guides/work-with-html-js-client
