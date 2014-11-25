<properties pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Autorización en el servicio de usuarios de Servicios móviles

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS">iOS</a><a href="/es-es/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android">Android</a><a href="/es-es/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML">HTML</a><a href="/es-es/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/" title="Back-end de .NET">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/"  title="Back-end de JavaScript" class="current">Back-end de JavaScript</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tema le muestra c&oacute;mo realizar la autorizaci&oacute;n de usuarios autenticados para obtener acceso a los datos en Servicios m&oacute;viles de Azure desde una aplicaci&oacute;n de la Tienda Windows. En este tutorial registrar&aacute; los scripts con Servicios m&oacute;viles para filtrar las consultas basadas en el userId de un usuario autenticado, asegur&aacute;ndose de que cada usuario puede ver solo sus propios datos.</p>
<p>Puede ver la versi&oacute;n en v&iacute;deo de este tutorial haciendo clic en el clip de la derecha.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="label">Ver el tutorial (en ingl&eacute;s)</a> <a style="background-image: url('/media/devcenter/mobile/videos/authorize-users-with-scripts-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir v&iacute;deo (en ingl&eacute;s)</span></a> <span class="time">13:52:00</span></div>

</div>

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a la autenticación][Introducción a la autenticación]. Antes de comenzar este tutorial, primero debe completar [Introducción a la autenticación][Introducción a la autenticación].

## <a name="register-scripts"></a>Registro de scripts

Puesto que la aplicación de inicio rápido lee e inserta datos, tiene que registrar los scripts para esas operaciones en la tabla TodoItem.

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

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
<p>El esquema din&aacute;mico debe habilitarse la primera vez que se ejecute este script de inserci&oacute;n. Al tener habilitado dicho esquema, Servicios m&oacute;viles agrega autom&aacute;ticamente la columna <strong>userId</strong> a la tabla <strong>TodoItem</strong> en la primera ejecuci&oacute;n. El esquema din&aacute;mico est&aacute; habilitado de forma predeterminada para un nuevo servicio m&oacute;vil y debe deshabilitarse antes de publicar la aplicaci&oacute;n en la Tienda Windows.</p>
</div>

5.  Repita los pasos 3 y 4 para reemplazar la operación **Read** existente por la siguiente función:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Este script filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte.

## Prueba de la aplicación

1.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que modificó cuando completó el tutorial [Introducción a la autenticación][Introducción a la autenticación].

2.  Presione la tecla F5 para ejecutar la aplicación y, a continuación, inicie sesión con el proveedor de identidades seleccionado.

    Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

3.  En la aplicación, escriba un texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

    ![][3]

    De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userID correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

4.  Vuelva a la tabla **todoitem** en el [Portal de administración][Portal de administración de Azure], haga clic en **Browse** y compruebe que los elementos recién agregados dispongan ahora de un valor userId asociado.

5.  (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación (Alt+F4) y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior.

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos][Introducción a los datos]
    
	Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    
	Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    
	Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET]
    
	Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

 
 


  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
  [Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-dotnet
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library
