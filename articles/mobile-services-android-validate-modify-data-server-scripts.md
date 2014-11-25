<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-android" urlDisplayName="Validate Data - Android" pageTitle="Use server scripts to validate and modify data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android" class="current">Android</a><a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML" class="current">HTML</a><a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

En este tema se muestra cómo aprovechar los scripts del servidor en Servicios móviles de Azure. Dichos scripts se registran en un servicio móvil y pueden usarse para realizar una gran variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. En este tutorial, definirá y registrará scripts de servidor que sirven para validar y modificar datos. Dado que el comportamiento de los scripts del servidor suele afectar al cliente, también actualizará la aplicación Android para que se beneficie de estos nuevos comportamientos.

Este tutorial le guiará a través de estos pasos básicos:

1.  [Incorporación de la validación de longitud de cadena][Incorporación de la validación de longitud de cadena]
2.  [Actualización del cliente para admitir la validación][Actualización del cliente para admitir la validación]
3.  [Incorporación de una marca de tiempo al insertar][Incorporación de una marca de tiempo al insertar]
4.  [Actualización del cliente para mostrar la marca de tiempo][Actualización del cliente para mostrar la marca de tiempo]

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos][Introducción a los datos]. Antes de comenzar este tutorial, primero debe completar [Introducción a los datos][Introducción a los datos].

## <a name="string-length-validation"></a>Incorporación de la validación

Siempre es conveniente validar la longitud de los datos enviados por los usuarios. En primer lugar, registre un script que valide la longitud de los datos de cadena enviados al servicio móvil y que rechace las cadenas demasiado largas, en este caso con más de 10 caracteres.

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su aplicación.

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

    <div class="dev-callout"> 
<b>Nota:</b> 
<p>Puede quitar un script registrado en la pesta&ntilde;a <strong>Script</strong> haciendo clic en <strong>Clear</strong> y, a continuaci&oacute;n, en <strong>Save</strong>.</p></div>

## <a name="update-client-validation"></a>Actualización del cliente

Ahora que el servicio móvil puede validar los datos y enviar respuestas de error, debe confirmar que su aplicación está administrando correctamente los errores de la validación.

1.  En Eclipse, abra el proyecto que ha creado al completar el tutorial [Introducción a los datos][Introducción a los datos].

2.  En el archivo ToDoActivity.java, ubique el método **addItem** y reemplace la llamada al método createAndShowDialog por el siguiente código:

        createAndShowDialog(exception.getCause().getMessage(), "Error");

    Esto muestra el mensaje de error devuelto por el servicio móvil.

3.  En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación y, a continuación, escriba un texto con más de 10 caracteres en el cuadro de texto y haga clic en el botón **Add**.

Observe que el error está controlado y que el mensaje de error se muestra al usuario.

## <a name="add-timestamp"></a>Incorporación de una marca de tiempo

Hemos aplicado las tareas anteriores para validar e insertar y, en consecuencia, aceptar o rechazar. Ahora, actualizará los datos insertados mediante un script de servidor que agrega una propiedad de marca de tiempo al objeto antes de insertarse.

<div class="dev-callout"><b>Nota:</b>
<p>La propiedad de marca de tiempo <b>createdAt</b> que se muestra en este ejemplo es redundante en este caso. Servicios m&oacute;viles crea autom&aacute;ticamente una propiedad del sistema <b>__createdAt</b> para cada tabla.</p>
</div>

1.  En la pestaña **Scripts** del [Portal de administración][Portal de administración de Azure], reemplace el script **Insert** actual por la siguiente función y, a continuación, haga clic en **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Esta función aumenta el script de inserción anterior mediante la incorporación de una nueva propiedad de marca de tiempo **createdAt** al objeto antes de que la llamada a **request**.**execute** lo inserte.

    <div class="dev-callout"><b>Nota:</b>
<p>El esquema din&aacute;mico debe habilitarse la primera vez que se ejecute este script de inserci&oacute;n. Al tener habilitado dicho esquema, Servicios m&oacute;viles agrega autom&aacute;ticamente la columna <strong>createdAt</strong> a la tabla <strong>TodoItem</strong> en la primera ejecuci&oacute;n. El esquema din&aacute;mico est&aacute; habilitado de forma predeterminada para un nuevo servicio m&oacute;vil y debe deshabilitarse antes de publicar la aplicaci&oacute;n.</p>
</div>

