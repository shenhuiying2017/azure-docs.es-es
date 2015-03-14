<properties 
	pageTitle="Autorización del servicio (HTML) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo autorizar a los usuarios en el back-end de JavaScript de de Servicios móviles de Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="glenga"/>

# Autorización en el servicio de usuarios de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	


Este tema le muestra cómo usar scripts de servidor a fin de autorizar a usuarios autenticados a obtener acceso a los datos en Servicios móviles de Azure desde una aplicación HTML.  En este tutorial registrará los scripts con Servicios móviles para filtrar las consultas basadas en el userId de un usuario autenticado, asegurándose de que cada usuario puede ver solo sus propios datos.

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

    > [AUZRE.IMPORTANT] El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Con el esquema dinámico habilitado, Servicios móviles agrega automáticamente la columna **userId** a la tabla **TodoItem** en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación.

5. Repita los pasos 3 y 4 para reemplazar la operación **Read** existente por la siguiente función:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Este script filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte.

## Prueba de la aplicación

1. En un explorador web, navegue a la página index.html de la aplicación y, a continuación, inicie sesión con el proveedor de identidades que prefiera. 

   	Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userId y ahora cuentan con valores nulos.

3. En la aplicación, escriba texto en **Enter new task** y, a continuación, haga clic en **Add**.

   	![][3]

   	De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userId correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

5. De nuevo en la tabla **todoitem** del [Portal de administración][Portal de administración de Azure], haga clic en **Examinar** y compruebe que cada elemento recién agregado cuenta ahora con un valor userId asociado.

6. (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación (Alt+F4) y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior. 

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a los datos]
  <br/>Más información sobre cómo almacenar y consultar datos con Servicios móviles.

* [Referencia del script de servidor de Servicios móviles]
  <br/>Obtenga más información acerca del registro y del uso de scripts de servidor.

* [Servicios móvilesHTML/JavaScript Referencia conceptual]
  <br/>Obtenga más información sobre el uso de Servicios móviles con HTML/JavaScript.
  
<!-- Anchors. -->
[Registro de scripts de servidor]: #register-scripts
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-quickstart-startup-html.png

<!-- URLs. -->
[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Panel Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introducción a los servicios móviles]: /es-es/develop/mobile/tutorials/get-started-html
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-html
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-html

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con HTML/JavaScript]: /es-es/develop/mobile/how-to-guides/work-with-html-js-client


<!--HONumber=42-->
