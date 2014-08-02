<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin Android app from Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" />

Limitación de consultas de Servicios móviles con paginación
===========================================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/add-paging-to-data-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[iOS C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[C\# para Android](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación Xamarin.Android desde Servicios móviles de Azure. En este tutorial se utilizarán los métodos de consulta **Take** y **Skip** en el cliente para solicitar "páginas" específicas de datos.

**Nota:**

Para evitar el desbordamiento de datos en clientes de dispositivos móviles, Servicios móviles implementa un límite automático de páginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tamaño de la página, puede solicitar explícitamente hasta 1.000 elementos en la respuesta.

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android). Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android).

1.  En Xamarin Studio, abra el proyecto que ha creado cuando realizó el tutorial [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android).

2.  Haga clic en **Run** para iniciar la aplicación y, a continuación, especifique el texto en el cuadro de texto y haga clic en el botón **Add**.

3.  Repita el paso anterior al menos tres veces de manera que disponga de más de tres elementos almacenados en la tabla TodoItem.

4.  En el archivo **TodoActivity.cs**, reemplace la consulta LINQ en el método **RefreshItemsFromTableAsync** por la siguiente consulta:

         var list = await todoTable.Where(item => item.Complete == false)
                                   .Take(3)
                                   .ToListAsync();

    Esta consulta devuelve los tres elementos principales que no están marcados como completados.

5.  Recompile e inicie la aplicación.

    Observe que solo se muestran los tres primeros resultados de la tabla TodoItem.

6.  (Opcional) Vea el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler].

	Tenga en cuenta que el método `Take(3)` se tradujo en la opción de consulta `$top=3` en el URI de consulta.

7.  Actualice la consulta LINQ en el método **RefreshItemsFromTableAsync** una vez más con la siguiente consulta:

             var list = await todoTable.Where(item => item.Complete == false)
                                       .Skip(3)
                                       .Take(3)
                                      .ToListAsync();

	Esta consulta omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

    **Nota:**

    Este tutorial usa un escenario simplificado para pasar valores de paginación codificados de forma rígida a los métodos **Take** y **Skip**. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores. También puede llamar al método **IncludeTotalCount** para obtener el recuento total de elementos disponibles en el servidor junto con los datos paginados.

8.  (Opcional) Vea de nuevo el URI de la solicitud enviada al servicio móvil.

	Observe que el método `Skip(3)` se ha traducido a la opción de consulta `$skip=3` en el URI de la consulta.

Pasos siguientes
----------------

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android)
    <br/>Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android)
    <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.


