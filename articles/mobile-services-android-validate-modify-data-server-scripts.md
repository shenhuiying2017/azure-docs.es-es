<properties pageTitle="Uso de scripts de servidor para validar y modificar datos (Android) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo validar y modificar los datos enviados mediante scripts de servidor desde su aplicación Android." services="mobile-services" documentationCenter="android" authors="RickSaling" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="ricksal"/>

# Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

En este tema se muestra cómo aprovechar los scripts del servidor en Servicios móviles de Azure. Dichos scripts se registran en un servicio móvil y pueden usarse para realizar una gran variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. En este tutorial, definirá y registrará scripts de servidor que sirven para validar y modificar datos. Dado que el comportamiento de los scripts del servidor suele afectar al cliente, también actualizará la aplicación Android para que se beneficie de estos nuevos comportamientos.

Este tutorial le guiará a través de estos pasos básicos:

1. [Incorporación de la validación de longitud de cadena]
2. [Actualización del cliente para admitir la validación]
3. [Incorporación de una marca de tiempo al insertar]
4. [Actualización del cliente para mostrar la marca de tiempo]

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos]. Antes de comenzar este tutorial, primero debe completar [Introducción a los datos].  

## <a name="string-length-validation"></a>Incorporación de la validación

Siempre es conveniente validar la longitud de los datos enviados por los usuarios. En primer lugar, registre un script que valide la longitud de los datos de cadena enviados al servicio móvil y que rechace las cadenas demasiado largas, en este caso con más de 10 caracteres.

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su aplicación. 

   	![][0]

2. Haga clic en la pestaña **Datos** y, a continuación, haga clic en la tabla **TodoItem**.

   	![][1]

3. Haga clic en **Script** y luego seleccione la operación **Insert**.

   	![][2]

4. Sustituya el script existente por la siguiente función y luego haga clic en **Guardar**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Este script comprueba la longitud de la propiedad **text** y envía una respuesta de error cuando esta sobrepasa los 10 caracteres. De lo contrario, se llama al método **execute** para completar la inserción.

   > [AZURE.TIP] Puede quitar un script registrado en la pestaña **Script** haciendo clic en **Borrar** y luego en **Guardar**.

## <a name="update-client-validation"></a>Actualización del cliente

Ahora que el servicio móvil puede validar los datos y enviar respuestas de error, debe confirmar que su aplicación está administrando correctamente los errores de la validación.

1. En Eclipse, abra el proyecto que ha creado al completar el tutorial [Introducción a los datos].

2. En el archivo ToDoActivity.javaile, busque el método **addItem** y sustituya la llamada al método createAndShowDialog por el código siguiente:

		createAndShowDialog(exception.getCause().getMessage(), "Error");

	Esto muestra el mensaje de error devuelto por el servicio móvil. 

3. En el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar) para iniciar la aplicación, luego escriba texto con una longitud superior a 10 caracteres en el cuadro de texto y haga clic en el botón **Agregar**.

  Observe que el error está controlado y que el mensaje de error se muestra al usuario.

## <a name="add-timestamp"></a>Incorporación de una marca de tiempo

Las tareas anteriores validaban una inserción y bien la aceptaban o rechazaban. Ahora, actualizará los datos insertados mediante un script de servidor que agrega una propiedad de marca de tiempo al objeto antes de insertarse.

> [AZURE.NOTE] La propiedad de marca de tiempo **createdAt** que se muestra en este ejemplo es redundante en este caso. Los Servicios móviles crean automáticamente una propiedad del sistema **__createdAt** para cada tabla.

1. En la pestaña **Scripts** del [Portal de administración], sustituya el script actual **Insert** por la siguiente función y luego haga clic en **Guardar**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Esta función aumenta el script de inserción anterior mediante la incorporación de una nueva propiedad de marca de tiempo **createdAt** al objeto antes de que la llamada a **request**.**execute** lo inserte. 

    > [AZURE.IMPORTANT] El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, los Servicios móviles agregan automáticamente la columna **createdAt** a la tabla **TodoItem** en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación.

2. En el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar) para iniciar la aplicación, luego escriba texto (de menos de 10 caracteres) en el cuadro de texto y haga clic en **Agregar**.

   	Observe que la nueva marca de tiempo no aparece en la interfaz de usuario de la aplicación.

3. De vuelta en el Portal de administración, haga clic en la pestaña **Examinar** en la tabla **todoitem**.
   
   	Observe que ahora aparece una columna **createdAt** y el nuevo elemento insertado tiene un valor de marca de tiempo.
  
