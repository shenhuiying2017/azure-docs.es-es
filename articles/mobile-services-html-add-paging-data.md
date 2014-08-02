<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Add paging to data (HTML5)" pageTitle="Add paging to data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your HTML app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

Limitación de consultas de Servicios móviles con paginación
===========================================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/add-paging-to-data-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación HTML desde Servicios móviles de Azure. En este tutorial se utilizarán los métodos de consulta **Take** y **Skip** en el cliente para solicitar "páginas" específicas de datos.

**Nota:**

Para evitar el desbordamiento de datos en clientes de dispositivos móviles, Servicios móviles implementa un límite automático de páginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tamaño de la página, puede solicitar explícitamente hasta 1.000 elementos en la respuesta.

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-html). Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-html).

1.  Ejecute uno de los siguientes archivos de comando desde la subcarpeta **server** del proyecto que modificó cuando completó el tutorial [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-html).

   -   **launch-windows** (equipos con Windows)
   -   **launch-mac.command** (equipos con Mac OS X)
   -   **launch-linux.sh** (equipos con Linux)

    **Nota:**

    En un equipo con Windows, escriba "R" cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.

    De este modo se inicia un servidor web en su equipo local para hospedar la aplicación.

2.  En un explorador web, navegue a <http://localhost:8000/>, escriba algún texto en **Add new task** y haga clic en **Add**.

3.  Repita los pasos anteriores al menos tres veces para que pueda tener más de tres elementos almacenados en la tabla TodoItem.

4.  En el archivo app.js, reemplace la definición de la variable `query` en el método **refreshTodoItems** con el siguiente código de línea:

         var query = todoItemTable.where({ complete: false }).take(3);

  Cuando se ejecuta, esta consulta devuelve los tres elementos principales que no están marcados como completados.

5.  Vuelva al explorador web y cargue nuevamente la página.

  Observe que solo se muestran los tres primeros resultados de la tabla TodoItem.

6.  (Opcional) Vea el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler].

  Observe que el método **take(3)** se ha traducido a la opción de consulta **$top=3** en el URI de la consulta.

7.  Actualice la consulta una vez más con el código siguiente:

         var query = todoItemTable.where({ complete: false }).skip(3).take(3);

8.  Vuelva al explorador web y cargue nuevamente la página.

  Esta consulta omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

    **Nota:**

    Este tutorial usa un escenario simplificado para pasar valores de paginación codificados de forma rígida a los métodos **Take** y **Skip**. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores. También puede llamar al método **includeTotalCount** para obtener el recuento total de elementos disponibles en el servidor, junto con los datos paginados.

9.  (Opcional) También puede ver el URI de la solicitud enviada al servicio móvil.

  Observe que el método **skip(3)** se ha traducido a la opción de consulta **$skip=3** en el URI de la consulta.

Pasos siguientes
----------------

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. A continuación, aprenda a autenticar usuarios de su aplicación en [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-html). Encontrará más información acerca de cómo usar los Servicios móviles con HTML/JavaScript en [Referencia conceptual de Servicios móviles HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)

