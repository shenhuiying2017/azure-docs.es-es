<properties pageTitle="Autorización en el servicio (Windows Phone) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# Autorización en el servicio de usuarios de Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tema le muestra cómo usar scripts de servidor a fin de autorizar a usuarios autenticados a obtener acceso a los datos en Servicios móviles de Azure desde una aplicación de Windows Phone 8. En este tutorial registrará los scripts con Servicios móviles para filtrar las consultas basadas en el userId de un usuario autenticado, asegurándose de que cada usuario puede ver solo sus propios datos.</p>
<p>Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, <a href="/es-es/develop/mobile/tutorials/get-started-with-users-wp8">Introducción a la autenticación</a>. Antes de comenzar este tutorial, primero debe completar <a href="/es-es/develop/mobile/tutorials/get-started-with-users-wp8">Introducción a la autenticación</a>.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="label">Ver el tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-scripts-for-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir video</span></a> <span class="time">15:00</span></div>
</div> 

## <a name="register-scripts"></a>Registro de scripts
Puesto que la aplicación de inicio rápido lee e inserta datos, tiene que registrar los scripts para esas operaciones en la tabla TodoItem.

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación. 

   	![][0]

2. Haga clic en la pestaña **Datos** y, a continuación, en la tabla **TodoItem**.

   	![][1]

3. Haga clic en **Script** y, a continuación, seleccione la operación **Insert**.

   	![][2]

4. Reemplace el script existente por la siguiente función y, a continuación, haga clic en **Guardar**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Este script agrega un valor userID al elemento, que es el identificador de usuario del usuario autenticado, antes de que se inserte en la tabla TodoItem. 

    <div class="dev-callout"><b>Nota:</b>
	<p>El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna <strong>userId</strong> a la tabla <strong>TodoItem</strong> en la primera ejecución. El esquema dinámico está habilitado de manera predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación en la Tienda de Windows Phone.</p>
    </div>


5. Repita los pasos 3 y 4 para reemplazar la operación **Read** existente por la siguiente función:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Este script filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte.

## Prueba de la aplicación

1. En Visual Studio 2012 Express para Windows Phone, abra el proyecto que modificó cuando completó el tutorial [Introducción a la autenticación].

2. Presione la tecla F5 para ejecutar la aplicación y, a continuación, inicie sesión con el proveedor de identidades seleccionado. 

   	Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

3. En la aplicación, escriba el texto en el cuadro de texto y, a continuación, haga clic en **Guardar**.

   	![][3]

   	De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento tiene el valor userId correcto, el servicio móvil lo devuelve.

5. Vuelva a la tabla **todoitem** en el [Portal de administración][Portal de administración de Azure], haga clic en **Examinar** y compruebe que los elementos recién agregados dispongan ahora de un valor userId asociado.

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a los datos]
  <br/>Más información sobre cómo almacenar y consultar datos con Servicios móviles.

* [Introducción a las notificaciones de inserción] 
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Referencia del script del servidor de Servicios móviles]
  <br/>Obtener más información acerca del registro y uso de scripts de servidor.

<!-- Anchors. -->
[Registro de scripts de servidor]: #register-scripts
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup-wp8.png

<!-- URLs. -->
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Panel Mis aplicaciones]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-wp8
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-wp8
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-wp8

[Portal de administración de Azure]: https://manage.windowsazure.com/