A continuación, debe actualizar la aplicación Android para que muestre esta nueva columna.

## <a name="update-client-timestamp"></a>Nueva actualización del cliente

El cliente de Servicios móviles omitirá los datos de cualquier respuesta que no pueda serializar en propiedades en el tipo definido. El paso final consiste en actualizar el cliente para que muestre estos nuevos datos.

1. En el Explorador de paquetes, abra el archivo ToDoItem.java y, a continuación, agregue la siguiente instrucción **import**:

		import java.util.Date;

2. Agregue el siguiente código a las definiciones de campo privadas en la clase **TodoItem**:

		/**
		 * Timestamp of the item inserted by the service.
		 */
		@com.google.gson.annotations.SerializedName("createdAt")
		private Date mCreatedAt;
  
    > [AZURE.NOTE] La anotación `SerializedName` indica al cliente que asigne la nueva propiedad `mCreatedAt` de la aplicación a la columna `createdAt` definida en la tabla TodoItem, que tiene un nombre diferente. Al usar esta anotación, la aplicación puede tener nombres de propiedad en objetos distintos a los nombres de columna de la base de datos SQL. Sin la anotación en cuestión, se produce un error debido a las diferencias en el uso de mayúsculas y minúsculas.

2. Agregue los siguientes métodos a la clase ToDoItem para obtener y definir la propiedad nueva mCreatedAt:

		/**
		 * Sets the timestamp.
		 * 
		 * @param date
		 *            timestamp to set
		 */
		public final void setCreatedAt(Date date) {
			mCreatedAt = date;
		}
		
		/**
		 * Returns the timestamp.
		 */
		public Date getCreatedAt() {
			return mCreatedAt;
		}

5. En el Explorador de paquetes, abra el archivo ToDoItemAdapter.java y agregue la siguiente instrucción **import**:

		import java.text.DateFormat;

6. En el método GetView, agregue el siguiente código:

		String createdAtText = "";
		if (currentItem.getCreatedAt() != null){
			DateFormat formatter = DateFormat.getDateInstance(DateFormat.SHORT);
			createdAtText = formatter.format(currentItem.getCreatedAt());
		}

   	Con esto se genera una cadena de fecha formateada cuando existe un valor de marca de tiempo. 

7. Busque el código `checkBox.setText(currentItem.getText());` y sustituya esta línea de código por lo siguiente:

		checkBox.setText(currentItem.getText() + " " + createdAtText);

	Esto añade la fecha de marca de tiempo al elemento para su visualización.
	
6. En el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar) para iniciar la aplicación. 

   	Observe que la marca de tiempo solo se muestra para los elementos insertados después de haber actualizado el script de inserción.

7. Reemplace el método **RefreshItemsFromTable** existente por el siguiente código:

		private void refreshItemsFromTable() {
			
			mToDoTable.where().field("complete").eq(false).and().field("createdAt").ne((String)null)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

   	Este método actualiza la consulta de forma que se filtren también los elementos que no tengan un valor de marca de tiempo.
	
8. En el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar) para iniciar la aplicación.

   	Observe que todos los elementos creados sin un valor de marca de tiempo desaparecen de la interfaz de usuario.

Ha completado este tutorial de trabajo con datos.

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación].

Los scripts de servidor también se usan al autorizar usuarios y para enviar notificaciones de inserción. Para obtener más información, consulte los siguientes tutoriales:

* [Autorización de usuarios con scripts]
  <br/>Aprenda cómo filtrar los datos según el identificador de un usuario autenticado.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda cómo enviar una notificación de inserción muy básica a la aplicación.

* [Referencia del script de servidor de Servicios móviles]
  <br/>Obtenga más información acerca del registro y del uso de scripts de servidor.

<!-- Anchors. -->
[Incorporación de la validación de longitud de cadena]: #string-length-validation
[Actualización del cliente para admitir la validación]: #update-client-validation
[Incorporación de una marca de tiempo al insertar]: #add-timestamp
[Actualización del cliente para mostrar la marca de tiempo]: #update-client-timestamp
[Pasos siguientes]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png



<!-- URLs. -->
[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introducción a los servicios móviles]: /es-es/develop/mobile/tutorials/get-started-android
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-android
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-android
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-android
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-android
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-android

[Portal de administración]: https://manage.windowsazure.com/
[Portal de administración de Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
