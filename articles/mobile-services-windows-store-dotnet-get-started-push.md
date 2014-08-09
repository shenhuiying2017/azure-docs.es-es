<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introducción a las notificaciones de inserción en Servicios móviles
===================================================================

[C\# para Tienda Windows](/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-push "JavaScript para Tienda Windows")[Windows Phone](/es-es/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone")[iOS](/es-es/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/es-es/documentation/articles/mobile-services-android-get-started-push "Android")[Xamarin.iOS](/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push "Xamarin.iOS")[Xamarin.Android](/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-push "Xamarin.Android")
[Back-end de .NET](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "Back-end de .NET") | [Back-end de JavaScript](/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/ "Back-end de JavaScript")

Este tema muestra cómo Visual Studio 2013 le permite utilizar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación de la Tienda Windows. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con los servicios de notificaciones de inserción de Windows (WNS) directamente desde Visual Studio. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

> [WACOM.NOTE] Servicios móviles ahora se integra con Centros de notificaciones de Azure para ofrecer compatibilidad con la funcionalidad de notificación de inserción adicional, como plantillas, varias plataformas y escala. Esta funcionalidad integrada actualmente está en su versión preliminar. Para obtener más información, consulte esta versión de [Introducción a las notificaciones de inserción](/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles](#register)
2.  [Actualización del código de notificación de inserción generado](#update-scripts)
3.  [Inserción de datos para recibir notificaciones](#test)

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started/) o [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-dotnet/) para conectar su proyecto al servicio móvil. Si no se conecta un servicio móvil, el Asistente para agregar notificaciones de inserción se encarga de crear esta conexión.

Registro de la aplicaciónIncorporación y configuración de notificaciones de inserción en la aplicación
------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Expanda **servicios**, **servicios móviles**, su nombre de servicio, abra el archivo de código generado y, a continuación, examine el método **UploadChannel** que obtiene el ID de instalación y el canal para el dispositivo e inserta estos datos en la nueva tabla de canales.

    El asistente también agrega una llamada a este método al controlador de eventos **OnLaunched** en el archivo de código App.xaml.cs. De esta forma se asegura que se intenta el registro del dispositivo siempre que se inicia la aplicación.

2.  En el Explorador de servidores, expanda **Azure**, **Servicios móviles**, su nombre de servicio y **channels** y, a continuación, abra el archivo insert.js.

    Este archivo, que se almacena en el servicio móvil, contiene código JavaScript que se ejecuta cuando un cliente envía una solicitud para registrar un dispositivo insertando datos en la tabla de canales.

    **Nota:**

    La versión inicial de este archivo contiene código que busca un registro existente para el dispositivo. También contiene código que envía una notificación de inserción cuando se agrega un nuevo registro a la tabla de canales. El código que envía una notificación de inserción se puede incluir en cualquier archivo de script registrado. La ubicación de este script depende de cómo se desencadena la notificación. Los scripts se pueden registrar en cualquier operación de inserción, actualización, eliminación o lectura en una tabla, como un trabajo programado o como una API personalizada. Para obtener más información, consulte [Uso de scripts del servidor en Servicios móviles](http://go.microsoft.com/fwlink/p/?LinkID=287178).

3.  Presione la tecla F5 para ejecutar la aplicación y comprobar que se recibe inmediatamente una notificación desde el servicio móvil.

    Esta notificación se ha generado mediante la inserción de una fila en la nueva tabla de canales, que es el registro del dispositivo.

Aunque el código generado facilita la demostración de una notificación cuando se ejecuta la aplicación, no suele ser un escenario descriptivo. A continuación, quitará el código de notificación de la tabla de canales y lo reemplazará, con algunos cambios, en la tabla TodoItem.

Actualización del códigoActualización del código de notificación de inserción generado
--------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

Prueba de la aplicaciónPruebas de notificaciones de inserción en su aplicación
------------------------------------------------------------------------------

1.  En Visual Studio, pulse la tecla F5 para ejecutar la aplicación.

2.  En la aplicación, escriba un texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

   	![][13]

   	Tenga en cuenta que una vez finalizada la inserción, la aplicación recibe una notificación de inserción de WNS.

   	![][14]

Pasos siguientes
----------------

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Introducción a los Centros de notificaciones](/es-es/manage/services/notification-hubs/getting-started-windows-dotnet/)

    Aprenda a aprovechar Centros de notificaciones en su aplicación de la Tienda Windows.

-   [Envío de notificaciones a los suscriptores](/es-es/manage/services/notification-hubs/breaking-news-dotnet/)

    Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

-   [Envío de notificaciones a los usuarios](/es-es/manage/services/notification-hubs/notify-users/)

    Sepa cómo enviar notificaciones de inserción desde un Servicio móvil a usuarios específicos en cualquier dispositivo.

-   [Envío de notificaciones entre plataformas a los usuarios](/es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/)

    Sepa cómo utilizar las plantillas para enviar notificaciones de inserción desde un Servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-dotnet/)

    Obtenga más información sobre almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación](/es-es/develop/mobile/tutorials/get-started-with-users-dotnet)

    Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)

    Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con .NET](/es-es/develop/mobile/how-to-guides/work-with-net-client-library/)

    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.


<!-- Images. -->

[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png



