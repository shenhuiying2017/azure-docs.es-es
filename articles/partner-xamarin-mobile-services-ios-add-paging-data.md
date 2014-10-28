<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-ios" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="donnam" solutions="" manager="dwrede" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Limitación de consultas de Servicios móviles con paginación

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/es-es/develop/mobile/tutorials/add-paging-to-data-dotnet" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-js" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">iOS C#</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">C# para Android</a>
</div>

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación iOS desde Servicios móviles de Azure. En este tutorial se utilizarán las propiedades de consulta **Skip** y **Take** en el cliente para solicitar "páginas" específicas de datos.

<div class="dev-callout"><b>Nota:</b>
<p>Para evitar el desbordamiento de datos en clientes de dispositivos m&oacute;viles, Servicios m&oacute;viles implementa un l&iacute;mite autom&aacute;tico de p&aacute;ginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tama&ntilde;o de la p&aacute;gina, puede solicitar expl&iacute;citamente hasta 1.000 elementos en la respuesta.</p>
</div>

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos][]. Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos][].

1.  En Xamarin Studio, abra el proyecto que ha modificado al completar el tutorial [Introducción a los datos][].

2.  Presione el botón **Run** para generar el proyecto e iniciar la aplicación y, a continuación, especifique el texto en el cuadro de texto y haga clic en el icono con el signo más (**+**).

3.  Repita el paso anterior al menos tres veces de manera que disponga de más de tres elementos almacenados en la tabla TodoItem.

4.  Abra el archivo **TodoService.cs**, busque el método **RefreshDataAsync** y reemplace la consulta LINQ en `todoTable` por la siguiente consulta:

            Items = await todoTable
                            .Where (todoItem => todoItem.Complete == false)
                            .Take(3)
                            .ToListAsync();

    Esta consulta devuelve los tres elementos principales que no están marcados como completados.

5.  Recompile e inicie la aplicación.

    Observe que solo se muestran los tres primeros resultados de la tabla TodoItem.

6.  Actualice la consulta LINQ en el método **RefreshDataAsync** una vez más con lo siguiente:

            Items = await todoTable
                            .Where (todoItem => todoItem.Complete == false)
                            .Skip(3)
                            .Take(3)
                            .ToListAsync();

    Esta vez, establezca el valor **Skip** en 3.

    Esta consulta omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

    <div class="dev-callout"><b>Nota:</b>
<p>Este tutorial usa un escenario simplificado para definir valores de paginaci&oacute;n codificados de forma r&iacute;gida para las propiedades <strong>Skip</strong> y <strong>Take</strong>. En una aplicaci&oacute;n en tiempo real, puede usar consultas similares a las anteriores con un control de paginaci&oacute;n o interfaz de usuario comparable para permitir a los usuarios desplazarse a las p&aacute;ginas anteriores y posteriores. Tambi&eacute;n puede llamar al m&eacute;todo <strong>IncludeTotalCount</strong> para obtener el recuento total de elementos disponibles en el servidor junto con los datos paginados.</p>
</div>

## <a name="next-steps"> </a>Pasos siguientes

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. Considere la posibilidad de profundizar más en el siguiente tema de Servicios móviles:

-   [Introducción a la autenticación][]
    Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

<!-- * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app. --> <!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [C# para Tienda Windows]: /es-es/develop/mobile/tutorials/add-paging-to-data-dotnet "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/develop/mobile/tutorials/add-paging-to-data-js "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone"
  [iOS]: /es-es/develop/mobile/tutorials/add-paging-to-data-ios "iOS"
  [Android]: /es-es/develop/mobile/tutorials/add-paging-to-data-android "Android"
  [HTML]: /es-es/develop/mobile/tutorials/add-paging-to-data-html "HTML"
  [iOS C#]: /es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS"
  [C# para Android]: /es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android"
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-xamarin-ios
