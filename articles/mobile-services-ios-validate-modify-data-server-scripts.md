<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-ios" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="" solutions="" manager="" editor="" />

Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor
=============================================================================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

En este tema se muestra cómo aprovechar los scripts del servidor en Servicios móviles de Azure. Dichos scripts se registran en un servicio móvil y pueden usarse para realizar una gran variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. En este tutorial, definirá y registrará scripts de servidor que sirven para validar y modificar datos. Dado que el comportamiento de los scripts del servidor suele afectar al cliente, también actualizará la aplicación iOS para que se beneficie de estos nuevos comportamientos.

Este tutorial le guiará a través de estos pasos básicos:

1.  [Incorporación de la validación de longitud de cadena](#string-length-validation)
2.  [Actualización del cliente para admitir la validación](#update-client-validation) 
3.   [Incorporación de una marca de tiempo al insertar](#add-timestamp)
4.  [Actualización del cliente para mostrar la marca de tiempo](#update-client-timestamp)

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-ios). Antes de comenzar este tutorial, primero debe completar [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

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

Ahora que el servicio móvil puede validar los datos y enviar respuestas de error, debe actualizar la aplicación para que pueda identificar los errores de la validación.

1.  En Xcode, abra el proyecto que ha modificado al completar el tutorial [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

2.  Presione el botón **Ejecutar** (Comando + R) para crear el proyecto e iniciar la aplicación y, a continuación, escriba un texto con más de 10 caracteres en el cuadro de texto y haga clic en el icono más (**+**).

  Observe que la aplicación produce un error no controlado como resultado de la respuesta 400 (solicitud incorrecta) devuelta por el servicio móvil. 

3.  En el archivo QSTodoService.m, ubique la siguiente línea de código en el método **addItem**:

		[self logErrorIfNotNil:error]; 

  Después de esta línea de código, reemplace el recordatorio del bloque de finalización por el código siguiente:

		BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

		// detectar error de validación de texto desde el servicio.
		if (goodRequest) // El servicio respondió adecuadamente
		{
			NSUInteger index = [items count];
			[(NSMutableArray *)items insertObject:result atIndex:index];
            
			// Avise a la persona que llama de que hemos terminado.
			completion(index);
		}
		else{
            
			// si se produjo un error desde el servicio
			// regístrelo y aparecerá de manera emergente la cadena devuelta.
			if (error && error.code == MSErrorMessageErrorCode) {
				NSLog(@"ERROR %@", error);
				UIAlertView *av =
				[[UIAlertView alloc]
				initWithTitle:@"Request Failed"
				message:error.localizedDescription
				delegate:nil
				cancelButtonTitle:@"OK"
				otherButtonTitles:nil
				];
				[av show];
			}
		}

  Esto registra el error en la ventana de salida y lo muestra al usuario. 

4.  Recompile e inicie la aplicación.

  ![][4]

  Observe que el error está controlado y que el mensaje de error se muestra al usuario.

Pasos siguientes
----------------

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación](/en-us/develop/mobile/tutorials/add-paging-to-data-ios).

Los scripts de servidor también se usan al autorizar usuarios y para enviar notificaciones de inserción. Para obtener más información, consulte los siguientes tutoriales:

* [Autorización de usuarios con scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios)
  <br/>Aprenda a filtrar datos basándose en el identificador de un usuario autenticado.

* [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-ios)
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Obtenga más información acerca del registro y uso de scripts de servidor.


<!-- Images. -->
[0]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
