<properties 
	pageTitle="Introducción a las notificaciones de inserción (inserción heredada) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para enviar notificaciones de inserción a la aplicación de Windows Phone (inserción heredada)." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="glenga"/>


# Introducción a las notificaciones de inserción en Servicios móviles (inserción heredada)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" >Windows Store C#</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">JavaScript para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
    <a href="/es-es/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/es-es/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
	<a href="/es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET backend">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="JavaScript backend" class="current">Back-end de JavaScript</a>
</div>

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de Windows Phone 8. 
En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Microsoft (MPNS). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

>[AZURE.NOTE]En este tema se tratan móviles <em>existentes</em> que todavía <em>no se han actualizado</em> para usar la integración de Centros de notificaciones. Cuando crea un <em>nuevo</em> servicio móvil, esta funcionalidad integrada se habilita automáticamente. Para los nuevos servicios móviles, consulte [Introducción a las notificaciones de inserción](/es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).
>
>La solución Servicios móviles se integra con Centros de notificaciones de Azure para ofrecer compatibilidad con la funcionalidad de notificación de inserción adicional, como plantillas, varias plataformas y escala. <em>Debería actualizar los servicios móviles existentes para que usen Centros de notificaciones siempre que sea posible</em>. Una vez que haya actualizado, vea esta versión de [Introducción a las notificaciones de inserción](/es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Creación de la tabla Registros]
2. [Incorporación de notificaciones de inserción a la aplicación]
3. [Actualización de scripts para enviar notificaciones de inserción]
4. [Inserción de datos para recibir notificaciones]

Este tutorial requiere [Visual Studio 2012 Express para Windows Phone] o una versión posterior.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles]. 

   >[AZURE.NOTE]Cuando envíe más de 500 mensajes por usuario al día, debe utilizar Centros de notificaciones. Para obtener más información, consulte <a href="/es-es/manage/services/notification-hubs/getting-started-windows-dotnet/">Introducción a los Centros de notificaciones</a>.

## <a name="create-table"></a>Creación de una tabla

[AZURE.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a>Incorporación de notificaciones de inserción a la aplicación</h2>
		
1. En Visual Studio, abra el archivo de proyecto MainPage.xaml.cs y agregue el siguiente código que crea una nueva clase **Registrations**:

	    public class Registrations
	    {
	        public string Id { get; set; }
	
	        [JsonProperty(PropertyName = "handle")]
	        public string Handle { get; set; }
	    }
	
	La propiedad Handle se utiliza para almacenar el URI de canal.

2. Abra el archivo App.xaml.cs y agregue la siguiente instrucción using:

        using Microsoft.Phone.Notification;

3. Agregue lo siguiente a App.xaml.cs:
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

		private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
	       await registrationsTable.InsertAsync(registration);
        }

   	Este código adquiere y almacena un canal para una suscripción a notificaciones e inserción y lo enlaza al icono predeterminado de la aplicación.

	> [AZURE.NOTE] En este tutorial, el servicio móvil envía una notificación de icono dinámico al dispositivo. Cuando envía una notificación del sistema, debe llamar al método **BindToShellToast** en el canal. Para admitir notificaciones del sistema y notificaciones de icono, llame a ambos métodos, **BindToShellTile** y **BindToShellToast**.
    
4. En la parte superior del controlador de eventos **Application_Launching** en App.xaml.cs, agregue la siguiente llamada al nuevo método **AcquirePushChannel**:

        AcquirePushChannel();

   	Esto garantiza que la propiedad **CurrentChannel** se inicialice cada vez que se inicia la aplicación.


5.	En el Explorador de soluciones, expanda **Propiedades**, abra el archivo WMAppManifest.xml, haga clic en la pestaña **Funcionalidades** y asegúrese de que la funcionalidad **ID___CAP___PUSH_NOTIFICATION** esté activada.

   	![][1]

   	Esto asegura que la aplicación puede recibir notificaciones de inserción.

<h2><a name="update-scripts"></a>Actualización de los scripts de inserción registrados en el Portal de administración</h2>

