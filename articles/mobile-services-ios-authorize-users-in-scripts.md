<properties pageTitle="Autorización en el servicio (iOS) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Autorización en el servicio de usuarios de Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]


En este tema se muestra cómo usar scripts de servidor para autorizar a los usuarios autenticados obtener acceso a datos en servicios móviles de Azure desde una aplicación de iOS.   En este tutorial registrará los scripts en Servicios móviles para filtrar las consultas basadas en el userId de un usuario autenticado, asegurándose de que cada usuario puede ver solo sus propios datos.

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a la autenticación]. Antes de comenzar este tutorial, debe completar [Introducción a la autenticación].  

## <a name="register-scripts"></a>Registro de scripts
Puesto que la aplicación de inicio rápido lee e inserta datos, tiene que registrar los scripts para esas operaciones en la tabla TodoItem.

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y elija su aplicación.

   	![][0]

2. Haga clic en la pestaña **Datos** y elija la tabla **TodoItem**.

   	![][1]

3. Haga clic en **Script**, seleccione la operación **Insertar**.

   	![][2]

4. Reemplace el script existente por la siguiente función y haga clic en **Guardar**.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }

    Este script agrega un valor userId al elemento, que es el identificador de usuario del usuario autenticado, antes de que se inserte en la tabla TodoItem.

    <div class="dev-callout"><b>Nota:</b>
	<p>El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna <strong>userId</strong> a la tabla <strong>TodoItem</strong> en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación en la Tienda Windows.</p>
    </div>


5. Repita los pasos 3 y 4 para reemplazar la operación **Read** actual por la siguiente función:

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }

   	Este script filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte.

## Prueba de la aplicación

1. En Xcode, abra el proyecto que ha modificado al completar el tutorial [Introducción a la autenticación].

2. Presione el botón **Run** (Ejecutar) para compilar el proyecto, inicie la aplicación en el emulador de iPhone e inicie sesión con su proveedor de identidades elegido.

   	Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

3. En la aplicación, escriba texto en **Insert a TodoItem** y haga clic en **Guardar**.

   	![][3]

   	De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userID correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

5. De nuevo en la tabla **todoitem** en el [Portal de administración][Portal de administración de Azure ], haga clic en **Examinar** y compruebe que los elementos recién agregados dispongan ahora de un valor userId asociado.

6. (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior.

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a los datos]
  <br/>Más información sobre cómo almacenar y consultar datos con Servicios móviles.

* [Introducción a las notificaciones de inserción]
  <br/>Vea cómo enviar una notificación de inserción muy básica a su aplicación.

* [Referencia del script del servidor de Servicios móviles]
  <br/>Más información acerca del registro y uso de scripts de servidor.

<!-- Anchors. -->
[Registro de scripts de servidor]: #register-scripts
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->

[Notificaciones de inserción de Windows y Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Panel Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-ios
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-ios
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-ios

[Portal de administración de Azure]: https://manage.windowsazure.com/
