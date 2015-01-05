<properties urlDisplayName="Get Started with Push (JS)" pageTitle="Introducción a las notificaciones de inserción (JavaScript para Android) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services " authors="ricksal"  solutions="" writer="ricksal" manager="dwrede" editor=""   />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal" />

# Incorporación de notificaciones de inserción a su aplicación de Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación Android usando la mensajería en la nube de Google (GCM). Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Habilitación del servicio de mensajería en la nube de Google](#register)
2. [Configuración de Servicios móviles](#configure)
3. [Incorporación de notificaciones de inserción a la aplicación](#add-push)
4. [Actualización de scripts para enviar notificaciones de inserción](#update-scripts)
5. [Inserción de datos para recibir notificaciones](#test)


>[AZURE.NOTE] Si desea ver el código fuente de la aplicación final, vaya <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithPush/Android" target="_blank">aquí</a>.

##Requisitos previos

[WACOM.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

>[WACOM.NOTE]Para llevar a cabo este procedimiento, debe tener una cuenta de Google asociada a una dirección de correo electrónico comprobada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

[WACOM.INCLUDE [Habilitación de GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

A continuación, utilizará este valor de clave de API para permitir que los Servicios móviles lleven a cabo la autenticación con GCM y envíen notificaciones de inserción en nombre de su aplicación.

##<a id="configure"></a>Configuración de Servicios móviles para enviar solicitudes de inserción

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Haga clic en la pestaña **Insertar**, escriba el valor de la **clave API** obtenido de GCM en el procedimiento anterior y, a continuación, haga clic en **Guardar**.

	>[WACOM.NOTE]Si completa este tutorial con un servicio móvil anterior, es posible que aparezca un vínculo en la parte inferior de la pestaña **Insertar** que indique **Habilitar inserción mejorada**. Haga clic en este enlace para actualizar el servicio móvil, de modo que se integre con los Centros de notificaciones. Este cambio no se podrá deshacer. Para obtener detalles sobre cómo habilitar las notificaciones de inserción mejoradas en un servicio móvil de producción, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">esta guía</a>.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

    <div class="dev-callout"><b>Importante</b>
	<p>Al configurar las credenciales de GCM para las notificaciones de inserción mejoradas en la pestaña Push del portal, se comparten con los Centros de notificaciones para configurar el centro de notificaciones con la aplicación.</p>
    </div>


El servicio móvil y la aplicación están ahora configurados para funcionar con GCM y los Centros de notificaciones.

##<a id="add-push"></a>Incorporación de notificaciones de inserción a la aplicación

###Comprobación de la versión del SDK de Android

[WACOM.INCLUDE [Comprobación del SDK](../includes/mobile-services-verify-android-sdk-version.md)]

El siguiente paso es instalar los servicios de Google Play. El servicio de mensajería en la nube de Google tiene algunos requisitos mínimos en el nivel de API para desarrollo y prueba, los que debe cumplir la propiedad **minSdkVersion** del manifiesto. 

Si va a realizar pruebas con un dispositivo antiguo, consulte [Configuración de SDK de Google Play Services] para determinar el valor mínimo que puede configurar y cómo configurarlo de forma adecuada.

###Incorporación de Google Play Services al proyecto

[WACOM.INCLUDE [Incorporación de Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Incorporación de código

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>Actualización del script de inserción registrado en el Portal de administración

1. En el Portal de administración, haga clic en la pestaña **Datos** y, a continuación, en la tabla **TodoItem**. 

   	![](./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2. En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insertar**.
   
  	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png)

   	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

		function insert(item, user, request) {
		// Define a payload for the Google Cloud Messaging toast notification.
		var payload = {
		    data: {
		        message: item.text 
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

   	Esto registra un nuevo script de inserción, que usa el [objeto gcm] para enviar una notificación de inserción a todos los dispositivos registrados una vez que la inserción se haya realizado correctamente. 

##<a id="test"></a>Probar las notificaciones de inserción en su aplicación

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

###Configuración de un emulador para realizar pruebas

Cuando ejecute esta aplicación en el emulador, asegúrese de utilizar un dispositivo virtual Android (AVD) compatible con las API de Google.

1. Reinicie Eclipse y, a continuación, en el Explorador de paquetes, haga clic con el botón secundario en el proyecto, haga clic en **Propiedades**, **Android**, marque **API de Google** y, a continuación, haga clic en **Aceptar**.

	![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

  	Con esto, el proyecto se dirige a las API de Google.

2. En **Ventana**, seleccione **Administrador de dispositivos virtuales de Android**, seleccione su dispositivo y haga clic en **Editar**.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3. Seleccione **API de Google** en **Destino** y haga clic en Aceptar.

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Con esto, el dispositivo virtual Android se dirige para usar las API de Google.

###Ejecución de la prueba

1. En el menú **Ejecutar** en Eclipse, haga clic en **Ejecutar** para iniciar la aplicación.

2. En la aplicación, escriba un texto significativo como _Una nueva tarea de Servicios móviles_ y, a continuación, haga clic en el botón **Agregar**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

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

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los Servicios móviles.

* [¿Qué son los Centros de notificaciones?]
  <br/>Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

* [Depuración de aplicaciones de los Centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenga orientación para solucionar y depurar las soluciones de Centros de notificaciones. 

* [Uso de la biblioteca de cliente Android para Servicios móviles]
  <br/>Obtenga más información sobre el uso de Servicios móviles con Android.

* [Mobile Services server script reference]
  <br/>Obtenga más información sobre cómo implementar lógica empresarial al servicio móvil.


<!-- Anchors. -->
[Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles]: #register
[Actualización del código de notificación de inserción generado]: #update-scripts
[Inserción de datos para recibir notificaciones]: #test
[Pasos siguientes]:#next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los Servicios móviles]: /en-us/documentation/articles/mobile-services-android-get-started/
[Introducción a los datos]: /en-us/documentation/articles/mobile-services-android-get-started-data/
[Introducción a la autenticación]: /en-us/documentation/articles/mobile-services-android-get-started-users
[Introducción a las notificaciones de inserción]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[Notificaciones de inserción para usuarios de la aplicación]: /en-us/develop/mobile/tutorials/push-notifications-to-users-js
[Autorización de usuarios con scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript y HTML]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[Configuración del SDK de Google Play Services]: http://go.microsoft.com/fwlink/?LinkId=389801
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Uso de la biblioteca de cliente Android para Servicios móviles]: /en-us/documentation/articles/mobile-services-android-how-to-use-client-library

[objeto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293

[Envío de notificaciones de inserción a usuarios autenticados]: /en-us/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/

[¿Qué son los Centros de notificaciones?]: /en-us/documentation/articles/notification-hubs-overview/
[Envío de notificaciones a los suscriptores]: /en-us/documentation/articles/notification-hubs-android-send-breaking-news/
[Envío de notificaciones basadas en plantillas a los suscriptores]: /en-us/documentation/articles/notification-hubs-android-send-localized-breaking-news/

<!--HONumber=35.1-->
