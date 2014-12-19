<properties pageTitle="Introducción a las notificaciones de inserción (Tienda Windows) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga" />

# Incorporación de notificaciones de inserción a su aplicación de Servicios móviles (inserción heredada)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" class="current">C# para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">JavaScript para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/es-es/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/es-es/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title=".NET backend">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/"  title="JavaScript backend" class="current">Back-end de JavaScript</a></div>	

Este tema muestra cómo Visual Studio 2013 le permite utilizar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación de la Tienda Windows. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con los servicios de notificaciones de inserción de Windows (WNS) directamente desde Visual Studio. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

>[WACOM.NOTE]Este tema admite servicios móviles <em>existentes</em> que <em>todavía no se han actualizado</em> para usar la integración de Centros de notificaciones. Al crear un <em>servicio móvil</em> nuevo, esta funcionalidad integrada se habilita automáticamente. Para servicios móviles nuevos, consulte [Introducción a las notificaciones de inserción](/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/).
>
>Servicios móviles se integra con Centros de notificaciones de Azure para ofrecer compatibilidad con la funcionalidad de inserción adicional, como plantillas, varias plataformas y escala mejorada. <em>Debería actualizar los servicios móviles existentes para que usen Centros de notificaciones siempre que sea posible</em>. Una vez que haya realizado la actualización, consulte esta versión de [Introducción a las notificaciones de inserción](/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles]
2. [Actualización del código de notificación de inserción generado]
3. [Inserción de datos para recibir notificaciones]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a Servicios móviles] o [Introducción a los datos] para conectar su proyecto al servicio móvil. Si no se conecta un servicio móvil, el Asistente para agregar notificaciones de inserción se encarga de crear esta conexión. 

<h2><a name="register"></a>Incorporación y configuración de notificaciones de inserción en la aplicación</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Expanda <strong>servicios</strong>, <strong>servicios móviles</strong>, el nombre de servicio, abra el archivo de código generado y, a continuación, examine el método <strong>UploadChannel</strong> que obtiene el ID de instalación y el canal para el dispositivo e inserta estos datos en la nueva tabla de canales.</p> 

<p>El asistente también agrega una llamada a este método al controlador de eventos <strong>OnLaunched</strong> en el archivo de código App.xaml.cs. De esta forma se asegura que se intenta el registro del dispositivo siempre que se inicia la aplicación.</p></li> 
<li><p>En el Explorador de servidores, expanda <strong>Azure</strong>, <strong>Servicios móviles</strong>, su nombre de servicio y <strong>canales</strong> y, a continuación, abra el archivo insert.js.</p> 

<p>Este archivo, que se almacena en el servicio móvil, contiene código JavaScript que se ejecuta cuando un cliente envía una solicitud para registrar un dispositivo insertando datos en la tabla de canales.</p> 

<div class="dev-callout"><b>Nota:</b>
	<p>La versión inicial de este archivo contiene código que busca un registro existente para el dispositivo. También contiene código que envía una notificación de inserción cuando se agrega un nuevo registro a la tabla de canales. El código que envía una notificación de inserción se puede incluir en cualquier archivo de script registrado. La ubicación de este script depende de cómo se desencadena la notificación. Los scripts se pueden registrar en cualquier operación de inserción, actualización, eliminación o lectura en una tabla, como un trabajo programado o como una API personalizada. Para obtener más información, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">Uso de scripts del servidor en Servicios móviles</a>.</p>
</div>
</li> 
<li><p>Presione la tecla F5 para ejecutar la aplicación y comprobar que se recibe inmediatamente una notificación desde el servicio móvil.</p>
<p>Esta notificación se ha generado mediante la inserción de una fila en la nueva tabla de canales, que es el registro del dispositivo.</p>
</li>
</ol>
Aunque el código generado facilita la demostración de una notificación cuando se ejecuta la aplicación, no suele ser un escenario descriptivo. A continuación, quitará el código de notificación de la tabla de canales y lo reemplazará, con algunos cambios, en la tabla TodoItem. 

<h2><a name="update-scripts"></a>Actualización del código de notificación de inserción generado</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>Probar las notificaciones de inserción en su aplicación</h2>

1. En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2. En la aplicación, escriba un texto en **Insertar un TodoItem** y, a continuación, haga clic en **Guardar**.

   	![][13]

   	Tenga en cuenta que una vez finalizada la inserción, la aplicación recibe una notificación de inserción de WNS.

   	![][14]

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

+ [Introducción a los Centros de notificaciones]
  <br/>Aprenda a aprovechar los Centros de notificaciones en su aplicación de la Tienda Windows.

+ [Envío de notificaciones a los suscriptores]
	<br/>Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

+ [Envío de notificaciones a usuarios]
	<br/>Sepa cómo enviar notificaciones de inserción desde un Servicio móvil a usuarios específicos en cualquier dispositivo.

+ [Envío de notificaciones multiplataforma a los usuarios]
	<br/>Sepa cómo utilizar las plantillas para enviar notificaciones de inserción desde un Servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a los datos]
  <br/>Más información sobre cómo almacenar y consultar datos con Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

* [Referencia del script del servidor de Servicios móviles]
  <br/>Obtener más información acerca del registro y uso de scripts de servidor.

* [Referencia conceptual de Servicios móviles con .NET]
  <br/>Obtenga más información sobre el uso de Servicios móviles con .NET.

<!-- Anchors. -->
[Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles]: #register
[Actualización del código de notificación de inserción generado]: #update-scripts
[Inserción de datos para recibir notificaciones]: #test
[Pasos siguientes]:#next-steps

<!-- Images. -->











[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-dotnet/
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-dotnet
[Notificaciones de inserción para usuarios de la aplicación]: /es-es/develop/mobile/tutorials/push-notifications-to-users-dotnet
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript y HTML]: /es-es/develop/mobile/tutorials/get-started-with-push-js

[Portal de administración de Azure]: https://manage.windowsazure.com/
[wns, objeto]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library/
[Validar y modificar los datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introducción a los Centros de notificaciones]: /es-es/manage/services/notification-hubs/getting-started-windows-dotnet/
[¿Qué son los Centros de notificaciones?]: /es-es/develop/net/how-to-guides/service-bus-notification-hubs/
[Envío de notificaciones a los suscriptores]: /es-es/manage/services/notification-hubs/breaking-news-dotnet/
[Envío de notificaciones a usuarios]: /es-es/manage/services/notification-hubs/notify-users/
[Envío de notificaciones multiplataforma a los usuarios]: /es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