[AZURE.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. Haga clic en **TodoItem**, en **Script** y seleccione **Insertar**. 

   	![][10]

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

	    function insert(item, user, request) {
    	    request.execute({
        	    success: function() {
            	    request.respond();
            	    sendNotifications();
        	    }
    	    });

	        function sendNotifications() {
        	    var registrationsTable = tables.getTable('Registrations');
        	    registrationsTable.read({
            	    success: function(registrations) {
                	    registrations.forEach(function(registration) {
                    	    push.mpns.sendFlipTile(registration.handle, {
                        	    title: item.text
                    	    }, {
                        	    success: function(pushResponse) {
                            	    console.log("Sent push:", pushResponse);
                        	    }
                    	    });
                	    });
            	    }
        	    });
    	    }
	    }

    Este script de inserción envía una notificación de inserción (con el texto del elemento insertado) a todos los canales almacenados en la tabla **Registros**.

<h2><a name="test"></a>Prueba de las notificaciones de inserción en su aplicación</h2>

1. En Visual Studio, seleccione **Implementar solución** en el menú **Compilar**.

2. En el emulador, deslícese rápidamente a la izquierda para que aparezca la lista de aplicaciones instaladas y busque la nueva aplicación **TodoList**.

3. Mantenga pulsado el icono de la aplicación y, a continuación, seleccione **anclar a inicio** en el menú contextual.

  	![][2]

  	Esta acción ancla un icono llamado **TodoList** al menú de inicio.

4. Toque el icono llamado **TodoList** para iniciar la aplicación. 

  	![][3]

5. En la aplicación, escriba el texto "hello push" en el cuadro de texto y, a continuación, haga clic en **Guardar**.

   	![][4]

  	Esto envía una solicitud de inserción al servicio móvil para almacenar el elemento agregado.

6. Pulse el botón **Inicio** para volver al menú de inicio. 

  	![][5]

  	Observe que la aplicación recibió la notificación de inserción y que el título del icono ahora es **hello push**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demuestra la funcionalidad de notificación de inserción básica que brinda Servicios móviles. Si su aplicación requiere funcionalidades más avanzadas, como enviar notificaciones entre plataformas, enrutamiento basado en suscripción o volúmenes de tamaño muy grande, considere utilizar Centros de notificaciones de Azure con su servicio móvil. Para obtener más información, consulte uno de los siguientes temas de Centros de notificaciones:

+ [Introducción a los Centros de notificaciones]
  <br/>Aprenda a aprovechar los Centros de notificaciones en su aplicación de la Tienda Windows.

+ [¿Qué son los Centros de notificaciones?]
	<br/>Aprenda a crear e insertar notificaciones para usuarios en varias plataformas.

+ [Envío de notificaciones de difusión a los suscriptores]
	<br/>Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

<!--+ [Envío de notificaciones a usuarios]
	<br/>Obtenga información sobre cómo enviar notificaciones de inserción desde un servicio móvil a usuarios específicos en cualquier dispositivo.

+ [Envío de notificaciones entre plataformas a los usuarios]
	<br/>Obtenga información sobre cómo usar las plantillas para enviar notificaciones de inserción desde un servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.
-->

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

* [Referencia del script de servidor de Servicios móviles]
  <br/>Obtenga más información acerca del registro y del uso de scripts de servidor.

* [Referencia conceptual de servicios móviles con .NET]
  <br/>Obtenga más información sobre el uso de Servicios móviles con .NET. 

<!-- Anchors. -->
[Creación de la tabla Registros]: #create-table
[Actualización de scripts para enviar notificaciones de inserción: #update-scripts
[Incorporación de notificaciones de inserción a la aplicación]: #add-push
[Inserción de datos para recibir notificaciones]: #test
[Pasos siguientes]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png



<!-- URLs. -->
[SDK de servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=268375
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Introducción a los servicios móviles]: /es-es/develop/mobile/tutorials/get-started-wp8
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-wp8
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-wp8
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-wp8
[Notificaciones de inserción para usuarios de la aplicación]: /es-es/develop/mobile/tutorials/push-notifications-to-users-wp8
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Portal de administración de Azure]: https://manage.windowsazure.com/
[mpns, objeto]: http://go.microsoft.com/fwlink/p/?LinkId=271130
[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referencia conceptual de servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library/
[Introducción a los Centros de notificaciones]: /es-es/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[¿Qué son los Centros de notificaciones?]: /es-es/develop/net/how-to-guides/service-bus-notification-hubs/
[Envío de notificaciones de difusión a los suscriptores]: /es-es/manage/services/notification-hubs/breaking-news-wp8/
[Envío de notificaciones a usuarios]: /es-es/manage/services/notification-hubs/notify-users/
[Envío de notificaciones entre plataformas a los usuarios]: /es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/



<!--HONumber=42-->
