<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure .Net Runtime Mobile Services and Notification Hubs to send push notifications to your Windows Store JavaScript app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Introducción a las notificaciones de inserción en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tema muestra cómo puede usar Servicios móviles de Azure con un back-end de .NET para enviar notificaciones de inserción a una aplicación de la Tienda Windows. Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción desde el back-end de .NET con los Centros de notificación cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

> [WACOM.NOTE] En este tema se muestra cómo configurar manualmente las notificaciones de inserción mediante el Servicio de notificaciones de inserción de Windows (WNS) para una aplicación de la Tienda Windows. Puede usar las herramientas de Visual Studio 2013 para configurar automáticamente las mismas notificaciones de inserción en un proyecto de aplicación de Windows. Para obtener más información, consulte la [versión de la aplicación universal de Windows][versión de la aplicación universal de Windows] de este tutorial.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registro de la aplicación con WNS y configuración de Servicios móviles][Registro de la aplicación con WNS y configuración de Servicios móviles]
2.  [Actualización de la aplicación para registrarse a fin de recibir notificaciones][Actualización de la aplicación para registrarse a fin de recibir notificaciones]
3.  [Actualización del servidor para enviar notificaciones de inserción][Actualización del servidor para enviar notificaciones de inserción]
4.  [Habilitación de notificaciones de inserción para pruebas locales][Habilitación de notificaciones de inserción para pruebas locales]
5.  [Inserción de datos para recibir notificaciones de inserción][Inserción de datos para recibir notificaciones de inserción]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos] para conectar su proyecto al servicio móvil. Si no se conecta un servicio móvil, el Asistente para agregar notificaciones de inserción se encarga de crear esta conexión.

## <span id="register"></span></a> Registro de la aplicación con WNS y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

El servicio móvil y la aplicación están ahora configurados para funcionar con WNS y los Centros de notificaciones. A continuación, actualizará la aplicación de Tienda Windows para el registro de notificaciones.

## <span id="update-app"></span></a> Actualización de la aplicación para registrarse a fin de recibir notificaciones

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1.  Abra el archivo default.js e inserte el siguiente código después del código que crea la instancia **MobileServiceClient**. Este código crea un canal de notificaciones de inserción y registra dichas notificaciones:

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);
            }, function (error) {
                var message = "Registration failed: " + error.message;
                var dialog = new Windows.UI.Popups.MessageDialog(message);
                dialog.showAsync();
            });

    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra para notificaciones de inserción. Si el registro no se realiza correctamente, se mostrará un mensaje de error en un cuadro de diálogo de mensajes.

2.  En Visual Studio, abra el archivo Package.appxmanifest y asegúrese de que **Capacidad de aviso** esté establecida en **Sí** en la pestaña **IU de la aplicación**.

    ![][0]

    Esto asegura que la aplicación puede generar notificaciones del sistema. Estas notificaciones se han habilitado ya en el proyecto de inicio de rápido descargado.

## <span id="update-server"></span></a> Actualización del servidor para enviar notificaciones de inserción

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a>Habilitación de notificaciones de inserción para pruebas locales

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a>Pruebas de notificaciones de inserción en su aplicación

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial se demostraron los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para usar Servicios móviles y Centros de notificaciones con el fin de enviar notificaciones de inserción. A continuación, considere completar uno de los siguientes tutoriales:

-   [Enviar notificaciones de inserción a los usuarios autenticados][Enviar notificaciones de inserción a los usuarios autenticados]
    Aprenda a usar las etiquetas para enviar notificaciones de inserción desde un servicio móvil solo a un usuario autenticado.

Considere la posibilidad de profundizar más en los siguientes temas sobre Servicios móviles y Centros de notificaciones:

-   [Introducción a los datos][Introducción a los datos]
    
	Obtenga más información sobre cómo almacenar y consultar datos con los servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]
    
	Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

-   [¿Qué son los Centros de notificaciones?][¿Qué son los Centros de notificaciones?]
    
	Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

-   [Referencia conceptual de Servicios móviles con HTML/JavaScript][Referencia conceptual de Servicios móviles con HTML/JavaScript]
    
	Obtenga más información acerca de cómo utilizar los Servicios móviles con aplicaciones JavaScript.





  [versión de la aplicación universal de Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push
  [Registro de la aplicación con WNS y configuración de Servicios móviles]: #register
  [Actualización de la aplicación para registrarse a fin de recibir notificaciones]: #update-app
  [Actualización del servidor para enviar notificaciones de inserción]: #update-server
  [Habilitación de notificaciones de inserción para pruebas locales]: #local-testing
  [Inserción de datos para recibir notificaciones de inserción]: #test
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
  [Enviar notificaciones de inserción a los usuarios autenticados]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [Referencia conceptual de Servicios móviles con HTML/JavaScript]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library
