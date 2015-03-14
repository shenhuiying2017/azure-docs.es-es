<properties 
	pageTitle="Introducción a los Centros de notificaciones de Azure" 
	description="Aprenda a usar los Centros de notificaciones de Azure para las notificaciones de inserción." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="dwrede"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>
# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

En este tema se muestra cómo puede usar los Centros de notificaciones de Azure para enviar notificaciones de inserción a aplicaciones Silverlight de Windows Phone 8 o Windows Phone 8.1. Si va a desarrollar para Windows Phone 8.1 (no Silverlight), consulte entonces la versión (/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/)[Windows Universal].
En este tutorial puede crear una aplicación de Windows Phone 8 vacía que recibe notificaciones de inserción mediante el servicios de notificaciones de inserción de Microsoft (MPNS). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su Centro de notificaciones.

> [AZURE.NOTE] El SDK de Windows Phone para Centros de notificaciones no admite el uso de WNS con aplicaciones Silverlight de Windows Phone 8.1. Para usar WNS (en lugar de MPNS) con aplicaciones Silverlight de Windows Phone 8.1, tiene que configurar sus credenciales WNS como se muestra en [Introducción a Windows Universal](/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/). Después, podrá registrarse desde el back-end, como se muestra en el tutorial [Notificación a usuarios](/es-es/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/), o usar las [API de REST de Centros de notificaciones](http://msdn.microsoft.com/library/dn223264.aspx).

Este tutorial le guiará a través de los siguientes pasos para habilitar las notificaciones de inserción:

1. [Creación del Centro de notificaciones]
2. [Conexión de la aplicación al Centro de notificaciones]
3. [Envío de notificaciones desde el back-end]

En este tutorial se demuestra el escenario de difusión sencillo con Centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para aprender a usar los Centros de notificaciones para abordar usuarios y grupos de dispositivos específicos. Este tutorial requiere lo siguiente:

+ [Visual Studio 2012 Express para Windows Phone, o una versión posterior.]

Completar este tutorial es un requisito previo para todos los demás tutoriales de Centros de notificaciones para aplicaciones de Windows Phone 8. 

> [AZURE.NOTE] para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20 target="_blank").

##<a name="configure-hub"></a>Creación del Centro de notificaciones

1. Inicie sesión en el [Portal de administración de Azure] y, a continuación, haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y finalmente en **Creación rápida**.

   	![][7]

3. Escriba un nombre para su Centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Crear un nuevo Centro de notificaciones**.

   	![][8]

4. Haga clic en el espacio de nombres que acaba de crear (por lo general ***nombre del Centro de notificaciones*-ns**) y, a continuación, haga clic en **Configurar** en la parte superior.

   	![][9]

5. Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el Centro de notificaciones que acaba de crear.

   	![][10]

6. Haga clic en **Información de conexión** en la parte inferior. Anote las dos cadenas de conexión.

   	![][12]

7. Haga clic en la pestaña **Configurar** y, a continuación, active la casilla **Habilite las notificaciones de inserción no autenticadas** en la sección **Configuración de notificaciones en Windows Phone**.

   	![][15]

Ahora tiene las cadenas de conexión necesarias para registrar su aplicación de Windows Phone 8 y enviar notificaciones.

> [AZURE.NOTE] Este tutorial usa MPNS en modo sin autenticar. El modo sin autenticar de MPNS viene con restricciones sobre las notificaciones que puede enviar a cada canal. Los Centros de notificaciones admiten el [modo sin autenticar de MPNS](http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105) (en inglés). <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

##<a name="connecting-app"></a>Conexión de la aplicación al Centro de notificaciones

1. En Visual Studio, cree una aplicación nueva de Windows Phone 8.

   	![][13]

	En Visual Studio 2013 Update 2 o versiones posteriores, cree en su lugar una aplicación Silverlight de Windows Phone.
	
	![][11]	

2. En Visual Studio, haga clic con el botón derecho en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**. 

	Esto muestra el cuadro de diálogo Administrar paquetes de NuGet.

3. Busque  `WindowsAzure.Messaging.Managed` y haga clic en **Instalar**, seleccione todos los proyectos en la solución y acepte los términos de uso. 

	![][20]

	Esto descarga, instala y agrega una referencia a todos los proyectos de la biblioteca de Mensajería de Azure para Windows usando el <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">paquete de NuGet WindowsAzure.Messaging.Managed</a>. 

4. Abra el archivo App.xaml.cs y agregue las siguientes instrucciones  `using`:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. En el siguiente código en la parte superior del método **Application_Launching** en App.xaml.cs:
	
	    var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    Asegúrese de insertar el nombre del centro y la cadena de conexión llamada **DefaultListenSharedAccessSignature** que obtuvo en la sección anterior.
    Este código recupera el valor de ChannelURI de la aplicación desde los MPNS y, a continuación, lo registra con su Centro de notificaciones. Garantiza también que el valor de ChannelURI se registre en su Centro de notificaciones cada vez que se inicia la aplicación.

	>[AZURE.NOTE]Este tutorial envía una notificación del sistema al dispositivo. Cuando envía una notificación de icono, debe llamar al método **BindToShellTile** en el canal. Para admitir notificaciones del sistema y notificaciones de icono, llame a ambos métodos, **BindToShellTile** y **BindToShellToast**. 
    