2.  En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación y, a continuación, escriba un texto (con menos de 10 caracteres) en el cuadro de texto y haga clic en el botón **Add**.

    Observe que la nueva marca de tiempo no aparece en la interfaz de usuario de la aplicación.

3.  Nuevamente en el Portal de administración, haga clic en la pestaña **Browse** en la tabla **todoitem**.

    Observe que ahora aparece una columna **createdAt** y el nuevo elemento insertado tiene un valor de marca de tiempo.

A continuación, debe actualizar la aplicación Android para que muestre esta nueva columna.

## <a name="update-client-timestamp"></a>Nueva actualización del cliente

El cliente Servicios móviles omitirá los datos de cualquier respuesta que no pueda serializar en propiedades en el tipo definido. El paso final consiste en actualizar el cliente para que muestre estos nuevos datos.

1.  En el Explorador de paquetes, abra el archivo ToDoItem.java y, a continuación, agregue la siguiente instrucción de **importación**:

        import java.util.Date;

2.  Agregue el siguiente código a las definiciones de campo privadas en la clase **TodoItem**:

        /**
         * Timestamp of the item inserted by the service.
         */
        @com.google.gson.annotations.SerializedName("createdAt")
        private Date mCreatedAt;

    <div class="dev-callout"><b>Nota:</b>
<p>La anotaci&oacute;n <code data-inline="1">SerializedName</code> indica al cliente que asigne la nueva propiedad <code data-inline="1">mCreatedAt</code> de la aplicaci&oacute;n a la columna <code data-inline="1">createdAt</code> definida en la tabla TodoItem, que tiene un nombre diferente. Al usar esta anotaci&oacute;n, la aplicaci&oacute;n puede tener nombres de propiedad en objetos distintos a los nombres de columna de la base de datos SQL. Sin la anotaci&oacute;n en cuesti&oacute;n, se produce un error debido a las diferencias en el uso de may&uacute;sculas y min&uacute;sculas.</p>
</div>

3.  Agregue los siguientes métodos a la clase ToDoItem para obtener y definir la propiedad nueva mCreatedAt:

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

4.  En el Explorador de paquetes, abra el archivo ToDoItemAdapter.java y, a continuación, agregue la siguiente instrucción de **importación**:

        import java.text.DateFormat;

5.  En el método GetView, agregue el siguiente código:

        String createdAtText = "";
        if (currentItem.getCreatedAt() != null){
            DateFormat formatter = DateFormat.getDateInstance(DateFormat.SHORT);
            createdAtText = formatter.format(currentItem.getCreatedAt());
        }

    Con esto se genera una cadena de fecha formateada cuando existe un valor de marca de tiempo.

6.  Ubique el código `checkBox.setText(currentItem.getText());` y reemplace esta línea de código por lo siguiente:

        checkBox.setText(currentItem.getText() + " " + createdAtText);

    Esto añade la fecha de marca de tiempo al elemento para su visualización.

7.  En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación.

    Observe que la marca de tiempo solo se muestra para los elementos insertados después de haber actualizado el script de inserción.

8.  Reemplace el método **RefreshItemsFromTable** existente por el siguiente código:

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

9.  En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación.

    Observe que todos los elementos creados sin un valor de marca de tiempo desaparecen de la interfaz de usuario.

Ha completado este tutorial de trabajo con datos.

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación][Limitación de consultas con paginación].

Los scripts de servidor también se usan al autorizar usuarios y para enviar notificaciones de inserción. Para obtener más información, consulte los siguientes tutoriales:

-   [Autorización de usuarios con scripts][Autorización de usuarios con scripts]
    Aprenda a filtrar datos basándose en el identificador de un usuario autenticado.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    Obtenga más información acerca del registro y uso de scripts de servidor.





  [Incorporación de la validación de longitud de cadena]: #string-length-validation
  [Actualización del cliente para admitir la validación]: #update-client-validation
  [Incorporación de una marca de tiempo al insertar]: #add-timestamp
  [Actualización del cliente para mostrar la marca de tiempo]: #update-client-timestamp
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-android
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-android
  [Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-android
  [Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-android
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
