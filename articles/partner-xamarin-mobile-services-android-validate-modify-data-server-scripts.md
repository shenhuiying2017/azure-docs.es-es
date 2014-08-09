<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-xamarin-android" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (Xamarin Android) | Mobile Dev Center" metaKeywords="access and change data, Azure Mobile Services, mobile devices, Azure, mobile, Xamarin.Android" description="Learn how to validate and modify data sent using server scripts from your Xamarin.Android app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="" />

Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor
=============================================================================================

[C\# para Tienda Windows](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript")[Windows Phone](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone")[iOS](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS")[Android](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

En este tema se muestra cómo aprovechar los scripts del servidor en Servicios móviles de Azure. Dichos scripts se registran en un servicio móvil y pueden usarse para realizar una gran variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. En este tutorial, definirá y registrará scripts de servidor que sirven para validar y modificar datos. Dado que el comportamiento de los scripts del servidor suele afectar al cliente, también actualizará la aplicación Android para que se beneficie de estos nuevos comportamientos. El código terminado está disponible en el ejemplo de la [aplicación ValidateModifyData](http://go.microsoft.com/fwlink/p/?LinkId=331330).

Este tutorial le guiará a través de estos pasos básicos:

1.  [Incorporación de la validación de longitud de cadena](#string-length-validation)
2.  [Actualización del cliente para admitir la validación](#update-client-validation)
3.  [Incorporación de una marca de tiempo al insertar](#add-timestamp)
4.  [Actualización del cliente para mostrar la marca de tiempo](#update-client-timestamp)

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android). Antes de comenzar este tutorial, primero debe completar [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android).

Incorporación de la validación
------------------------------

Siempre es conveniente validar la longitud de los datos enviados por los usuarios. En primer lugar, registre un script que valide la longitud de los datos de cadena enviados al servicio móvil y que rechace las cadenas demasiado largas, en este caso con más de 10 caracteres.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su aplicación.

    ![](./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  Haga clic en la pestaña **Data** y, a continuación, haga clic en la tabla **TodoItem**.

    ![](./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png)

3.  Haga clic en **Script** y, a continuación, seleccione la operación **Insert**.

    ![](./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png)

4.  Sustituya el script existente por la siguiente función y, a continuación, haga clic en **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
             } else {
                 request.execute();
             }
         }

    Este script comprueba la longitud de la propiedad **text** y envía una respuesta de error cuando esta sobrepasa los 10 caracteres. En caso de no sobrepasarlos, se llama al método **execute** para completar la inserción.

    **Nota:**

    Puede quitar un script registrado en la pestaña **Script** haciendo clic en **Clear** y, a continuación, en **Save**.

Actualización del cliente
-------------------------

Ahora que el servicio móvil puede validar los datos y enviar respuestas de error, debe confirmar que su aplicación está administrando correctamente los errores de la validación.

1.  En Xamarin Studio, abra el proyecto que ha creado cuando realizó el tutorial [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android).

2.  En el archivo TodoActivity.cs, localice el método **AddItem** y reemplace la llamada al método CreateAndShowDialog por el siguiente código:

		var exDetail = ex.InnerException.InnerException as
			MobileServiceInvalidOperationException;
		CreateAndShowDialog(exDetail.Message, "Error");

    Esto muestra el mensaje de error devuelto por el servicio móvil.

3.  Haga clic en **Run** para iniciar la aplicación, a continuación, escriba el texto de más de 10 caracteres en el cuadro de texto y haga clic en el botón **Add**.

  Observe que el error está controlado y que el mensaje de error se muestra al usuario.

Incorporación de una marca de tiempo
------------------------------------

Hemos aplicado las tareas anteriores para validar e insertar y, en consecuencia, aceptar o rechazar. Ahora, actualizará los datos insertados mediante un script de servidor que agrega una propiedad de marca de tiempo al objeto antes de insertarse.

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

    El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna **createdAt** a la tabla **TodoItem** en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación.

2.  En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación y, a continuación, escriba un texto (con menos de 10 caracteres) en el cuadro de texto y haga clic en el botón **Add**.

	Observe que la nueva marca de tiempo no aparece en la interfaz de usuario de la aplicación.

3.  Nuevamente en el Portal de administración, haga clic en la pestaña **Browse** en la tabla **todoitem**.

	Observe que ahora aparece una columna **createdAt** y el nuevo elemento insertado tiene un valor de marca de tiempo.

A continuación, debe actualizar la aplicación Android para que muestre esta nueva columna.

Nueva actualización del cliente
-------------------------------

El cliente Servicios móviles omitirá los datos de cualquier respuesta que no pueda serializar en propiedades en el tipo definido. El paso final consiste en actualizar el cliente para que muestre estos nuevos datos.

1.  Agregue el siguiente código a las definiciones de campo privadas en la clase **TodoItem**:

         [DataMember(Name = "createdAt")]
         public DateTime? CreatedAt { get; set; }

    **Nota:**

    La anotación `DataMember's Name` indica al cliente que asigne la nueva propiedad `CreatedAt` de la aplicación a la columna `createdAt` definida en la tabla TodoItem, que tiene un nombre diferente. Al usar esta anotación, la aplicación puede tener nombres de propiedad en objetos distintos a los nombres de columna de la base de datos SQL. Sin la anotación en cuestión, se produce un error debido a las diferencias en el uso de mayúsculas y minúsculas.

2.  En el método GetView, agregue el siguiente código justo encima de donde el código actual que establece `checkBox.Text` en `currentItem.Text`:

		string displayDate = "missing";
		if (currentItem.CreatedAt.HasValue)
			displayDate = currentItem.CreatedAt.Value.ToShortTimeString();

	De esta manera se crea una cadena de fecha con formato cuando existe un valor de marca de tiempo. 

3.  Busque el código `checkBox.Text = currentItem.Text` nuevo y reemplace esta línea de código por lo siguiente:

         checkBox.Text = string.Format("{0} - {1}", currentItem.Text, displayDate);

    Esto añade la fecha de marca de tiempo al elemento para su visualización.

4.  En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación.

    Observe que la marca de tiempo solo se muestra para los elementos insertados después de haber actualizado el script de inserción.

5.  En **TodoActivity.cs**, reemplace la consulta existente en **RefreshItemsFromTableAsync** por la siguiente consulta:

         var list = await todoTable.Where(item => item.Complete == false && 
                                          item.CreatedAt != null)
                                   .ToListAsync();

    Este método actualiza la consulta de forma que se filtren también los elementos que no tengan un valor de marca de tiempo.

6.  En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación.

  Observe que todos los elementos creados sin un valor de marca de tiempo desaparecen de la interfaz de usuario.

Ha completado este tutorial de trabajo con datos.

Pasos siguientes
----------------

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación](/es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-android).

Los scripts de servidor también se usan al autorizar usuarios y para enviar notificaciones de inserción. Para obtener más información, consulte los siguientes tutoriales:

* [Autorización de usuarios con scripts](/es-es/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android)
  <br/>Aprenda a filtrar datos basándose en el identificador de un usuario autenticado.

* [Introducción a las notificaciones de inserción](/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-android)
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Obtenga más información acerca del registro y uso de scripts de servidor.


