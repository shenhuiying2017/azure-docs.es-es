<properties 
	pageTitle="Introducción a las notificaciones push (JavaScript para Android) | Microsoft Azure" 
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para enviar notificaciones de inserción a la aplicación JavaScript de Android." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="ricksal"/>

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push-EC.md)]

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación Android usando la mensajería en la nube de Google (GCM). Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones push:

1. [Habilitación del servicio de mensajería en la nube de Google](#register)
2. [Configuración de Servicios móviles](#configure)
3. [Incorporación de notificaciones de inserción a la aplicación](#add-push)
4. [Actualización de scripts para enviar notificaciones de inserción](#update-scripts)
5. [Inserción de datos para recibir notificaciones](#test)


>[AZURE.NOTE]Si desea ver el código fuente de la aplicación final, vaya <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithPush/Android" target="_blank">aquí</a>.

##Requisitos previos

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites-EC.md)]

##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [Habilitación de GCM](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>Configuración de Servicios móviles para enviar solicitudes de inserción

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="add-push"></a>Incorporación de notificaciones de inserción a la aplicación

###Comprobación de la versión del SDK de Android

[AZURE.INCLUDE [Comprobar el SDK](../../includes/mobile-services-verify-android-sdk-version-EC.md)]

El siguiente paso es instalar los servicios de Google Play. El servicio de mensajería en la nube de Google tiene algunos requisitos mínimos en el nivel de API para desarrollo y prueba, que debe cumplir la propiedad **minSdkVersion** del manifiesto.

Si va a realizar pruebas con un dispositivo antiguo, consulte [Configuración del SDK de Google Play Services] para determinar el valor mínimo que puede configurar, y cómo configurarlo de forma adecuada.

###Incorporación de Google Play Services al proyecto

[AZURE.INCLUDE [Incorporación de Play Services](../../includes/mobile-services-add-Google-play-services-EC.md)]

###Incorporación de código

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push-EC.md)]


##<a id="update-scripts"></a>Actualización del script de inserción registrado en el Portal de administración

1. En el Portal de administración, haga clic en la pestaña **Datos** y luego en la tabla **TodoItem**. 

   	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-portal-data-tables.png)

2. En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insertar**.
   
  	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-insert-script-push2.png)

   	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

		function insert(item, user, request) {
		// Define a payload for the Google Cloud Messaging toast notification.
		var payload = {
		    "data": {
		        "message": item.text 
		    }
		};		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	Esto registra un nuevo script de inserción, que usa el [objeto gcm] para enviar una notificación push a todos los dispositivos registrados después de que se realiza la inserción.

##<a id="test"></a>Prueba de las notificaciones push en su aplicación

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

###Configuración de un emulador para realizar pruebas

Cuando ejecute esta aplicación en el emulador, asegúrese de utilizar un dispositivo virtual Android (AVD) compatible con las API de Google.

1. Reinicie Eclipse y, a continuación, en el Explorador de paquetes, haga clic con el botón secundario en el proyecto, haga clic en **Properties** (Propiedades), haga clic en **Android**, active **Google APIs** (API de Google) y, a continuación, haga clic en **OK** (Aceptar).

	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-services-import-android-properties.png)

  	Con esto, el proyecto se dirige a las API de Google.

2. En **Ventana**, seleccione **Administrador de dispositivos virtuales de Android**, seleccione su dispositivo y haga clic en **Editar**.

	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-services-android-virtual-device-manager.png)

3. Seleccione **Google APIs** (API de Google) en **Target** (Destino) y, a continuación, haga clic en OK (Aceptar).

   	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-services-android-virtual-device-manager-edit.png)

	Con esto se dirige el dispositivo virtual Android para que use las API de Google.

###Ejecución de la prueba

1. En el menú **Run** en Eclipse, haga clic en **Run** para iniciar la aplicación.

2. En la aplicación, escriba texto significativo como _A new Mobile Services task_ y, a continuación, haga clic en el botón **Add** (Agregar).

  	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-quickstart-push1-android.png)

3. Deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el Centro de notificaciones del dispositivo y visualizar la notificación.


Ha completado correctamente este tutorial.


## <a name="next-steps"> </a>Pasos siguientes

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Introducción a los datos] <br/>Obtenga más información sobre cómo almacenar y consultar datos con los servicios móviles.

* [Adición de autenticación a la aplicación][Get started with authentication] <br/>Obtenga información sobre cómo autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

* [¿Qué son los Centros de notificaciones?] <br/>Obtenga más información sobre el funcionamiento de los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas de cliente.

* [Depuración de aplicaciones de centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenga instrucciones de resolución de problemas y depuración de soluciones de centros de notificaciones.

* [Cómo usar la biblioteca de cliente Android para servicios móviles] <br/>Obtenga más información acerca de cómo usar servicios móviles con Android.

* [Referencia de script de servidor de servicios móviles] <br/>Obtenga más información acerca de cómo implementar la lógica empresarial en el servicio móvil.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a Servicios móviles]: mobile-services-android-get-started.md
[Introducción a los datos]: mobile-services-android-get-started-data.md
[Get started with authentication]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Configuración del SDK de Google Play Services]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure Management Portal]: https://manage.windowsazure.com/
[Cómo usar la biblioteca de cliente Android para servicios móviles]: mobile-services-android-how-to-use-client-library.md

[objeto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Referencia de script de servidor de servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-android-push-notifications-app-users.md

[¿Qué son los Centros de notificaciones?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md
 

<!---HONumber=August15_HO7-->