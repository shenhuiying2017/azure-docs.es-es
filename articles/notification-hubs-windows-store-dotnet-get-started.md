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

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal" class="current">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación de la Tienda Windows o de Windows Phone 8.1 (no Silverlight). Si su objetivo es Silverlight para Windows Phone 8.1, consulte la versión de [Windows Phone](/es-es/documentation/articles/notification-hubs-windows-phone-get-started/). 
En este tutorial puede crear una aplicación de la Tienda Windows vacía que recibe notificaciones de inserción mediante el servicio de notificaciones de inserción de Windows (WNS). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su Centro de notificaciones.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Registro de la aplicación para las notificaciones de inserción]
2. [Configuración del Centro de notificaciones]
3. [Conexión de la aplicación al Centro de notificaciones]
4. [Envío de notificaciones desde el back-end]

En este tutorial se demuestra un escenario de difusión sencillo con Centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para aprender a usar los Centros de notificaciones para abordar usuarios y grupos de dispositivos específicos. Este tutorial requiere lo siguiente:

+ Microsoft Visual Studio Express 2013 para Windows con Update 2<br/>Esta versión de Visual Studio es necesaria para crear un proyecto de aplicación universal. Si solo desea crear un aplicación para la Tienda Windows, necesita Visual Studio 2012 Express para Windows 8.

+ Una cuenta de la Tienda Windows activa

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

Completar este tutorial es un requisito previo para todos los tutoriales de Centros de notificaciones para aplicaciones de la Tienda Windows. 

##<a name="register"></a>Registro de la aplicación para la Tienda Windows

Para enviar notificaciones de inserción a las aplicaciones de la Tienda Windows desde Servicios móviles, debe enviar su aplicación a la Tienda Windows. A continuación, debe configurar su Centro de notificaciones para que se integre con WNS.

1. Si aún no ha registrado la aplicación, navegue a la página <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Enviar una aplicación</a> en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión con su cuenta Microsoft y, a continuación, haga clic en **Nombre de la aplicación**.

   	![][0]

2. Escriba el nombre de la aplicación en **Nombre de la aplicación**, haga clic en **Reservar nombre de aplicación** y, a continuación, haga clic en **Guardar**.

   	![][1]

   	Se crea un nuevo registro de la Tienda Windows para su aplicación.

3. En Visual Studio, cree un nuevo proyecto de aplicaciones de la Tienda en Visual C# con la plantilla **Aplicación vacía**.

   	![][2]

4. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto de la aplicación para la Tienda Windows, haga clic en **Tienda** y, a continuación, en **Asociar aplicación con la Tienda...**. 

   	![][3]

   	Aparece el asistente **Asocie la aplicación con la Tienda Windows**.

5. En el asistente, haga clic en **Iniciar sesión** y, a continuación, inicie sesión con su cuenta Microsoft.

6. Haga clic en la aplicación que registró en el paso 2, haga clic en **Siguiente** y, después, en **Asociar**.

   	![][4]

   	Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación. 

7. (Opcional) Repita los pasos 4-6 para el proyecto de la aplicación de la Tienda de Windows Phone.  

7. De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Services** (Servicios). 

   	![][5] 

8. En la página **Servicios**, haga clic en el **sitio de Servicios de Live** en **Servicios móviles de Microsoft Azure**.

   	![][17]

9. En la pestaña **Configuración de aplicación**, anote los valores de **Secreto del cliente** e **Identificador de seguridad de paquete (SID)**. 

   	![][6]

 	> [AZURE.NOTE] **Nota de seguridad**
	El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No los comparta con nadie ni los distribuya con su aplicación.

##<a name="configure-hub"></a>Configuración del Centro de notificaciones

1. Inicie sesión en el [Portal de administración de Azure] y haga clic en **NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y, a continuación, en **Creación rápida**.

   	![][7]

3. Escriba un nombre para el Centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Crear una nueva base de datos central de notificaciones**.

   	![][8]

4. Haga clic en el espacio de nombres que acaba de crear (por lo general ***nombre del Centro de notificaciones*-ns**) y, a continuación, haga clic en la pestaña **Configurar** en la parte superior.

   	![][9]

5. Seleccione la pestaña **Bases de datos centrales de notificaciones** en la parte superior y, a continuación, haga clic en el Centro de notificaciones que acaba de crear.

   	![][10]

6. Seleccione la pestaña **Configurar** en la parte superior, escriba los valores de **Secreto del cliente** y **SID del paquete** obtenidos de WNS en la sección anterior y, a continuación, haga clic en **Guardar**.

   	![][11]

7. Seleccione la pestaña **Panel** en la parte superior y, a continuación, haga clic en **Información de conexión**. Anote las dos cadenas de conexión.

   	![][12]

Su Centro de notificaciones está ahora configurado para funcionar con WNS y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones.

##<a name="connecting-app"></a>Conexión de la aplicación al Centro de notificaciones

1. En Visual Studio, haga clic con el botón derecho en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**. 

	Esto muestra el cuadro de diálogo Administrar paquetes de NuGet.

2. Busque `WindowsAzure.Messaging.Managed` y haga clic en **Instalar**, seleccione todos los proyectos de la solución y acepte los términos de uso. 

	![][20]

	Esta acción descarga, instala y agrega una referencia a todos los proyectos de la biblioteca de Mensajería de Azure para Windows usando el <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">paquete de NuGet WindowsAzure.Messaging.Managed</a>. 

3. Abra el archivo de proyecto App.xaml.cs y agregue las siguientes instrucciones `using`:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;

	En un proyecto universal, este archivo se encuentra en la carpeta `<Nombre_proyecto>.Shared`.

