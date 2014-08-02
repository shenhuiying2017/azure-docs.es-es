<properties linkid="develop-mobile-tutorials-authorize-users-in-scripts-xamarin-android" urlDisplayName="Authorize Users in Scripts (Xamarin.Android)" pageTitle="Authorize users in scripts (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure authorizing user, Xamarin.Android scripts authorization, authorize mobile services" description="Learn how to authorize users with scripts in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Use scripts to authorize users in Mobile Services" authors="" />

Uso de scripts para autorizar a los usuarios en Servicios móviles
=================================================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios "iOS")[Android](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-android "Android")[HTML](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-html "HTML")[iOS C\#](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios "Xamarin.iOS")[C\# para Android](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android "Xamarin.Android")

Este tema le muestra cómo usar scripts de servidor a fin de autorizar a usuarios autenticados para el acceso a los datos en Servicios móviles de Azure desde una aplicación Xamarin.Android. En este tutorial registrará los scripts con Servicios móviles para filtrar las consultas basadas en el userId de un usuario autenticado, asegurándose de que cada usuario puede ver solo sus propios datos.

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android). Antes de comenzar este tutorial, primero debe completar [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android).

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

    El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna **userId** a la tabla **TodoItem** en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación en la Tienda Windows.

5.  Repita los pasos 3 y 4 para reemplazar la operación **Read** existente por la siguiente función:

         function read(query, user, request) {
            query.where({ userId: user.userId });    
            request.execute();
         }

	Este script filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte.

Prueba de la aplicación
-----------------------

1.  En Xamarin Studio o Visual Studio, abra el proyecto que modificó al realizar el tutorial [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android).

2.  Haga clic en **Run** para iniciar la aplicación e iniciar sesión con el proveedor de identidades elegido.

	Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

3.  En la aplicación, escriba un texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

	De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userID correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

4.  Vuelva a la tabla **todoitem** en el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Browse** y compruebe que los elementos recién agregados dispongan ahora de un valor userId asociado.

5.  (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior.

Pasos siguientes
----------------

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android)
    <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android)
    <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/p/?LinkId=262293)
    <br/>Obtenga más información acerca del registro y uso de scripts de servidor.


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-xamarin-android
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Azure Management Portal]: https://manage.windowsazure.com/
