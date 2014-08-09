<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

Introducción a los Centros de notificaciones
============================================

[C\# para Tienda Windows](/es-es/manage/services/notification-hubs/getting-started-windows-dotnet "C# para Tienda Windows")[Windows Phone](/es-es/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/es-es/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/es-es/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/es-es/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación de la Tienda Windows. En este tutorial puede crear una aplicación de la Tienda Windows vacía que recibe notificaciones de inserción mediante el servicio de notificaciones de inserción de Windows (WNS). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su centro de notificaciones.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registro de la aplicación para las notificaciones de inserción](#register)
2.  [Configuración de su Centro de notificaciones](#configure-hub)
3.  [Conexión de su aplicación al Centro de notificaciones](#connecting-app)
4.  [Envío de notificaciones desde su back-end](#send)

En este tutorial se demuestra un escenario de difusión sencillo con centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para aprender a usar los centros de notificaciones para abordar usuarios y grupos de dispositivos específicos. Este tutorial requiere lo siguiente:

-   Microsoft Visual Studio 2012 Express para Windows 8
-   Una cuenta de la Tienda Windows activa

Completar este tutorial es un requisito previo para todos los tutoriales de Centros de notificaciones para aplicaciones de la Tienda Windows.

**Nota:**

para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Registro de la aplicaciónRegistro de la aplicación para la Tienda Windows
-------------------------------------------------------------------------

Para enviar notificaciones de inserción a las aplicaciones de la Tienda Windows desde Servicios móviles, debe enviar su aplicación a la Tienda Windows. A continuación, debe configurar su centro de notificaciones para que se integre con WNS.

1.  Si aún no ha registrado su aplicación, vaya a la [página Enviar una aplicación](http://go.microsoft.com/fwlink/p/?LinkID=266582) en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta Microsoft y, a continuación, haga clic en **App name**.

	![][0]

2.  Escriba el nombre de la aplicación en **App name**, haga clic en **Reserve app name** y, a continuación, haga clic en **Save**.

	![][1]

	Se crea un nuevo registro de la Tienda Windows para su aplicación.

3.  En Visual Studio 2012 Express para Windows 8, cree un nuevo proyecto de la Tienda Windows en Visual C\# con la plantilla **Aplicación vacía**.

	![][2]

4.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Store** y, a continuación, haga clic en **Associate App with the Store...**.

	![][3]

	Aparece el asistente **Associate Your App with the Windows Store**.

5.  En el asistente, haga clic en **Sign in** y, a continuación, inicie sesión con su cuenta Microsoft.

6.  Haga clic en la aplicación que ha registrado en el paso 2, haga clic en **Next** y, a continuación, en **Associate**.

	![][4]

	Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.    

7.  De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Services**.

	![][5] 

8.  En la página **Services**, haga clic en el **sitio de Live Services** en **Servicios móviles de Azure**.

	![][17]

9.  Haga clic en **Authenticating your service** y anote los valores de **secreto de cliente** y **Package security identifier (SID)**.

	![][6]

	**Nota de seguridad**

    El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No los comparta con nadie ni los distribuya con su aplicación.

Configuración de su Centro de notificacionesConfiguración de su Centro de notificaciones
----------------------------------------------------------------------------------------

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) y, a continuación, haga clic en **NEW** en la parte inferior de la pantalla.

2.  Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y finalmente en **Quick Create**.

	![][7]

3.  Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Create a new Notification Hub**.

	![][8]

4.  Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, a continuación, haga clic en la pestańa **Configure** en la parte superior.

	![][9]

5.  Seleccione la pestańa **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

	![][10]

6.  Seleccione la pestańa **Configure**, en la parte superior, escriba los valores de **secreto de cliente** y **Package SID** obtenidos de WNS en la sección anterior y, a continuación, haga clic en **Save**.

	![][11]

7.  Seleccione la pestańa **Panel** en la parte superior y, a continuación, haga clic en **Connection Information**. Anote las dos cadenas de conexión.

	![][12]

Su centro de notificaciones está ahora configurado para funcionar con WNS y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones.

Conexión de su aplicaciónConexión de su aplicación al Centro de notificaciones
------------------------------------------------------------------------------

1.  Agregue una referencia a la biblioteca de Mensajería de Azure para la Tienda Windows usando el paquete [WindowsAzure.Messaging.Managed NuGet](http://nuget.org/packages/WindowsAzure.Messaging.Managed/). En el menú principal de Visual Studio, haga clic en **Tools**, luego en **Library Package Manager** y finalmente en **Package Manager Console**. Posteriormente, en la ventana de la consola, escriba:

         Install-Package WindowsAzure.Messaging.Managed

    y, a continuación, presione **Entrar**.

2.  Abra el archivo App.xaml.cs y agregue las siguientes instrucciones `using`:

         using Windows.Networking.PushNotifications;
         using Microsoft.WindowsAzure.Messaging;
         using Windows.UI.Popups;

3.  Agregue el siguiente código a App.xaml.cs, en la clase `App`. Asegúrese de reemplazar el marcador de posición "nombre de centro" por el nombre del centro de notificaciones que aparece en el portal en la pestańa **Centros de notificaciones** (por ejemplo, **mynotificationhub2** del ejemplo anterior):

         private async void InitNotificationsAsync()
         {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
			
            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
			var result = await hub.RegisterNativeAsync(channel.Uri);
                
             // Muestra el Id. de registro para que sepa que fue correcto
             if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

          }

    Asegúrese de insertar el nombre del centro y la cadena de conexión llamada **DefaultListenSharedAccessSignature** que obtuvo en la sección anterior. Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra con su centro de notificaciones.

4.  En la parte superior del controlador de eventos **OnLaunched** en App.xaml.cs, agregue la siguiente llamada al nuevo método **InitNotificationsAsync**:

         InitNotificationsAsync();

    Esto garantiza que el valor de ChannelURI se registre en su centro de notificaciones cada vez que se inicia la aplicación.

5.  Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente con la clave de registro.

	![][19]

Envío de notificacionesEnvío de notificaciones desde su back-end
----------------------------------------------------------------

Puede enviar notificaciones mediante los centros de notificaciones desde cualquier back-end que use la [interfaz REST](http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx). En este tutorial puede enviar notificaciones con una aplicación de consola .NET y con un Servicio móvil mediante un script de Node.

Para enviar notificaciones mediante una aplicación .NET:

1.  Con la solución anterior todavía abierta en Visual Studio, en el Explorador de soluciones, haga doble clic en **Package.appxmanifest** para abrirlo en el editor de Visual Studio.

2.  Desplácese hasta **Todos los activos de imagen** y haga clic en **Logotipo del distintivo**. En **Notificaciones**, establezca **Capacidad de aviso** en **Yes**:

	![][18]

	En el menú **Archivo**, haga clic en **Guardar todo**.

3.  Ahora cree una aplicación de consola nueva de Visual C\#:
	
	![][13]

4.  Agregue una referencia al SDK de Bus de servicio de Azure con el [paquete WindowsAzure.ServiceBus NuGet](http://nuget.org/packages/WindowsAzure.ServiceBus/). En el menú principal de Visual Studio, haga clic en **Tools**, luego en **Library Package Manager** y finalmente en **Package Manager Console**. Posteriormente, en la ventana de la consola, escriba lo siguiente:

         Install-Package WindowsAzure.ServiceBus

    y, a continuación, presione **Entrar**.

5.  Abra el archivo Program.cs y agregue la siguiente instrucción `using`:

         using Microsoft.ServiceBus.Notifications;

6.  En la clase `Program`, agregue el siguiente método. Asegúrese de reemplazar el marcador de posición "nombre de centro" por nombre del centro de notificaciones que aparece en el portal en la pestańa **Centros de notificaciones**:

         private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

	Asegúrese de insertar el nombre del centro y la cadena de conexión llamada **DefaultFullSharedAccessSignature** que obtuvo en la sección "Configuración de su Centro de notificaciones". Tenga en cuenta que esta es la cadena de conexión con acceso **Total**, no acceso **Escuchar**.

7.  Agregue las siguientes líneas al método `Main`:

          SendNotificationAsync();
          Console.ReadLine();

8.  Presione la tecla **F5** para ejecutar la aplicación. Debería recibir una notificación del sistema.

	![][14]

Puede encontrar todas las cargas posibles en MSDN en el [catálogo de notificaciones del sistema](http://msdn.microsoft.com/es-es/library/windows/apps/hh761494.aspx), el [catálogo de iconos](http://msdn.microsoft.com/es-es/library/windows/apps/hh761491.aspx) y la [información general de distintivos](http://msdn.microsoft.com/es-es/library/windows/apps/hh779719.aspx) (en inglés).

Para enviar una notificación usando un Servicio móvil, siga la [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started/#create-new-service) y, a continuación, realice lo siguiente:

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) y haga clic en su Servicio móvil.

2.  Seleccione la pestańa **Scheduler** en la parte superior.

	![][15]

3.  Cree un nuevo trabajo programado, inserte un nombre y, a continuación, haga clic en **On demand**.

	![][16]

4.  Cuando se cree el trabajo, haga clic en el nombre del trabajo. Luego, haga clic en la pestańa **Script** de la barra superior.

5.  Inserte el siguiente script en su función de programador. Asegúrese de reemplazar los marcadores de posición por su nombre del centro de notificaciones y la cadena de conexión para *DefaultFullSharedAccessSignature* que obtuvo anteriormente. Cuando haya terminado, haga clic en **Save** en la barra inferior.

         var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
        notificationHubService.wns.sendToastText01(
            null,
            {
                text1: 'Hello from Mobile Services!!!'
            },
            function (error) {
                if (!error) {
                    console.warn("Notification successful");
                }
        });

6.  Haga clic en **Run Once** en la barra inferior. Debería recibir una notificación del sistema.

Pasos siguientes
----------------

En este sencillo ejemplo, difunde notificaciones a todos los dispositivos con Windows. Para abordar usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios](/es-es/manage/services/notification-hubs/notify-users-aspnet). Si desea segmentar a sus usuarios por grupos de interés, consulte [Uso de Centros de notificaciones para enviar noticias de último minuto](/es-es/manage/services/notification-hubs/breaking-news-dotnet). Para obtener más información sobre el uso de los Centros de notificaciones, consulte la [orientación sobre los Centros de notificaciones](http://msdn.microsoft.com/es-es/library/jj927170.aspx) y los [procedimientos de los Centros de notificaciones para la Tienda Windows](http://msdn.microsoft.com/es-es/library/jj927172.aspx) (en inglés).

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-win8-app.png
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