4. También en App.xaml.cs, agregue la siguiente definición de método **InitNotificationsAsync** a la clase **App**:
	
	    private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
			
            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
			var result = await hub.RegisterNativeAsync(channel.Uri);
            
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }
	
    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra con su Centro de notificaciones.

    >[AZURE.NOTE]Asegúrese de reemplazar el marcador de posición "hub name" por el nombre del Centro de notificaciones que aparece en el portal en la pestaña **Bases de datos centrales de notificaciones** (por ejemplo, **mynotificationhub2** en el ejemplo anterior). Sustituya también el marcador de posición de la cadena de conexión por la cadena de conexión **DefaultListenSharedAccessSignature** que obtuvo en la sección anterior.
    
5. En la parte superior del controlador de eventos **OnLaunched** en App.xaml.cs, agregue la siguiente llamada al nuevo método **InitNotificationsAsync**:

        InitNotificationsAsync();

    Esto garantiza que el valor de ChannelURI se registre en su Centro de notificaciones cada vez que se inicia la aplicación.

6. En el Explorador de soluciones, haga doble clic en **Package.appxmanifest** de la aplicación de la Tienda Windows y, en **Notificaciones**, establezca **Capacidad de aviso** en **Sí**:

   	![][18]

   	En el menú **Archivo**, haga clic en **Guardar todo**.

7. (Opcional) Repita los pasos anteriores para el proyecto de la aplicación de la Tienda de Windows Phone.

8. Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente con la clave de registro.
   
   	![][19]

9. (Opcional) Repita el paso anterior para ejecutar el otro proyecto.

La carpeta ahora ya está lista para recibir notificaciones.

##<a name="send"></a>Envío de notificaciones desde el back-end

Puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end que use la <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz REST</a>. En este tutorial puede enviar notificaciones con una aplicación de consola .NET. Para ver un ejemplo de cómo enviar notificaciones desde un back-end de los Servicios móviles de Azure integrado en Centros de notificaciones, vea **Introducción a las notificaciones de inserción en Servicios móviles** ([back-end .NET](/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/) | [back-end JavaScript](/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)).  Para ver un ejemplo de cómo enviar notificaciones con las API de REST, consulte **Uso de los Centros de notificaciones desde Java o PHP** ([Java](/es-es/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/es-es/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Haga clic con el botón derecho en la solución, seleccione **Agregar**, **Nuevo proyecto...** y, después, en **Visual C#**, haga clic en **Windows**, **Aplicación de consola** y **Aceptar**. 

   	![][13]

	Esto agrega una aplicación de consola nueva de Visual C# a la solución. También puede hacer esto en una solución separada. 

4. Haga clic con el botón derecho en **Herramientas**, luego en **Administrador de paquetes de la biblioteca** y finalmente en **Consola del administrador de paquetes**. 

	Esto muestra la Consola del Administrador de paquetes.

6. En la ventana de la consola, establezca **Proyecto predeterminado** en su nuevo proyecto de aplicación de consola y después ejecute el siguiente comando en la ventana de la consola:

        Install-Package WindowsAzure.ServiceBus
    
	Esta acción agrega una referencia al SDK de Bus de servicio de Azure con el <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">paquete de NuGet WindowsAzure.ServiceBus</a>. 

5. Abra el archivo Program.cs y agregue la siguiente instrucción `using`:

        using Microsoft.ServiceBus.Notifications;

6. En la clase **Program**, agregue el método siguiente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	Asegúrese de reemplazar el marcador de posición "hub name" por el nombre del Centro de notificaciones que aparece en el portal en la pestaña **Bases de datos centrales de notificaciones**. Sustituya también el marcador de posición de la cadena de conexión por la cadena de conexión llamada **DefaultFullSharedAccessSignature** que obtuvo en la sección "Configuración del Centro de notificaciones". 

	>[AZURE.NOTE]Asegúrese de usar la cadena de conexión con acceso **Total**, no con acceso **Escuchar**. La cadena de acceso escuchar no tiene permisos para enviar notificaciones.

7. A continuación, agregue las siguientes líneas al método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

8. Con la aplicación de la consola establecida como proyecto de inicio, presione la tecla **F5** para ejecutar la aplicación. 

   	![][14]

	Debería recibir una notificación del sistema en todos los dispositivos registrados. Si hace clic o toca el banner de notificaciones se carga la aplicación.

Puede encontrar todas las cargas compatibles en los temas de [catálogo de notificaciones del sistema], [catálogo de iconos] e [información general sobre distintivos] de MSDN.

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, difunde notificaciones a todos los dispositivos con Windows. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de los Centros de notificaciones para insertar notificaciones para los usuarios]. Si desea segmentar los usuarios por grupos de interés, consulte [Uso de los Centros de notificaciones para enviar noticias de última hora]. Para obtener más información sobre el uso de Centros de notificaciones, consulte [Información general acerca de los Centros de notificaciones].

<!-- Anchors. -->
[Registro de la aplicación para las notificaciones de inserción]: #register
[Configuración del Centro de notificaciones]: #configure-hub
[Conexión de la aplicación al Centro de notificaciones]: #connecting-app
[Envío de notificaciones desde el back-end]: #send
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-dotnet
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-dotnet
[Notificaciones de inserción para usuarios de la aplicación]: /es-es/develop/mobile/tutorials/push-notifications-to-users-dotnet
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript y HTML]: /es-es/develop/mobile/tutorials/get-started-with-push-js

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Información general acerca de los Centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx

[Uso de los Centros de notificaciones para insertar notificaciones para los usuarios]: /es-es/manage/services/notification-hubs/notify-users-aspnet
[Uso de los Centros de notificaciones para enviar noticias de última hora]: /es-es/manage/services/notification-hubs/breaking-news-dotnet

[catálogo de notificaciones del sistema]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[catálogo de iconos]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[información general sobre distintivos]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx

<!--HONumber=45--> 
