<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Add paging to data (HTML5)" pageTitle="Add paging to data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your HTML app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Limitación de consultas de Servicios móviles con paginación

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/es-es/develop/mobile/tutorials/add-paging-to-data-dotnet" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-js" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación HTML desde Servicios móviles de Azure. En este tutorial se utilizarán los métodos de consulta **Take** y **Skip** en el cliente para solicitar "páginas" específicas de datos.

<div class="dev-callout"><b>Nota:</b>
<p>Para evitar el desbordamiento de datos en clientes de dispositivos m&oacute;viles, Servicios m&oacute;viles implementa un l&iacute;mite autom&aacute;tico de p&aacute;ginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tama&ntilde;o de la p&aacute;gina, puede solicitar expl&iacute;citamente hasta 1.000 elementos en la respuesta.</p>
</div>

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos][Introducción a los datos]. Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos][Introducción a los datos].

1.  Ejecute uno de los siguientes archivos de comando desde la subcarpeta **server** del proyecto que modificó cuando completó el tutorial [Introducción a los datos][Introducción a los datos].

    -   **launch-windows** (equipos con Windows)
    -   **launch-mac.command** (equipos con Mac OS X)
    -   **launch-linux.sh** (equipos con Linux)

    <div class="dev-callout"><b>Nota:</b>
    <p>En un equipo con Windows, escriba &quot;R&quot; cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podr&iacute;a advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador contin&uacute;e con la carga del script.</p>
</div>

    De este modo se inicia un servidor web en su equipo local para hospedar la aplicación.

2.  En un explorador web, navegue a <http://localhost:8000/>, escriba algún texto en **Add new task** y haga clic en **Add**.

3.  Repita el paso anterior al menos tres veces de manera que disponga de más de tres elementos almacenados en la tabla TodoItem.

4.  En el archivo app.js, reemplace la definición de la variable `query` en el método **refreshTodoItems** con el siguiente código de línea:

        var query = todoItemTable.where({ complete: false }).take(3);

    Cuando se ejecuta, esta consulta devuelve los tres elementos principales que no están marcados como completados.

5.  Vuelva al explorador web y cargue nuevamente la página.

    Observe que solo se muestran los tres primeros resultados de la tabla TodoItem.

6.  (Opcional) Vea el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler].

    Observe que el método **take(3)** se ha traducido en la opción de consulta **$top=3** en el URI de la consulta.

7.  Actualice la consulta una vez más con el código siguiente:

        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

8.  Vuelva al explorador web y cargue nuevamente la página.

    Esta consulta omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

    <div class="dev-callout"><b>Nota:</b>
<p>Este tutorial usa un escenario simplificado para pasar valores de paginaci&oacute;n codificados de forma r&iacute;gida a los m&eacute;todos <strong>Take</strong> y <strong>Skip</strong>. En una aplicaci&oacute;n en tiempo real, puede usar consultas similares a las anteriores con un control de paginaci&oacute;n o interfaz de usuario comparable para permitir a los usuarios desplazarse a las p&aacute;ginas anteriores y posteriores.  Tambi&eacute;n puede llamar al m&eacute;todo <strong>includeTotalCount</strong> para obtener el recuento total de elementos disponibles en el servidor, junto con los datos paginados.</p>
</div>

9.  (Opcional) Vea de nuevo el URI de la solicitud enviada al servicio móvil.

    Observe que el método **skip(3)** se ha traducido en la opción de consulta **$skip=3** en el URI de la consulta.

## <a name="next-steps"> </a>Pasos siguientes

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. A continuación, aprenda a autenticar usuarios de su aplicación en [Introducción a la autenticación][Introducción a la autenticación]. Encontrará más información acerca de cómo usar los Servicios móviles con HTML/JavaScript en [Referencia conceptual de Servicios móviles HTML/JavaScript][Referencia conceptual de Servicios móviles HTML/JavaScript]

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-html
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-html
  [Referencia conceptual de Servicios móviles HTML/JavaScript]: /es-es/develop/mobile/how-to-guides/work-with-html-js-client
