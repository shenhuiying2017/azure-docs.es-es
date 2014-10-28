<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc,ricksal" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc,ricksal"></tags>

# Introducción a las notificaciones de inserción en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][]]

En este tema se muestra cómo puede usar Servicios móviles de Azure con un back-end de .NET para enviar notificaciones de inserción a una aplicación universal de Windows. Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido universal de Windows. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción desde el back-end de .NET con los Centros de notificación cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

> [WACOM.NOTE] En este tema se muestra cómo configurar manualmente las notificaciones de inserción mediante el Servicio de notificaciones de inserción de Windows (WNS) para una aplicación de la Tienda Windows. Puede usar las herramientas de Visual Studio 2013 para configurar automáticamente las mismas notificaciones de inserción en un proyecto de aplicación de Windows. Para obtener más información, consulte la [versión de la aplicación universal de Windows][] de este tutorial.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registro de la aplicación con WNS y configuración de Servicios móviles][]
2.  [Actualización de la aplicación para registrarse a fin de recibir notificaciones][]
3.  [Actualización del servidor para enviar notificaciones de inserción][]
4.  [Habilitación de notificaciones de inserción para pruebas locales][]
5.  [Inserción de datos para recibir notificaciones de inserción][]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles][] o [Introducción a los datos][] para conectar su proyecto al servicio móvil.

> [WACOM.NOTE]En este tema se describen las aplicaciones para Windows Phone 8.1 de la Tienda Windows. Para agregar notificaciones de inserción a una aplicación de Windows Phone 8 o Windows Phone 8.1 Silverlight, consulte esta versión de [Introducción a las notificaciones de inserción en Servicios móviles][].

## <span id="register"></span></a> Registro de la aplicación con WNS y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app][]]

El servicio móvil y las aplicaciones están ahora configurados para funcionar con WNS y con los Centros de notificaciones. A continuación, actualizará la aplicación universal de Windows para el registro de notificaciones.

## <span id="update-app"></span></a> Actualización de la aplicación para registrarse a fin de recibir notificaciones

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1.  En Visual Studio, abra el archivo App.xaml.cs y agregue las siguientes instrucciones `using`:

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  Agregue el siguiente método `InitNotificationAsync` a la clase **App** para crear un canal de notificaciones de inserción y registrarse para obtener notificaciones de inserción.

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            System.Exception exception = null;
            try
            {
                await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
            }
            catch (System.Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra para notificaciones de inserción.

3.  En la parte superior del controlador de eventos **OnLaunched** en App.xaml.cs, agregue la siguiente llamada al nuevo método **InitNotificationsAsync**:

        InitNotificationsAsync();

    Esto garantiza que se solicitará registro cada vez que se cargue la página. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado.

4.  En Visual Studio, abra el archivo Package.appxmanifest y asegúrese de que la opción **Capacidad de aviso** esté definida en **Sí** en la pestaña **IU de la aplicación**. Guarde el archivo.

    ![][]

    Esto asegura que la aplicación puede generar notificaciones del sistema.

## <span id="update-server"></span></a> Actualización del servidor para enviar notificaciones de inserción

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push][]]

## <span id="local-testing"></span></a>Habilitación de notificaciones de inserción para pruebas locales

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][]]

## <span id="test"></span></a>Pruebas de notificaciones de inserción en su aplicación

[WACOM.INCLUDE [mobile-services-windows-store-test-push][]]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial se demostraron los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para usar Servicios móviles y Centros de notificaciones con el fin de enviar notificaciones de inserción. A continuación, si lo desea, complete el siguiente tutorial, [Enviar notificaciones de inserción a los usuarios autenticados][], en el que se muestra cómo usar las etiquetas para enviar notificaciones de inserción desde un servicio móvil solo a un usuario autenticado.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

-   [Introducción a los datos][]
    Obtenga más información sobre cómo almacenar y consultar datos con los servicios móviles.

-   [Introducción a la autenticación][]
    Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

-   [¿Qué son los Centros de notificaciones?][]
    Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

-   [Referencia conceptual de Servicios móviles con .NET][]
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [versión de la aplicación universal de Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Registro de la aplicación con WNS y configuración de Servicios móviles]: #register
  [Actualización de la aplicación para registrarse a fin de recibir notificaciones]: #update-app
  [Actualización del servidor para enviar notificaciones de inserción]: #update-server
  [Habilitación de notificaciones de inserción para pruebas locales]: #local-testing
  [Inserción de datos para recibir notificaciones de inserción]: #test
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [Introducción a las notificaciones de inserción en Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Enviar notificaciones de inserción a los usuarios autenticados]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library
