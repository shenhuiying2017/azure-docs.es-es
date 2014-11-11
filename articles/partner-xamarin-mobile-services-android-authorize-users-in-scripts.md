<properties linkid="develop-mobile-tutorials-authorize-users-in-scripts-xamarin-android" urlDisplayName="Authorize Users in Scripts (Xamarin.Android)" pageTitle="Authorize users in scripts (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure authorizing user, Xamarin.Android scripts authorization, authorize mobile services" description="Learn how to authorize users with scripts in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Use scripts to authorize users in Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Uso de scripts para autorizar a los usuarios en Servicios móviles

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/es-es/develop/mobile/tutorials/authorize-users-in-scripts-dotnet" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/authorize-users-in-scripts-js" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/authorize-users-in-scripts-wp8" title="Windows Phone">Windows Phone</a><a href="/es-es/develop/mobile/tutorials/authorize-users-in-scripts-ios" title="iOS">iOS</a><a href="/es-es/develop/mobile/tutorials/authorize-users-in-scripts-android" title="Android">Android</a><a href="/es-es/develop/mobile/tutorials/authorize-users-in-scripts-html" title="HTML">HTML</a><a href="/es-es/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios" title="Xamarin.iOS">iOS C#</a><a href="/es-es/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android" title="Xamarin.Android" class="current">C# para Android</a>
</div>

Este tema le muestra cómo usar scripts de servidor a fin de autorizar a usuarios autenticados para el acceso a los datos en Servicios móviles de Azure desde una aplicación Xamarin.Android. En este tutorial registrará los scripts con Servicios móviles para filtrar las consultas basadas en el userId de un usuario autenticado, asegurándose de que cada usuario puede ver solo sus propios datos.

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

1.  En Xamarin Studio o Visual Studio, abra el proyecto que modificó al realizar el tutorial [Introducción a la autenticación][Introducción a la autenticación].

2.  Haga clic en **Run** para iniciar la aplicación e iniciar sesión con el proveedor de identidades elegido.

    Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

3.  En la aplicación, escriba un texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

    De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userID correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

4.  Vuelva a la tabla **todoitem** en el [Portal de administración][Portal de administración de Azure], haga clic en **Browse** y compruebe que los elementos recién agregados dispongan ahora de un valor userId asociado.

5.  (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior.

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos][Introducción a los datos]
    Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    Obtenga más información acerca del registro y uso de scripts de servidor.



  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [0]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-xamarin-android
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=262293
