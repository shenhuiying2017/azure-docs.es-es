<properties pageTitle="Autorización en el servicio (Android) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# Autorización en el servicio de usuarios de Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	

En este tema se muestra cómo usar scripts de servidor para autorizar a los usuarios autenticados a obtener acceso a datos en Servicios móviles de Azure desde una aplicación Android.  En este tutorial registrará los scripts con Servicios móviles para filtrar las consultas basadas en el identificador de usuario de un usuario autenticado, asegurándose de que cada usuario puede ver solo sus propios datos.

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a la autenticación]. Antes de comenzar este tutorial, primero debe completar [Introducción a la autenticación].  

## <a name="register-scripts"></a>Registro de scripts
Puesto que la aplicación de inicio rápido lee e inserta datos, tiene que registrar los scripts para esas operaciones en la tabla TodoItem.

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación. 

   	![][0]

2. Haga clic en la pestaña **Datos** y, a continuación, haga clic en la tabla **TodoItem**.

   	![][1]

3. Haga clic en **Script** y, a continuación, seleccione la operación **Insertar**.

   	![][2]

4. Sustituya el script existente por la siguiente función y, a continuación, haga clic en **Guardar**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Este script agrega un valor userId al elemento, que es el identificador de usuario del usuario autenticado, antes de que se inserte en la tabla TodoItem. 

    <div class="dev-callout"><b>Nota:</b>
	<p>El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna <strong>userId</strong> a la tabla <strong>TodoItem</strong> en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación en la Tienda Windows.</p>
    </div>


5. Repita los pasos 3 y 4 para reemplazar la operación **Leer** existente por la siguiente función:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Este script filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte.

## Prueba de la aplicación

1. En Eclipse, abra el proyecto que ha modificado al completar el tutorial [Introducción a la autenticación].

2. En el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar) para iniciar la aplicación e inicie sesión con el proveedor de identidades que haya elegido. 

   	Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

3. En la aplicación, escriba un texto en **Insert a TodoItem** (Insertar una tarea pendiente) y, a continuación, haga clic en **Save** (Guardar).

   	De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userID correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

5. Vuelva a la tabla **todoitem** del [Portal de administración][Portal de administración de Azure], haga clic en **Examinar** y verifique que cada tarea nueva agregada tiene un valor userId asociado.

6. (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior.

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a los datos]
  <br/>Obtenga más información sobre el almacenamiento y la consulta de datos mediante Servicios móviles.

* [Introducción a las notificaciones de inserción] 
  <br/>Obtenga información sobre cómo enviar a la aplicación una notificación de inserción muy básica.

* [Referencia del script del servidor de Servicios móviles]
  <br/>Obtenga más información sobre el registro y uso de scripts de servidor.

<!-- Anchors. -->
[Registro de scripts de servidor]: #register-scripts
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Panel Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-android
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-android
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-android
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-android

[Portal de administración de Azure]: https://manage.windowsazure.com/
