<properties pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Autorización en el servicio de usuarios de Servicios móviles
============================================================

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="Windows Store C#" class="current">C\# de Tienda Windows</a><a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="Windows Store JavaScript">[JavaScript de Tienda Windows</a><a href="/en-us/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android">Android</a><a href="/en-us/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML">HTML</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/" title=".NET backend">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tema le muestra cómo realizar la autorización de usuarios autenticados para obtener acceso a los datos en Servicios móviles de Azure desde una aplicación de la Tienda Windows. En este tutorial registrará los scripts con Servicios móviles para filtrar las consultas basadas en el userId de un usuario autenticado, asegurándose de que cada usuario puede ver solo sus propios datos.</p>

<p>Puede ver la versión en vídeo de este tutorial haciendo clic en el clip de la derecha.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="label">Ver el tutorial (en inglés)</a> <a style="background-image: url('/media/devcenter/mobile/videos/authorize-users-with-scripts-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo (en inglés)</span></a> <span class="time">13:52</span></div>
</div>

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet). Antes de comenzar este tutorial, primero debe completar [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet).

<a name="register-scripts"></a>
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

    <div class="dev-callout"><b>Nota:</b>

    <p>El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna <b>userId</b> a la tabla <b>TodoItem</b> en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación en la Tienda Windows.</p>
	</div>

5.  Repita los pasos 3 y 4 para reemplazar la operación **Read** existente por la siguiente función:

         function read(query, user, request) {
            query.where({ userId: user.userId });    
            request.execute();
         }

	Este script filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte.

Prueba de la aplicación
-----------------------

1.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que modificó cuando completó el tutorial [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet).

2.  Presione la tecla F5 para ejecutar la aplicación y, a continuación, inicie sesión con el proveedor de identidades seleccionado.

	Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

3.  En la aplicación, escriba un texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

	![][3]

	De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userID correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

4.  Vuelva a la tabla **todoitem** en el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Browse** y compruebe que los elementos recién agregados dispongan ahora de un valor userId asociado.

5.  (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación (Alt+F4) y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior.

Pasos siguientes
----------------

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/)
    Obtenga más información sobre almacenar y consultar datos con los Servicios móviles.

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet)
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
    Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript and HTML]: mobile-services-win8-javascript/

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
