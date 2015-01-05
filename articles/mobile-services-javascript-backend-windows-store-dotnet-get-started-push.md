<properties pageTitle="Introducción a las notificaciones de inserción (Tienda Windows) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />


# Incorporación de notificaciones de inserción a su aplicación de Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación universal de Windows. 
Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción con Centros de notificaciones cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

>[WACOM.NOTE]En este tema se muestra cómo configurar manualmente las notificaciones de inserción mediante Servicios móviles en un proyecto de la Tienda Windows. Puede usar las herramientas de Visual Studio 2013 para agregar las mismas notificaciones de inserción a un proyecto de aplicación de la Tienda Windows. Para obtener más información, consulte la [versión de la aplicación universal de Windows](/en-us/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-push/) de este tutorial. 

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Registro de la aplicación con WNS y configuración de Servicios móviles](#register)
2. [Actualización de la aplicación para registrarse a fin de recibir notificaciones](#update-app)
3. [Actualización de scripts del servidor para enviar notificaciones de inserción](#update-scripts)
3. [Inserción de datos para recibir notificaciones de inserción](#test)

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles] o [Introducción a los datos] para conectar su proyecto al servicio móvil. Si no se conecta un servicio móvil, el Asistente para agregar notificaciones de inserción se encarga de crear esta conexión. 

>[WACOM.NOTE]En este tema se describen las aplicaciones para Windows Phone 8.1 de la Tienda Windows. Para agregar notificaciones de inserción a una aplicación de Windows Phone 8 o Windows Phone Silverlight 8.1, consulte esta versión de [Introducción a las notificaciones de inserción en Servicios móviles](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push).

##<a id="register"></a> Registro de la aplicación con WNS y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

El servicio móvil y la aplicación están ahora configurados para funcionar con WNS y los Centros de notificaciones. A continuación, actualizará la aplicación universal de la Tienda Windows para el registro de notificaciones.

##<a id="update-app"></a> Actualización de la aplicación para registrarse a fin de recibir notificaciones

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1. En Visual Studio, abra el archivo App.xaml.cs y agregue las siguientes instrucciones "using":

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. Agregue el método siguiente a la clase **App**: 
	
        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel =
                await PushNotificationChannelManager
                    .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
        }

    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra para notificaciones de inserción.
    
4. En la parte superior del controlador de eventos **OnLaunched** en App.xaml.cs, agregue la siguiente llamada al nuevo método **InitNotificationsAsync**:

        InitNotificationsAsync();

	Esto garantiza que se solicitará registro cada vez que se cargue la página. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado. 

5. Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente con la clave de registro.
  
6. (Opcional) Si no está usando el proyecto de inicio rápido generado por el Portal de administración, abra el archivo Package.appxmanifest y asegúrese de que en la pestaña **IU de la aplicación**, **Capacidad de aviso** esté establecido en **Sí**.

   	![][2]

   	Esto asegura que la aplicación puede generar notificaciones del sistema. Estas notificaciones se han habilitado ya en el proyecto de inicio de rápido descargado.

##<a id="update-scripts"></a> Actualización de scripts del servidor para enviar notificaciones de inserción

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Probar las notificaciones de inserción en su aplicación

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial hemos presentado las nociones para habilitar una aplicación de la Tienda Windows para que envíe notificaciones de inserción con Servicios móviles y Centros de notificaciones. A continuación, considere completar uno de los siguientes tutoriales:

+ [Envío de notificaciones de inserción a usuarios autenticados]
	<br/>Aprenda a usar las etiquetas para enviar notificaciones de inserción desde un servicio móvil solo a un usuario autenticado.

+ [Envío de notificaciones a los suscriptores]
	<br/>Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

+ [Envío de notificaciones basadas en plantillas a los suscriptores]
	<br/>Obtenga información sobre cómo usar las plantillas para enviar notificaciones de inserción desde un servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los Servicios móviles.

* [¿Qué son los Centros de notificaciones?]
  <br/>Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

* [Depuración de aplicaciones de los Centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenga orientación para solucionar y depurar las soluciones de Centros de notificaciones. 

* [Referencia conceptual de Servicios móviles con .NET]
  <br/>Obtenga más información sobre el uso de Servicios móviles con .NET.

* [Referencia del script del servidor de Servicios móviles]
  <br/>Obtenga más información sobre cómo implementar lógica empresarial al servicio móvil.

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los Servicios móviles]: /en-us/documentation/articles/mobile-services-windows-store-get-started
[Introducción a los datos]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[Introducción a la autenticación]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users

[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referencia conceptual de Servicios móviles con .NET]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[Envío de notificaciones de inserción a usuarios autenticados]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/

[¿Qué son los Centros de notificaciones?]: /en-us/documentation/articles/notification-hubs-overview/
[Envío de notificaciones a los suscriptores]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Envío de notificaciones basadas en plantillas a los suscriptores]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!--HONumber=35.1-->
