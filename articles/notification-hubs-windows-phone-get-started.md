<properties urlDisplayName="Get Started" pageTitle="Introducción a los Centros de notificaciones de Azure" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />
# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

En este tema se muestra cómo puede usar los Centros de notificaciones de Azure para enviar notificaciones de inserción a aplicaciones Silverlight de Windows Phone 8 o Windows Phone 8.1. Si va a desarrollar para Windows Phone 8.1 (no Silverlight), consulte entonces la versión [Windows Universal](/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/).
En este tutorial puede crear una aplicación de Windows Phone 8 vacía que recibe notificaciones de inserción mediante el servicios de notificaciones de inserción de Microsoft (MPNS). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su centro de notificaciones.

> [AZURE.NOTE] El SDK de Windows Phone para Centros de notificaciones no admite el uso de WNS con aplicaciones Silverlight de Windows Phone 8.1. Para usar WNS (en lugar de MPNS) con aplicaciones Silverlight de Windows Phone 8.1, tiene que configurar sus credenciales WNS como se muestra en [Get Started for Windows Universal](/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/) (Introducción a Windows Universal). Después, podrá registrarse desde el back-end, como se muestra en el tutorial [Notificación a los usuarios](/es-es/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/), o usar las [API de REST de Centros de notificaciones](http://msdn.microsoft.com/es-es/library/dn223264.aspx).

Este tutorial le guiará a través de los siguientes pasos para habilitar las notificaciones de inserción:

1. [Creación de su centro de notificaciones]
2. [Conexión de su aplicación al centro de notificaciones]
3. [Envío de notificaciones desde su backend]

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para aprender a usar los centros de notificaciones para abordar usuarios y grupos de dispositivos específicos. Este tutorial requiere lo siguiente:

+ [Visual Studio 2012 Express para Windows Phone], o una versión posterior.

Completar este tutorial es un requisito previo para todos los demás tutoriales de centros de notificaciones para aplicaciones de Windows Phone 8. 

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.</p></div>

##<a name="configure-hub"></a>Creación de su centro de notificaciones

1. Inicie sesión en el [Portal de administración de Azure] y luego haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y **Creación rápida**.

   	![][7]

3. Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Crear un nuevo centro de notificaciones**.

   	![][8]

4. Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, a continuación, haga clic en **Configurar** en la parte superior.

   	![][9]

5. Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

   	![][10]

6. Haga clic en **Connection Information** (Información de conexión) en la parte inferior. Anote las dos cadenas de conexión.

   	![][12]

7. Haga clic en la pestaña **Configurar**, y luego en la casilla **Enable unauthenticated push notifications** (Habilitar las notificaciones de inserción sin autenticar) en la sección **Windows Phone notifications settings** (Configuración de notificaciones de Windows Phone).

   	![][15]

Ahora tiene las cadenas de conexión necesarias para registrar su aplicación de Windows Phone 8 y enviar notificaciones.

<div class="dev-callout"><b>Nota:</b>
		<p>Este tutorial usa MPNS en modo sin autenticar. El modo sin autenticar de MPNS viene con restricciones sobre las notificaciones que puede enviar a cada canal. Los Centros de notificaciones admiten el <a href="http://msdn.microsoft.com/es-es/library/windowsphone/develop/ff941099(v=vs.105).aspx">modo sin autenticar de MPNS</a> (en inglés). <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

##<a name="connecting-app"></a>Conexión de su aplicación al centro de notificaciones

1. En Visual Studio, cree una aplicación nueva de Windows Phone 8.

   	![][13]

	En Visual Studio 2013 Update 2 o versiones posteriores, cree en su lugar una aplicación Silverlight de Windows Phone.
	
	![][11]	

2. En Visual Studio, haga clic con el botón secundario en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**. 

	Esto muestra el cuadro de diálogo Administrar paquetes de NuGet.

3. Busque `WindowsAzure.Messaging.Managed` y haga clic en **Instalar** y acepte los términos de uso. 

	![][20]

	Esta acción descarga, instala y agrega una referencia a la biblioteca de Mensajería de Azure para Windows usando el <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">paquete WindowsAzure.Messaging.Managed de NuGet</a>. 

4. Abra el archivo App.xaml.cs y agregue la siguiente instrucción `using`:

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
    Este código recupera el valor de ChannelURI de la aplicación desde los MPNS y, a continuación, lo registra con su centro de notificaciones. Garantiza también que el valor de ChannelURI se registre en su centro de notificaciones cada vez que se inicia la aplicación.

	>[WACOM.NOTE]Este tutorial envía una notificación del sistema al dispositivo. Cuando envía una notificación de icono, debe llamar al método **BindToShellTile** en el canal. Para admitir notificaciones del sistema y notificaciones de icono, llame a ambos métodos, **BindToShellTile** y **BindToShellToast**. 
    
6. En el Explorador de soluciones, expanda **Propiedades**, abra el archivo WMAppManifest.xml, haga clic en la pestaña **Funcionalidades** y asegúrese de que la funcionalidad **ID___CAP___PUSH_NOTIFICATION** esté marcada.

   	![][14]

   	Esto asegura que la aplicación pueda recibir notificaciones de inserción.
	
7. Presione la tecla F5 para ejecutar la aplicación.

	Se muestra un mensaje de registro.

##<a name="send"></a>Envío de notificaciones desde su backend

Puede enviar notificaciones mediante los centros de notificaciones desde cualquier back-end que use la <a href="http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx">interfaz REST</a>. En este tutorial puede enviar notificaciones con una aplicación de consola .NET. Para ver un ejemplo de cómo enviar notificaciones desde un backend de los Servicios móviles de Azure integrado en centros de notificaciones, vea **Introducción a las notificaciones de inserción en Servicios móviles** ([.NET backend](/es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/) | [JavaScript backend](/es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/)).  Para ver un ejemplo de cómo enviar notificaciones con las API de REST, consulte **Uso de Centro de notificaciones desde Java/PHP** ([Java](/es-es/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/es-es/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Haga clic con el botón secundario en la solución, seleccione **Agregar** y **Nuevo proyecto...**, luego en **Visual C#** haga clic en **Windows** y **Aplicación de consola** y haga clic en **Aceptar**. 

   	![][6]

	Esta acción agrega una aplicación de consola nueva de Visual C# a la solución. También puede hacer esto en una solución separada. 

4. Haga clic con el botón secundario en **Tools** (Herramientas), luego haga clic en **Library Package Manager** (Administrador de paquetes de biblioteca) y finalmente en **Package Manager Console** (Consola del Administrador de paquetes). 

	Esto muestra la Consola del Administrador de paquetes.

6. En la ventana de la consola, establezca **Proyecto predeterminado** en su nuevo proyecto de aplicación de la consola y, a continuación, en la ventana de la consola, ejecute el siguiente comando:

        Install-Package WindowsAzure.ServiceBus
    
	Esta acción agrega una referencia al SDK de Bus de servicio de Azure con el <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/"> paquete WindowsAzure.ServiceBus de NuGet</a>. 

5. Abra el archivo Program.cs y agregue la siguiente instrucción `using`:

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

	Asegúrese de reemplazar el marcador de posición "nombre de centro" por nombre del centro de notificaciones que aparece en el portal en la pestaña **Centros de notificaciones**. Sustituya también el marcador de la cadena de conexión por la cadena de conexión llamada **DefaultListenSharedAccessSignature** que obtuvo en la sección "Configurar el centro de notificaciones". 

	>[WACOM.NOTE]Asegúrese de utilizar la cadena de conexión con acceso **Total**, no con acceso **Escuchar**. La cadena de acceso de escucha no tiene permisos para enviar notificaciones.

4. Posteriormente, agregue la siguiente línea al método Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Con el emulador de Windows Phone en ejecución y su aplicación cerrada, establezca el proyecto de aplicación de consola como el proyecto de inicio predeterminado y, a continuación, presione la tecla F5 para ejecutar la aplicación. 

	Recibirá una notificación del sistema. Al pulsar en el mensaje emergente, se carga la aplicación.

Puede encontrar todas las cargas posibles en los temas de [catálogo de notificaciones del sistema] y [catálogo de iconos] de MSDN.

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, difunde notificaciones a todos los dispositivos con Windows Phone 8. Para abordar usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios]. Si desea segmentar a sus usuarios por grupos de interés, puede leer [Uso de Centros de notificaciones para enviar noticias de último minuto]. Obtenga más información sobre el uso de Centros de notificaciones en [Información general acerca de los Centros de notificaciones].

<!-- Anchors. -->
[Creación de su centro de notificaciones]: #configure-hub
[Conexión de su aplicación al centro de notificaciones]: #connecting-app
[Envío de notificaciones desde su backend]: #send
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
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Información general acerca de los Centros de notificaciones de Azure]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
[Procedimientos de los Centros de notificaciones para Windows Phone 8]: tbd!!!
[Modo MPNS autenticado]: http://msdn.microsoft.com/es-es/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Uso de los Centros de notificaciones para las notificaciones de inserción a los usuarios]: /es-es/manage/services/notification-hubs/notify-users-aspnet
[Uso de los Centros de notificaciones para enviar noticias de última hora]: /es-es/manage/services/notification-hubs/breaking-news-dotnet
[catálogo del sistema]: http://msdn.microsoft.com/es-es/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catálogo de iconos]: http://msdn.microsoft.com/es-es/library/windowsphone/develop/hh202948(v=vs.105).aspx
