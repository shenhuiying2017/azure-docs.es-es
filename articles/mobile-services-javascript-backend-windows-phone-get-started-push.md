<properties pageTitle="Introducción a las notificaciones de inserción (Tienda Windows) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo usar Servicios móviles de Azure y centros de notificaciones para enviar notificaciones de inserción a la aplicación de la Tienda Windows." services="mobile-services, notification-hubs" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"/>


# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación Silverlight de Windows Phone. Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción con Centros de notificaciones cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Actualización de la aplicación para registrarse a fin de recibir notificaciones](#update-app)
2. [Actualización de scripts del servidor para enviar notificaciones de inserción](#update-scripts)
3. [Inserción de datos para recibir notificaciones de inserción](#test)

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles] o [Introducción a los datos] para conectar su proyecto al servicio móvil. Si no se conecta un servicio móvil, el Asistente para agregar notificaciones de inserción se encarga de crear esta conexión. 

>[AZURE.NOTE]Para enviar notificaciones de inserción a una aplicación de la Tienda de Windows Phone 8.1, siga la versión [Aplicación de la Tienda Windows](/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push) de este tutorial.

##<a id="update-app"></a> Actualización de la aplicación para registrarse a fin de recibir notificaciones

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1. En Visual Studio, abra el archivo App.xaml.cs y agregue la instrucción siguiente  `using`:

        using Microsoft.Phone.Notification;

3. Agregue lo siguiente a App.xaml.cs:
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                    // Register for notifications using the new channel
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    Este código recupera el valor de ChannelURI de la aplicación desde el Servicio de notificación de inserción de Microsoft (MPNS) que usa Windows Phone 8.x "Silverlight" y luego lo registra para notificaciones de inserción.

	>[AZURE.NOTE]En este tutorial, el servicio móvil envía una notificación del sistema al dispositivo. Cuando envía una notificación de icono, debe llamar al método **BindToShellTile** en el canal.

4. En la parte superior del controlador de eventos **Application_Launching** en App.xaml.cs, agregue la siguiente llamada al nuevo método **AcquirePushChannel**:

        AcquirePushChannel();

	Esto garantiza que se solicitará registro cada vez que se cargue la página. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado. 

5. Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente con la clave de registro.
  
6.	En el Explorador de soluciones, expanda **Propiedades**, abra el archivo WMAppManifest.xml, haga clic en la pestaña **Funcionalidades** y asegúrese de que la funcionalidad **ID___CAP___PUSH_NOTIFICATION** esté activada.

   	![][1]

   	Esto asegura que la aplicación puede generar notificaciones del sistema. 

##<a id="update-scripts"></a>Actualización de scripts del servidor para enviar notificaciones de inserción

Finalmente, debe actualizar el script registrado para insertar la operación en la tabla TodoItem a fin de enviar notificaciones.

1. Haga clic en **TodoItem**, en **Script** y seleccione **Insertar**. 

   	![][10]

2. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

		function insert(item, user, request) {
		// Define a payload for the Windows Phone toast notification.
		var payload = '<?xml version="1.0" encoding="utf-8"?>' +
		    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
		    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
		    '</wp:Text2></wp:Toast></wp:Notification>';
		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		    	push.mpns.send(null, payload, 'toast', 22, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse);
						request.respond();
		                },              
		                error: function (pushResponse) {
		                    console.log("Error Sending push:", pushResponse);
							request.respond(500, { error: pushResponse });
		                    }
		                });
		            }
		        });      
		}

	Este script de inserción envía una notificación de inserción (con el texto del elemento insertado) a todos los registros de aplicaciones de Windows Phone una vez que se realiza la inserción.

