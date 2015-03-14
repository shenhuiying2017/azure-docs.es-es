<properties 
	pageTitle="Autorización del servicio (Tienda Windows) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo autorizar a los usuarios en el back-end de JavaScript de Servicios móviles de Azure." 
	services="" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="glenga"/>

# Autorización en el servicio de usuarios de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	

Este tema le muestra cómo utilizar los scripts de servidor para autorizar a los usuarios autenticados a obtener acceso a los datos en Servicios móviles de Azure desde una aplicación de la Tienda Windows.  En este tutorial registrará los scripts con Servicios móviles para filtrar las consultas basadas en el userId de un usuario autenticado, asegurándose de que cada usuario puede ver solo sus propios datos.

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a la autenticación]. Antes de comenzar este tutorial, primero debe completar [Introducción a la autenticación].  

## <a name="register-scripts"></a>Registro de scripts
Puesto que la aplicación de inicio rápido lee e inserta datos, tiene que registrar los scripts para esas operaciones en la tabla TodoItem.

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación. 

   	![][0]

2. Haga clic en la pestaña **Datos** y, a continuación, haga clic en la tabla **TodoItem**.

   	![][1]

3. Haga clic en **Script** y, a continuación, seleccione la operación **Insert**.

   	![][2]

4. Sustituya el script existente por la siguiente función y, a continuación, haga clic en **Guardar**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Este script agrega un valor userId al elemento, que es el identificador de usuario del usuario autenticado, antes de que se inserte en la tabla TodoItem. 

    > [AZURE.NOTE] El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna **userId** a la tabla **TodoItem** en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación en la Tienda Windows.


5. Repita los pasos 3 y 4 para reemplazar la operación **Read** existente por la siguiente función:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Este script filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte.

## Prueba de la aplicación

1. En Visual Studio 2012 Express para Windows 8, abra el proyecto que modificó cuando completó el tutorial [Introducción a la autenticación].

2. Presione la tecla F5 para ejecutar la aplicación y, a continuación, inicie sesión con el proveedor de identidades seleccionado. 

   	Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

3. En la aplicación, escriba texto en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

   	![][3]

   	De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userID correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

5. De vuelta en la tabla **todoitem** del [Portal de administración][Portal de administración de Azure], haga clic en **Examinar** y compruebe que los elementos recién agregados tienen ahora un valor userId asociado.

6. (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación (Alt+F4) y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior. 

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Referencia del script de servidor de Servicios móviles]
  <br/>Obtenga más información acerca del registro y del uso de scripts de servidor.

<!-- Anchors. -->
[Registro de scripts de servidor]: #register-scripts
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Notificaciones de inserción de Windows y de Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Panel Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introducción a los servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-js
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-js
[JavaScript y HTML]: mobile-services-win8-javascript/

[Portal de administración de Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
