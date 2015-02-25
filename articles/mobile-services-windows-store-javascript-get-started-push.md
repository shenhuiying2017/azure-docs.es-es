<properties pageTitle="Introducción a las notificaciones de inserción (inserción heredada) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo usar Servicios móviles de Azure para enviar notificaciones de inserción a la aplicación de JavaScript de la Tienda Windows (inserción heredada)." services="mobile-services, notification-hubs" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="11/22/2014" ms.author="glenga"/>

# Incorporación de notificaciones de inserción a su aplicación de Servicios móviles (inserción heredada)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">C# para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript" class="current">JavaScript para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/es-es/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/es-es/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title=".NET backend">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="JavaScript backend" class="current">Back-end de JavaScript</a></div>		

Este tema muestra cómo Visual Studio 2013 le permite utilizar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación de la Tienda Windows. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con los servicios de notificaciones de inserción de Windows (WNS) directamente desde Visual Studio. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

>[AZURE.NOTE]En este tema se tratan móviles <em>existentes</em> que todavía <em>no se han actualizado</em> para usar la integración de Centros de notificaciones. Cuando crea un <em>nuevo</em> servicio móvil, esta funcionalidad integrada se habilita automáticamente. Para servicios móviles nuevos, consulte [Introducción a las notificaciones de inserción](/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).
>
>La solución Servicios móviles se integra con Centros de notificaciones de Azure para ofrecer compatibilidad con la funcionalidad de notificación de inserción adicional, como plantillas, varias plataformas y escala. <em>Debería actualizar los servicios móviles existentes para que usen Centros de notificaciones siempre que sea posible</em>. Una vez que haya realizado la actualización, consulte esta versión de [Introducción a las notificaciones de inserción](/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles]
2. [Actualización del código de notificación de inserción generado]
3. [Inserción de datos para recibir notificaciones]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles] o [Introducción a los datos] para conectar su proyecto al servicio móvil. Si no se conecta un servicio móvil, el Asistente para agregar notificaciones de inserción se encarga de crear esta conexión. 

<h2><a name="register"></a>Incorporación y configuración de notificaciones de inserción en la aplicación</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Abra el archivo de código push.register.js generado y examine el código que obtiene el identificador de instalación y el canal del dispositivo e inserta estos datos en la tabla <strong>channels</strong> nueva.</p> 

	<p>El Asistente para agregar notificaciones de inserción creó esta tabla en su servicio móvil. Este código garantiza que se intenta el registro del dispositivo siempre que se activa la aplicación.</p></li>
<li><p>En el Explorador de servidores, expanda <strong>Azure</strong>, <strong>Servicios móviles</strong>, su nombre de servicio y <strong>channels</strong> y, a continuación, abra el archivo insert.js.</p> 

<p>Este archivo, que se almacena en el servicio móvil, contiene código JavaScript que se ejecuta cuando un cliente envía una solicitud para registrar un dispositivo insertando datos en la tabla de canales.</p> 

> [AZURE.NOTE] La versión inicial de este archivo contiene código que busca un registro existente para el dispositivo. También contiene código que envía una notificación de inserción cuando se agrega un nuevo registro a la tabla de canales. El código que envía una notificación de inserción se puede incluir en cualquier archivo de script registrado. La ubicación de este script depende de cómo se desencadena la notificación. Los scripts se pueden registrar en cualquier operación de inserción, actualización, eliminación o lectura en una tabla, como un trabajo programado o como una API personalizada. Para obtener más información, consulte [Uso de scripts del servidor en Servicios móviles](http://go.microsoft.com/fwlink/p/?LinkID=287178).
</li> 
<li><p>Presione la tecla F5 para ejecutar la aplicación y comprobar que se recibe inmediatamente una notificación desde el servicio móvil.</p>
<p>Esta notificación se ha generado mediante la inserción de una fila en la nueva tabla de canales, que es el registro del dispositivo.</p>
</li>
</ol>

Aunque el código generado facilita la demostración de una notificación cuando se ejecuta la aplicación, no suele ser un escenario descriptivo. A continuación, quitará el código de notificación de la tabla de canales y lo reemplazará, con algunos cambios, en la tabla TodoItem. 

<h2><a name="update-scripts"></a>Actualización del código de notificación de inserción generado</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>Prueba de las notificaciones de inserción en su aplicación</h2>

1. En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2. En la aplicación, escriba texto en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

   	![][13]

   	Tenga en cuenta que una vez finalizada la inserción, la aplicación recibe una notificación de inserción de WNS.

   	![][14]

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demuestra la funcionalidad de notificación de inserción básica que brinda Servicios móviles. Si su aplicación requiere funcionalidades más avanzadas, como enviar notificaciones entre plataformas, enrutamiento basado en suscripción o volúmenes de tamaño muy grande, considere utilizar Centros de notificaciones de Azure con su servicio móvil. Para obtener más información, consulte uno de los siguientes temas de Centros de notificaciones:

+ [Introducción a los Centros de notificaciones]
  <br/>Aprenda a aprovechar los Centros de notificaciones en su aplicación de la Tienda Windows.

+ [Envío de notificaciones de difusión a los suscriptores]
	<br/>Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

+ [Envío de notificaciones a usuarios]
	<br/>Obtenga información sobre cómo enviar notificaciones de inserción desde un servicio móvil a usuarios específicos en cualquier dispositivo.

+ [Envío de notificaciones entre plataformas a los usuarios]
	<br/>Obtenga información sobre cómo usar las plantillas para enviar notificaciones de inserción desde un servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

* [Referencia del script de servidor de Servicios móviles]
  <br/>Obtenga más información acerca del registro y del uso de scripts de servidor.

* [Referencia conceptual de Servicios móviles con HTML/JavaScript]
  <br/>Obtenga más información acerca de cómo utilizar los Servicios móviles con HTML y JavaScript.  

<!-- Anchors. -->
[Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles]: #register
[Actualización del código de notificación de inserción generado]: #update-scripts
[Inserción de datos para recibir notificaciones]: #test
[Pasos siguientes]:#next-steps

<!-- Images. -->







[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-js/
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-js
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-js
[Notificaciones de inserción a los usuarios de aplicaciones]: /es-es/develop/mobile/tutorials/push-notifications-to-users-js
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript y HTML]: /es-es/develop/mobile/tutorials/get-started-with-push-js

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con HTML/JavaScript]: /es-es/develop/mobile/how-to-guides/work-with-html-js-client/
[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introducción a los Centros de notificaciones]: /es-es/manage/services/notification-hubs/getting-started-windows-dotnet/
[¿Qué son los Centros de notificaciones?]: /es-es/develop/net/how-to-guides/service-bus-notification-hubs/
[Envío de notificaciones de difusión a los suscriptores]: /es-es/manage/services/notification-hubs/breaking-news-dotnet/
[Envío de notificaciones a usuarios]: /es-es/manage/services/notification-hubs/notify-users/
[Envío de notificaciones entre plataformas a los usuarios]: /es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/


<!--HONumber=42-->
