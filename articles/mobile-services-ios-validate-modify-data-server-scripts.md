<properties urlDisplayName="Validate Data" pageTitle="Uso de scripts de servidor para validar y modificar datos (iOS) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

En este tema se muestra cómo aprovechar los scripts de servidor en Servicios móviles de Azure. Dichos scripts se registran en un servicio móvil y pueden usarse para realizar una gran variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. En este tutorial, definirá y registrará scripts de servidor que sirven para validar y modificar datos. Dado que el comportamiento de los scripts del servidor suele afectar al cliente, también actualizará la aplicación iOS para que se beneficie de estos nuevos comportamientos.

Este tutorial le guiará a través de estos pasos básicos:

1. [Incorporación de la validación de longitud de cadena]
2. [Actualización del cliente para admitir la validación]


Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos]. Antes de comenzar este tutorial, debe completar [Introducción a los datos].  

## <a name="string-length-validation"></a>Incorporación de la validación

Siempre es conveniente validar la longitud de los datos enviados por los usuarios. En primer lugar, registre un script que valide la longitud de los datos de cadena enviados al servicio móvil y que rechace las cadenas demasiado largas, en este caso con más de 10 caracteres.

1. Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su aplicación.

   	![][0]

2. Haga clic en la pestaña **Data** y, a continuación, haga clic en la tabla **TodoItem**.


   	![][1]

3. Haga clic en **Script** y, a continuación, seleccione la operación **Insert**.

   	![][2]

4. Sustituya el script existente por la siguiente función y, a continuación, haga clic en **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Este script comprueba la longitud de la propiedad **text** y envía una respuesta de error cuando esta sobrepasa los 10 caracteres. En caso de no sobrepasarlos, se llama al método **execute** para completar la inserción.

    <div class="dev-callout">
	<b>Nota:</b>
	<p>Puede quitar un script registrado en la pestaña <strong>Script</strong> haciendo clic en <strong>Clear</strong> y, a continuación, en <strong>Save</strong>.</p></div>

## <a name="update-client-validation"></a>Actualización del cliente

Ahora que el servicio móvil puede validar los datos y enviar respuestas de error, debe actualizar la aplicación para que pueda identificar los errores de la validación.

1. En Xcode, abra el proyecto que ha modificado al completar el tutorial [Introducción a los datos][Introducción a los datos].

2. Presione el botón **Ejecutar** (Comando + R) para crear el proyecto e iniciar la aplicación y, a continuación, escriba un texto con más de 10 caracteres en el cuadro de texto y haga clic en el icono más (**+**).

   	Observe que la aplicación produce un error no controlado como resultado de la respuesta 400 (solicitud incorrecta) devuelta por el servicio móvil.

3. En el archivo QSTodoService.m, ubique la siguiente línea de código en el método **addItem**:

        [self logErrorIfNotNil:error];

   	Después de esta línea de código, reemplace el recordatorio del bloque de finalización por el código siguiente:

        BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

        // detect text validation error from service.
        if (goodRequest) // The service responded appropriately
        {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:result atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }
        else{

            // if there's an error that came from the service
            // log it, and popup up the returned string.
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

4. Recompile e inicie la aplicación.

   	![][4]

  	Observe que el error está controlado y que el mensaje de error se muestra al usuario.

<!--## <a name="add-timestamp"></a>Add a timestamp

The previous tasks validated an insert and either accepted or rejected it. Now, you will update inserted data by using a server script that adds a timestamp property to the object before it gets inserted.

1. In the **Scripts** tab in the [Management Portal], replace the current **Insert** script with the following function, and then click **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    This function augments the previous insert script by adding a new **createdAt** timestamp property to the object before it gets inserted by the call to **request**.**execute**.

    <div class="dev-callout"><b>Note</b>
	<p>Dynamic schema must be enabled the first time that this insert script runs. With dynamic schema enabled, Mobile Services automatically adds the <strong>createdAt</strong> column to the <strong>TodoItem</strong> table on the first execution. Dynamic schema is enabled by default for a new mobile service, and it should be disabled before the app is published.</p>
    </div>

2. In Visual Studio, press the **F5** key to run the app, then type text (shorter than 10 characters) in **Insert a TodoItem** and click **Save**.

   	Notice that the new timestamp does not appear in the app UI.

3. Back in the Management Portal, click the **Browse** tab in the **todoitem** table.

   	Notice that there is now a **createdAt** column, and the new inserted item has a timestamp value.

Next, you need to update the iOS app to display this new column.

## <a name="update-client-timestamp"></a>Update the client again

The Mobile Service client will ignore any data in a response that it cannot serialize into properties on the defined type. The final step is to update the client to display this new data.

1. In Visual Studio, open the file MainPage.xaml.cs, then replace the existing **TodoItem** class with the following definition:

	    public class TodoItem
	    {
	        public int Id { get; set; }

            [DataMember(Name="text")]
	        public string Text { get; set; }

            [DataMember(Name="complete")]
	        public bool Complete { get; set; }

            [DataMember(Name="createdAt")]
	        public DateTime? CreatedAt { get; set; }
	    }

    This new class definition includes the new timestamp property, as a nullable DateTime type.

    <div class="dev-callout"><b>Note</b>
	<p>The <strong>DataMemberAttribute</strong> tells the client to map the new <strong>CreatedAt</strong> property in the app to the <strong>createdAt</strong> column defined in the TodoItem table, which has a different casing. By using this attribute, your app can have property names on objects that differ from column names in the SQL Database. Without this attribute, an error would occur because of the casing differences.</p>
    </div>

5. Add the following XAML element just below the **CheckBoxComplete** element in the MainPage.xaml file:

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	Esto muestra la nueva propiedad **CreatedAt** en un cuadro de texto.

6. Presione la tecla **F5** para ejecutar la aplicación.

   Observe que la marca de tiempo solo se muestra para los elementos insertados después de haber actualizado el script de inserción.

7. Reemplace el método **RefreshTodoItems** por el siguiente código:

        private void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and
            // items without a timestamp.
            items = todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionView();

            ListItems.ItemsSource = items;
        }

   	Este método actualiza la consulta de forma que se filtren también los elementos que no tengan un valor de marca de tiempo.

8. Presione la tecla **F5** para ejecutar la aplicación.

   	Observe que todos los elementos creados sin un valor de marca de tiempo desaparecen de la interfaz de usuario.

Ha completado este tutorial de trabajo con datos.-->

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación].

Los scripts de servidor también se usan al autorizar usuarios y para enviar notificaciones de inserción. Para obtener más información, consulte los siguientes tutoriales:

* [Autorización de usuarios con scripts]
  <br/>Obtenga información acerca de cómo filtrar datos según el identificador de un usuario autenticado.

* [Introducción a las notificaciones de inserción]
  <br/>Vea cómo enviar una notificación de inserción muy básica a su aplicación.

* [Referencia del script del servidor de Servicios móviles]
  <br/>Más información acerca del registro y uso de scripts de servidor.

<!-- Anchors. -->
[Incorporación de la validación de longitud de cadena]: #string-length-validation
[Actualización del cliente para admitir la validación]: #update-client-validation
[Incorporación de una marca de tiempo al insertar]: #add-timestamp
[Actualización del cliente para mostrar la marca de tiempo]: #update-client-timestamp
[Pasos siguientes]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-ios
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-ios
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-ios
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-ios
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-ios

[Portal de administración]: https://manage.windowsazure.com/
[Portal de administración de Azure]: https://manage.windowsazure.com/
