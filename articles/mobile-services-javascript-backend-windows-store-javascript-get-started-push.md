<properties pageTitle="Introducción a las notificaciones de inserción (Tienda Windows) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo usar Servicios móviles de Azure y centros de notificaciones para enviar notificaciones de inserción a la aplicación de la Tienda Windows." services="mobile-services, notification-hubs" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"/>


# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de la Tienda Windows. 
Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción con Centros de notificaciones cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

>[AZURE.NOTE]En este tema se muestra cómo configurar manualmente las notificaciones de inserción mediante el Servicio de notificaciones de inserción de Windows (WNS) para una aplicación de la Tienda Windows. Puede usar las herramientas de Visual Studio 2013 para configurar automáticamente las mismas notificaciones de inserción en un proyecto de aplicación de Windows. Para obtener más información, consulte la [versión de la aplicación universal de Windows](/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push) de este tutorial.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Registro de la aplicación con WNS y configuración de Servicios móviles](#register)
2. [Actualización de la aplicación para registrarse a fin de recibir notificaciones](#update-app)
3. [Actualización de scripts del servidor para enviar notificaciones de inserción](#update-scripts)
3. [Inserción de datos para recibir notificaciones de inserción](#test)

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles] o [Introducción a los datos] para conectar su proyecto al servicio móvil. Si no se conecta un servicio móvil, el Asistente para agregar notificaciones de inserción se encarga de crear esta conexión. 

##<a id="register"></a>Registro de la aplicación con WNS y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

El servicio móvil y la aplicación están ahora configurados para funcionar con WNS y los Centros de notificaciones. A continuación, actualizará la aplicación de Tienda Windows para el registro de notificaciones.

##<a id="update-app"></a>Actualización de la aplicación para registrarse a fin de recibir notificaciones

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1. Abra el archivo default.js e inserte el siguiente código después del código que crea la instancia **MobileServiceClient**:

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);                    
            });      

	Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra para notificaciones de inserción.

2. Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente con la clave de registro.

6. Abra el archivo Package.appxmanifest y asegúrese de que en la pestaña **IU de la aplicación**, la opción **Capacidad de aviso** esté definida en **Sí**.

   	![][2]

   	Esto asegura que la aplicación puede generar notificaciones del sistema. 

##<a id="update-scripts"></a>Actualización de scripts del servidor para enviar notificaciones de inserción

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a>Prueba de las notificaciones de inserción en su aplicación

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial hemos presentado las nociones para habilitar una aplicación de la Tienda Windows para que envíe notificaciones de inserción con Servicios móviles y Centros de notificaciones. Le recomendamos que después realice el siguiente tutorial, [Envío de notificaciones de inserción a usuarios autenticados], que muestra cómo enviar estas notificaciones con etiquetas desde un servicio móvil a solo un usuario autenticado.

<!---+ [Envío de notificaciones de inserción a usuarios autenticados]
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

* [Referencia del script de servidor de Servicios móviles]
  <br/>Obtenga más información sobre cómo implementar lógica empresarial al servicio móvil.

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-users

[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referencia conceptual de servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[Envío de notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/

[¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/
[Envío de notificaciones de difusión a los suscriptores]: /es-es/documentation/articles/notification-hubs-windows-store-javascript-send-breaking-news/
[Envío de notificaciones basadas en plantillas a los suscriptores]: /es-es/documentation/articles/notification-hubs-windows-store-javascript-send-localized-breaking-news/


<!--HONumber=42-->