6. En el Explorador de soluciones, expanda **Propiedades**, abra el archivo WMAppManifest.xml, haga clic en la pestaña **Funcionalidades** y asegúrese de que la funcionalidad **ID___CAP___PUSH_NOTIFICATION** esté marcada.

   	![][14]

   	Esto asegura que la aplicación pueda recibir notificaciones de inserción.
	
7. Presione la tecla F5 para ejecutar la aplicación.

	Se muestra un mensaje de registro.

##<a name="send"></a>Envío de notificaciones desde el back-end

Puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end que use la <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz REST</a>. En este tutorial puede enviar notificaciones con una aplicación de consola .NET. Para ver un ejemplo de cómo enviar notificaciones desde un back-end de los Servicios móviles de Azure integrado en Centros de notificaciones, consulte **Introducción a las notificaciones de inserción en Servicios móviles** (back-end de [.NET](/es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/) | [back-end de JavaScript](/es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/)).  Para obtener un ejemplo de cómo enviar notificaciones mediante las API de REST, consulte **Uso de los Centros de notificaciones de Java y PHP** ([Java](/es-es/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/es-es/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Haga clic con el botón derecho en la solución, seleccione **Agregar** y **Nuevo proyecto...**, a continuación en **Visual C#** haga clic en **Windows** y **Aplicación de consola** y haga clic en **Aceptar**. 

   	![][6]

	Esto agrega una aplicación de consola nueva de Visual C# a la solución. También puede hacer esto en una solución separada. 

4. Haga clic con el botón derecho en **Herramientas**, luego en **Administrador de paquetes de biblioteca** y finalmente en **Consola del Administrador de paquetes**. 

	Esto muestra la Consola del Administrador de paquetes.

6. En la ventana de la consola, establezca **Proyecto predeterminado** en su nuevo proyecto de aplicación de consola y después ejecute el siguiente comando en la ventana de la consola:

        Install-Package WindowsAzure.ServiceBus
    
	Esta acción agrega una referencia al SDK de Bus de servicio de Azure con el <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">paquete WindowsAzure.ServiceBus de NuGet</a>. 

5. Abra el archivo Program.cs y agregue la siguiente instrucción  `using`:

        using Microsoft.ServiceBus.Notifications;

6. En la clase **Program**, agregue el siguiente método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Asegúrese de reemplazar el marcador de posición "nombre de centro" por el nombre del Centro de notificaciones que aparece en el portal en la pestaña **Centros de notificaciones**. Reemplace también el marcador de posición de la cadena de conexión por la cadena de conexión llamada **DefaultFullSharedAccessSignature** que obtuvo en la sección "Configuración del Centro de notificaciones". 

	>[AZURE.NOTE]Asegúrese de utilizar la cadena de conexión con acceso **Total**, no con acceso de **Escucha**. La cadena de acceso escuchar no tiene permisos para enviar notificaciones.

4. Posteriormente, agregue la siguiente línea al método Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Con el emulador de Windows Phone en ejecución y su aplicación cerrada, establezca el proyecto de aplicación de consola como el proyecto de inicio predeterminado y, a continuación, presione la tecla F5 para ejecutar la aplicación. 

	Recibirá una notificación del sistema. Al pulsar en el mensaje emergente, se carga la aplicación.

Puede encontrar todas las cargas posibles en los temas de [catálogo de notificaciones del sistema] y [catálogo de iconos] de MSDN.

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, difunde notificaciones a todos los dispositivos con Windows Phone 8. Para abordar usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios]. Si desea segmentar a sus usuarios por grupos de interés, puede leer [Uso de Centros de notificaciones para enviar noticias de último minuto]. Obtenga más información sobre el uso de Centros de notificaciones en la [Orientación sobre los Centros de notificaciones].

<!-- Anchors. -->
[Creación del Centro de notificaciones]: #configure-hub
[Conexión de la aplicación al Centro de notificaciones]: #connecting-app
[Envío de notificaciones desde el back-end]: #send
[Pasos siguientes]:#next-steps

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Orientación sobre los Centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedimientos de los Centros de notificaciones para Windows Phone 8]
[Modo MPNS autenticado]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Uso de los Centros de notificaciones para insertar notificaciones en los usuarios]: /es-es/manage/services/notification-hubs/notify-users-aspnet
[Uso de los Centros de notificaciones para enviar noticias de última hora]: /es-es/manage/services/notification-hubs/breaking-news-dotnet
[catálogo de notificaciones del sistema]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catálogo de iconos]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx

<!--HONumber=45--> 
