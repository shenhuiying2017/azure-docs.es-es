<properties pageTitle="Service-side authorization (HTML) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Autorización en el servicio de usuarios de Servicios móviles
============================================================

[C\# para Tienda Windows](/en-us/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "JavaScript para Tienda Windows")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS")[Android](/en-us/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android")[HTML](/en-us/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android")

[Back-end de JavaScript](/en-us/documentation/articles/mobile-services-html-authorize-users-in-scripts/ "Back-end de JavaScript")

Este tema le muestra cómo usar scripts de servidor a fin de autorizar a usuarios autenticados a obtener acceso a los datos en Servicios móviles de Azure desde una aplicación HTML. En este tutorial registrará los scripts con Servicios móviles para filtrar las consultas basadas en el userId de un usuario autenticado, asegurándose de que cada usuario puede ver solo sus propios datos.

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-html). Antes de comenzar este tutorial, primero debe completar [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-html).

Registro de scripts
-------------------

Puesto que la aplicación de inicio rápido lee e inserta datos, tiene que registrar los scripts para esas operaciones en la tabla TodoItem.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

  ![][0]

2.  Haga clic en la pestaña **Data** y, a continuación, haga clic en la tabla **TodoItem**.

  ![][1]

3.  Haga clic en **Script** y, a continuación, seleccione la operación **Insert**.

  ![][2]

4.  Sustituya el script existente por la siguiente función y, a continuación, haga clic en **Save**.

         function insert(item, user, request) {
           item.userId = user.userId;    
           request.execute();
         }

  Este script agrega un valor userId al elemento, que es el identificador de usuario del usuario autenticado, antes de que se inserte en la tabla TodoItem.

    **Nota:**

    El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna **userId** a la tabla **TodoItem** en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación.

5.  Repita los pasos 3 y 4 para reemplazar la operación **Read** existente por la siguiente función:

         function read(query, user, request) {
            query.where({ userId: user.userId });    
            request.execute();
         }

  Este script filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte.

Prueba de la aplicación
-----------------------

1.  En un explorador web, navegue a la página index.html de la aplicación y, a continuación, inicie sesión con el proveedor de identidades que prefiera.

  Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

2.  En la aplicación, escriba un texto en **Enter new task** y, a continuación, haga clic en **Add**.

  ![][3]

  De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userID correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

3.  Vuelva a la tabla **todoitem** en el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Browse** y compruebe que los elementos recién agregados dispongan ahora de un valor userId asociado.

4.  (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación (Alt+F4) y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior.

Pasos siguientes
----------------

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-html)
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/p/?LinkId=262293)
  <br/>Obtenga más información acerca del registro y uso de scripts de servidor.

* [Referencia conceptual de Servicios móviles con HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)
  <br/>Obtenga más información acerca de cómo utilizar los Servicios móviles con HTML/JavaScript.


<!-- Images. -->
[0]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-quickstart-startup-html.png