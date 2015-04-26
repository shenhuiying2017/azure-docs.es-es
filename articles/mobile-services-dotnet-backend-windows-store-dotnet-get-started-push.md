<properties 
	pageTitle="Introducción a los centros de notificaciones de inserción usando servicios móviles en tiempo de ejecución de .NET" 
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure y centros de notificaciones para enviar notificaciones de inserción a la aplicación de la Tienda Windows." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc,ricksal"/>


# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tema muestra cómo usar Servicios móviles de Azure con un back-end de .NET para enviar notificaciones de inserción a una aplicación universal para Windows. Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido universal de Windows. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción desde el back-end de .NET con los Centros de notificación cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

>[AZURE.NOTE] En este tema se muestra cómo configurar manualmente las notificaciones de inserción mediante el Servicio de notificaciones de Windows (WNS) para una aplicación de la Tienda Windows. Puede usar las herramientas de Visual Studio 2013 para configurar automáticamente las mismas notificaciones de inserción en un proyecto de aplicación de Windows. Para obtener más información, vea la [versión de la aplicación universal de Windows](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push) de este tutorial.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Registro de la aplicación con WNS y configuración de Servicios móviles](#register)
2. [Actualización de la aplicación para registrarse a fin de recibir notificaciones](#update-app)
3. [Actualización del servidor para enviar notificaciones de inserción](#update-server)
4. [Habilitación de notificaciones de inserción para pruebas locales](#local-testing)
3. [Inserción de datos para recibir notificaciones de inserción](#test)

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles] o [Introducción a los datos] para conectar su proyecto al servicio móvil. 

>[AZURE.NOTE] En este tema se describen las aplicaciones para Windows Phone 8.1 de la Tienda Windows. Para agregar notificaciones de inserción a una aplicación de Windows Phone Silverlight 8.1 o Windows Phone 8, consulte esta versión de [Introducción a las notificaciones de inserción en Servicios móviles](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push).

##<a id="register"></a> Registro de la aplicación con WNS y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

El servicio móvil y las aplicaciones están ahora configurados para funcionar con WNS y con los Centros de notificaciones. A continuación, actualizará la aplicación universal de Windows para el registro de notificaciones.

##<a id="update-app"></a> Actualización de la aplicación para registrarse a fin de recibir notificaciones

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1. En Visual Studio, abra el archivo App.xaml.cs y agregue las siguientes instrucciones  `using`:

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. Agregue el siguiente método `InitNotificationAsync` a la clase **App** para crear un canal de notificaciones de inserción y registrarse para obtener notificaciones de inserción: 
	
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
    
3. En la parte superior del controlador de eventos **OnLaunched** en App.xaml.cs, agregue la siguiente llamada al nuevo método **InitNotificationsAsync**:

        InitNotificationsAsync();

	Esto garantiza que se solicitará registro cada vez que se cargue la página. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado. 

4. En Visual Studio, abra el archivo Package.appxmanifest y asegúrese de que la opción **Capacidad de aviso** esté definida en **Sí** en la pestaña **IU de la aplicación**. Guarde el archivo.

   	![][1]

   	Esto asegura que la aplicación puede generar notificaciones del sistema. 

##<a id="update-server"></a> Actualización del servidor para enviar notificaciones de inserción

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Habilitación de notificaciones de inserción para pruebas locales

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> Prueba de las notificaciones de inserción en su aplicación

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial hemos presentado las nociones para habilitar una aplicación de la Tienda Windows para que envíe notificaciones de inserción con Servicios móviles y Centros de notificaciones. Le recomendamos que después complete el tutorial [Envío de notificaciones de inserción a usuarios autenticados], que muestra cómo enviar estas notificaciones con etiquetas desde un servicio móvil a un único usuario autenticado.

<!--+ [Envío de notificaciones de inserción a usuarios autenticados]
	<br/>Aprenda a usar las etiquetas para enviar notificaciones de inserción desde un servicio móvil a solo un usuario autenticado.

+ [Envío de notificaciones de difusión a los suscriptores]
	<br/>Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

+ [Envío de notificaciones basadas en plantillas a los suscriptores]
	<br/>Obtenga información sobre cómo usar las plantillas para enviar notificaciones de inserción desde un servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.
-->
Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los Servicios móviles.

* [¿Qué son los Centros de notificaciones?]
  <br/>Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

* [Depuración de aplicaciones de los Centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenga orientación para solucionar y depurar las soluciones de Centros de notificaciones. 

* [Referencia conceptual de servicios móviles con .NET]
  <br/>Obtenga más información sobre el uso de Servicios móviles con .NET.

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users

[Envío de notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/
[Envío de notificaciones de difusión a los suscriptores]: /es-es/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Envío de notificaciones basadas en plantillas a los suscriptores]: /es-es/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/


[Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library



<!--HONumber=42-->
