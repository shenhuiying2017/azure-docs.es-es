<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Android app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" solutions="" manager="" editor="" />

Limitación de consultas de Servicios móviles con paginación
===========================================================

[C\# para Tienda Windows](/es-es/develop/mobile/tutorials/add-paging-to-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/add-paging-to-data-js "JavaScript para Tienda Windows")[Windows Phone](/es-es/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/es-es/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/es-es/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/es-es/develop/mobile/tutorials/add-paging-to-data-html "HTML")[Xamarin.iOS](/es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación Android desde Servicios móviles de Azure. En este tutorial se utilizarán los métodos de consulta **top** y **skip** en el cliente para solicitar "páginas" específicas de datos.

**Nota:**

Para evitar el desbordamiento de datos en clientes de dispositivos móviles, Servicios móviles implementa un límite automático de páginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tamaño de la página, puede solicitar explícitamente hasta 1.000 elementos en la respuesta.

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-android). Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-android).

1.  En Eclipse, abra el proyecto que ha creado al completar el tutorial [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-android).

2.  En el menú **Run**, haga clic en **Run** para iniciar la aplicación y, a continuación, escriba texto en el cuadro de texto y haga clic en el botón **Add**.

3.  Repita los pasos anteriores al menos tres veces para que pueda tener más de tres elementos almacenados en la tabla TodoItem.

4.  En el archivo ToDoActivity.java, reemplace el método **RefreshTodoItems** por el siguiente código:

         private void refreshItemsFromTable() {
             // Definir una consulta filtrada que devuelva los tres primeros elementos.
             mToDoTable.where().field("complete").eq(false).top(3)
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

  Esta consulta devuelve los tres elementos principales que no están marcados como completados.

5.  Recompile e inicie la aplicación.

    Observe que solo se muestran los tres primeros resultados de la tabla TodoItem.

6.  (Opcional) Vea el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler].

  Observe que el método `top(3)` se ha traducido a la opción de consulta `$top=3` en el URI de la consulta.

7.  Actualice el método **RefreshTodoItems** una vez más con el código siguiente:

         private void refreshItemsFromTable() {
             // Definir una consulta filtrada que devuelva los tres primeros elementos.
             mToDoTable.where().field("complete").eq(false).skip(3).top(3)
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

  Esta consulta omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

    **Nota:**

    Este tutorial usa un escenario simplificado para pasar valores de paginación codificados de forma rígida a los métodos **top** y **skip**. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores. También puede llamar al método **includeInlineCount** para obtener el recuento total de elementos disponibles en el servidor, junto con los datos paginados.

8.  (Opcional) También puede ver el URI de la solicitud enviada al servicio móvil.

  Observe que el método `skip(3)` se ha traducido a la opción de consulta `$skip=3` en el URI de la consulta.

Pasos siguientes
----------------

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a la autenticación](/es-es/develop/mobile/tutorials/get-started-with-users-android)
  <br/>Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Introducción a las notificaciones de inserción](/es-es/develop/mobile/tutorials/get-started-with-push-android)
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.


