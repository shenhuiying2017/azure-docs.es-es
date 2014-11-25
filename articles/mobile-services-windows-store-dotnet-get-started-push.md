<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga" />

# Introducción a las notificaciones de inserción en Servicios móviles (inserción heredada)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/es-es/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/es-es/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Back-end de .NET">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/"  title="Back-end de JavaScript" class="current">Back-end de JavaScript</a></div>

Este tema muestra cómo Visual Studio 2013 le permite utilizar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación de la Tienda Windows. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con los servicios de notificaciones de inserción de Windows (WNS) directamente desde Visual Studio. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

> [WACOM.NOTE] Este tema admite *servicios móviles* existentes que *todavía no se hayan actualizado para usar* la integración con Centros de notificaciones. Al crear un *servicio móvil* nuevo, esta funcionalidad integrada se habilita automáticamente. Para servicios móviles nuevos, consulte [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción].
>
> La solución Servicios móviles se integra con Centros de notificaciones de Azure para ofrecer compatibilidad con la funcionalidad de notificación de inserción adicional, como plantillas, varias plataformas y escala. *Debería actualizar los servicios móviles existentes para que usen Centros de notificaciones siempre que sea posible*. Una vez que haya realizado la actualización, consulte esta versión de [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción].

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles][Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles]
2.  [Actualización del código de notificación de inserción generado][Actualización del código de notificación de inserción generado]
3.  [Inserción de datos para recibir notificaciones][Inserción de datos para recibir notificaciones]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos] para conectar su proyecto al servicio móvil. Si no se conecta un servicio móvil, el Asistente para agregar notificaciones de inserción se encarga de crear esta conexión.

## <a name="register"></a><span class="short-header">Registro de la aplicación</span>Incorporación y configuración de notificaciones de inserción en la aplicación

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Expanda **servicios**, **servicios móviles**, su nombre de servicio, abra el archivo de código generado y, a continuación, examine el método **UploadChannel** que obtiene el ID de instalación y el canal para el dispositivo e inserta estos datos en la nueva tabla de canales.

    El asistente también agrega una llamada a este método al controlador de eventos **OnLaunched** en el archivo de código App.xaml.cs. De esta forma se asegura que se intenta el registro del dispositivo siempre que se inicia la aplicación.

2.  En el Explorador de servidores, expanda **Azure**, **Servicios móviles**, su nombre de servicio y **channels** y, a continuación, abra el archivo insert.js.

    Este archivo, que se almacena en el servicio móvil, contiene código JavaScript que se ejecuta cuando un cliente envía una solicitud para registrar un dispositivo insertando datos en la tabla de canales.

    <div class="dev-callout"><b>Nota:</b>
    <p>La versi&oacute;n inicial de este archivo contiene c&oacute;digo que busca un registro existente para el dispositivo. Tambi&eacute;n contiene c&oacute;digo que env&iacute;a una notificaci&oacute;n de inserci&oacute;n cuando se agrega un nuevo registro a la tabla de canales. El c&oacute;digo que env&iacute;a una notificaci&oacute;n de inserci&oacute;n se puede incluir en cualquier archivo de script registrado. La ubicaci&oacute;n de este script depende de c&oacute;mo se desencadena la notificaci&oacute;n. Los scripts se pueden registrar en cualquier operaci&oacute;n de inserci&oacute;n, actualizaci&oacute;n, eliminaci&oacute;n o lectura en una tabla, como un trabajo programado o como una API personalizada. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">Uso de scripts del servidor en Servicios m&oacute;viles</a>.</p>
</div>

3.  Presione la tecla F5 para ejecutar la aplicación y comprobar que se recibe inmediatamente una notificación desde el servicio móvil.

    Esta notificación se ha generado mediante la inserción de una fila en la nueva tabla de canales, que es el registro del dispositivo.

Aunque el código generado facilita la demostración de una notificación cuando se ejecuta la aplicación, no suele ser un escenario descriptivo. A continuación, quitará el código de notificación de la tabla de canales y lo reemplazará, con algunos cambios, en la tabla TodoItem.

## <a name="update-scripts"></a><span class="short-header">Actualización del código</span>Actualización del código de notificación de inserción generado

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

## <a name="test"></a><span class="short-header">Prueba de la aplicación</span>Pruebas de notificaciones de inserción en su aplicación

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  En la aplicación, escriba un texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

    ![][0]

    Tenga en cuenta que una vez finalizada la inserción, la aplicación recibe una notificación de inserción de WNS.

    ![][1]

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Introducción a los Centros de notificaciones][Introducción a los Centros de notificaciones]
    
	Aprenda a aprovechar Centros de notificaciones en su aplicación de la Tienda Windows.

-   [Envío de notificaciones a los suscriptores][Envío de notificaciones a los suscriptores]
    
	Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

-   [Envío de notificaciones a los usuarios][Envío de notificaciones a los usuarios]
    
	Obtenga información sobre cómo enviar notificaciones de inserción desde un servicio móvil a usuarios específicos en cualquier dispositivo.

-   [Envío de notificaciones entre plataformas a los usuarios][Envío de notificaciones entre plataformas a los usuarios]
    
	Obtenga información sobre cómo usar las plantillas para enviar notificaciones de inserción desde un servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos][Introducción a los datos]
    
	Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]
    
	Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    
	Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET]
    
	Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

 
 


  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles]: #register
  [Actualización del código de notificación de inserción generado]: #update-scripts
  [Inserción de datos para recibir notificaciones]: #test
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-dotnet/
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png
  [Introducción a los Centros de notificaciones]: /es-es/manage/services/notification-hubs/getting-started-windows-dotnet/
  [Envío de notificaciones a los suscriptores]: /es-es/manage/services/notification-hubs/breaking-news-dotnet/
  [Envío de notificaciones a los usuarios]: /es-es/manage/services/notification-hubs/notify-users/
  [Envío de notificaciones entre plataformas a los usuarios]: /es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library/