3. Haga clic en la pestaña **Inserción**, marque **Habilite las notificaciones de inserción no autenticadas** y, a continuación, haga clic en **Guardar**.

	>[AZURE.NOTE]Si completa este tutorial con un servicio móvil anterior, es posible que aparezca un vínculo en la parte inferior de la pestaña **Inserción** que indique **Habilitar inserción mejorada**. Haga clic en este enlace para actualizar el servicio móvil, de modo que se integre con los Centros de notificaciones. Este cambio no se podrá deshacer. Para obtener detalles sobre cómo habilitar las notificaciones de inserción mejoradas en un servicio móvil de producción, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">esta guía</a>.

	![][11]

	Con esto se permite que el servicio móvil se conecte a MPNS en modo sin autenticar para enviar notificaciones de inserción.

	>[AZURE.NOTE]Este tutorial usa MPNS en modo sin autenticar. En este modo, MPNS limita el número de notificaciones que se pueden enviar a un canal de dispositivo. Para quitar esta restricción, debe generar y cargar un certificado haciendo clic en **Upload** (Cargar) y seleccionando el certificado. Para obtener más información sobre la generación del certificado, consulte [Configuración de un servicio web autenticado para enviar notificaciones de inserción para Windows Phone].

##<a id="test"></a>Prueba de las notificaciones de inserción en su aplicación

1. En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

    >[AZURE.NOTE] Es posible que se produzca una excepción 401 Unauthorized RegistrationAuthorizationException al realizar pruebas en el emulador de Windows Phone. Esta excepción puede producirse durante la llamada  `RegisterNativeAsync()` debido a la manera en el que el emulador de Windows Phone sincroniza su reloj con el equipo host. Puede resultar en un token de seguridad rechazado. Para solucionarlo, establezca manualmente el reloj del emulador antes de realizar las pruebas.

5. En la aplicación, escriba el texto "hello push" en el cuadro de texto, haga clic en **Guardar** y luego haga clic inmediatamente en el botón Inicio o Atrás para abandonar la aplicación.

   	![][4]

  	Esto envía una solicitud de inserción al servicio móvil para almacenar el elemento agregado. Observe que el dispositivo recibe una notificación del sistema que dice **hello push**.

	![][5]

	>[AZURE.NOTE]No recibirá la notificación mientras permanezca en la aplicación. Para recibir una notificación del sistema mientras la aplicación esté activa, deberá gestionar el evento [ShellToastNotificationReceived](http://msdn.microsoft.com/es-es/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx).



## <a name="next-steps"> </a>Pasos siguientes

En este tutorial hemos presentado las nociones para habilitar una aplicación de la Tienda Windows para que envíe notificaciones de inserción con Servicios móviles y Centros de notificaciones. A continuación, considere completar uno de los siguientes tutoriales:

+ [Envío de notificaciones de inserción a usuarios autenticados]
	<br/>Aprenda a usar las etiquetas para enviar notificaciones de inserción desde un servicio móvil a solo un usuario autenticado.

+ [Envío de notificaciones de difusión a los suscriptores]
	<br/>Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

<!---+ [Envío de notificaciones basadas en plantillas a los suscriptores]
	<br/>Obtenga información sobre cómo usar las plantillas para enviar notificaciones de inserción desde un servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.
-->
Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los Servicios móviles.

* [¿Qué son los Centros de notificaciones?]
  <br/>Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

* [Referencia conceptual de servicios móviles con .NET]
  <br/>Obtenga más información sobre el uso de Servicios móviles con .NET.

* [Referencia del script de servidor de Servicios móviles]
  <br/>Obtenga más información sobre cómo implementar lógica empresarial al servicio móvil.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-users

[Configuración de un servicio web autenticado para enviar notificaciones de inserción para Windows Phone]: http://msdn.microsoft.com/es-es/library/windowsphone/develop/ff941099(v=vs.105).aspx

[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referencia conceptual de servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[Envío de notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/

[¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/
[Envío de notificaciones de difusión a los suscriptores]: /es-es/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
[Envío de notificaciones basadas en plantillas a los suscriptores]: /es-es/documentation/articles/notification-hubs-windows-phone-send-localized-breaking-news/


<!--HONumber=42-->
