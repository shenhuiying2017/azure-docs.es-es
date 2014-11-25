<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Introducción a las notificaciones de inserción en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de la Tienda Windows.
En este tutorial, habilitará las notificaciones de inserción al proyecto de inicio rápido con Centros de notificaciones de Azure. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción con Centros de notificaciones cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

> [WACOM.NOTE] En este tema se muestra cómo configurar manualmente las notificaciones de inserción mediante el Servicio de notificaciones de inserción de Windows (WNS) para una aplicación de la Tienda Windows. Puede usar las herramientas de Visual Studio 2013 para configurar automáticamente las mismas notificaciones de inserción en un proyecto de aplicación de Windows. Para obtener más información, consulte la [versión de la aplicación universal de Windows][versión de la aplicación universal de Windows] de este tutorial.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registro de la aplicación con WNS y configuración de Servicios móviles][Registro de la aplicación con WNS y configuración de Servicios móviles]
2.  [Actualización de la aplicación para registrarse a fin de recibir notificaciones][Actualización de la aplicación para registrarse a fin de recibir notificaciones]
3.  [Actualización de scripts del servidor para enviar notificaciones de inserción][Actualización de scripts del servidor para enviar notificaciones de inserción]
4.  [Inserción de datos para recibir notificaciones de inserción][Inserción de datos para recibir notificaciones de inserción]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos] para conectar su proyecto al servicio móvil. Si no se conecta un servicio móvil, el Asistente para agregar notificaciones de inserción se encarga de crear esta conexión.

## <span id="register"></span></a> Registro de la aplicación con WNS y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

El servicio móvil y la aplicación están ahora configurados para funcionar con WNS y los Centros de notificaciones. A continuación, actualizará la aplicación de Tienda Windows para el registro de notificaciones.

## <span id="update-app"></span></a> Actualización de la aplicación para registrarse a fin de recibir notificaciones

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1.  Abra el archivo default.js e inserte el siguiente código después del código que crea la instancia **MobileServiceClient**:

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);                    
            });      

    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra para notificaciones de inserción.

2.  Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente con la clave de registro.

3.  Abra el archivo Package.appxmanifest y asegúrese de que en la pestaña **IU de la aplicación**, la opción **Capacidad de aviso** esté definida en **Sí**.

    ![][0]

    Esto asegura que la aplicación puede generar notificaciones del sistema.

## <span id="update-scripts"></span></a> Actualización de scripts del servidor para enviar notificaciones de inserción

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a>Pruebas de notificaciones de inserción en su aplicación

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial se demostraron los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para usar Servicios móviles y Centros de notificaciones con el fin de enviar notificaciones de inserción. A continuación, si lo desea, complete el siguiente tutorial, [Enviar notificaciones de inserción a los usuarios autenticados][Enviar notificaciones de inserción a los usuarios autenticados], en el que se muestra cómo usar las etiquetas para enviar notificaciones de inserción desde un servicio móvil solo a un usuario autenticado.

<!---+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

-   [Introducción a los datos][Introducción a los datos]
    
	Obtenga más información sobre cómo almacenar y consultar datos con los servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]
    
	Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

-   [¿Qué son los Centros de notificaciones?][¿Qué son los Centros de notificaciones?]
    
	Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

-   [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET]
    
	Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    
	Obtenga más información sobre cómo implementar lógica empresarial al servicio móvil.

 
 


  [versión de la aplicación universal de Windows]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push
  [Registro de la aplicación con WNS y configuración de Servicios móviles]: #register
  [Actualización de la aplicación para registrarse a fin de recibir notificaciones]: #update-app
  [Actualización de scripts del servidor para enviar notificaciones de inserción]: #update-scripts
  [Inserción de datos para recibir notificaciones de inserción]: #test
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-get-started
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data
  [0]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [Enviar notificaciones de inserción a los usuarios autenticados]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-users
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
