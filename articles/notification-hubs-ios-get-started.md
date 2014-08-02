<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

Introducción a los Centros de notificaciones
============================================

[C\# de Tienda Windows](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

Este tema muestra cómo puede usar Centros de notificaciones de Microsoft Azure para enviar notificaciones de inserción a una aplicación de iOS. En este tutorial puede crear una aplicación de iOS vacía que recibe notificaciones de inserción mediante el servicio de notificaciones de inserción de Apple (APN). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su centro de notificaciones.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Generación del archivo de solicitud de firma de certificado](#certificates)
2.  [Registro de la aplicación y habilitación para las notificaciones de inserción](#register)
3.  [Creación de un perfil de aprovisionamiento para la aplicación](#profile)
4.  [Configuración de su Centro de notificaciones](#configure-hub)
5.  [Conexión de su aplicación al Centro de notificaciones](#connecting-app)
6.  [Envío de notificaciones desde su back-end](#send)

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para aprender a usar los centros de notificaciones para abordar usuarios y grupos de dispositivos específicos. Este tutorial cuenta con los siguientes requisitos previos:

-   [SDK de iOS para Servicios móviles](http://go.microsoft.com/fwLink/?LinkID=266533)
-   [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   Dispositivo compatible con iOS 5.0 (o una versión posterior)
-   Pertenencia al programa para desarrolladores de iOS

    **Nota:**

    Debido a los requisitos de configuración de las notificaciones de inserción, debe implementar y probar las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

Completar este tutorial es un requisito previo para todos los tutoriales de centros de notificaciones para aplicaciones de iOS.

**Nota:**

Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

El servicio de notificaciones de inserción de Apple (APNS) usa certificados para autenticar su servicio móvil. Siga estas instrucciones para crear los certificados necesarios y cargarlos en su servicio móvil. Para consultar la documentación oficial de la característica APNS, consulte [Apple Push Notification Service].

Generación de un archivo CSRGeneración del archivo de solicitud de firma de certificado
---------------------------------------------------------------------------------------

Primero debe generar el archivo de solicitud de firma de certificado (CSR) que Apple usa para generar un certificado firmado.

1.  En la carpeta Utilities, ejecute la herramienta Acceso a llaves.

2.  Haga clic en **Acceso a llaves**, expanda **Certificate Assistant** y, a continuación, haga clic en **Request a Certificate from a Certificate Authority...**.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png)

1.  Seleccione una dirección de correo electrónico de usuario en **User Email Address** y un nombre común en **Common Name**, asegúrese de que **Saved to disk** se encuentra seleccionado y, a continuación, haga clic en **Continue**. Deje el campo **CA Email Address** en blanco, ya que no es obligatorio.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png)

1.  Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Save As**, seleccione la ubicación en **Where** y, a continuación, haga clic en **Save**.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png)

De esta forma, se guarda el archivo CSR en la ubicación seleccionada; el escritorio es la ubicación predeterminada. Recuerde la ubicación seleccionada para este archivo.

A continuación, se registrará la aplicación en Apple, se habilitarán las notificaciones de inserción y se cargará este archivo CSR exportado para crear un certificado de inserción.

Registro de la aplicaciónRegistro de la aplicación para las notificaciones de inserción
---------------------------------------------------------------------------------------

Para poder enviar notificaciones de inserción a una aplicación iOS desde servicios móviles, debe registrar su aplicación con Apple y también registrar las notificaciones de inserción.

1.  Si aún no ha registrado su aplicación, diríjase al [portal de aprovisionamiento de iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) (en inglés) del Centro para desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identifiers** y, a continuación, en **App IDs**. Para finalizar, haga clic en el signo **+** para registrar una nueva aplicación.

	![][B102] 

2.  Escriba un nombre para su aplicación en **Description**, especifique el valor *MobileServices.Quickstart* en **Bundle Identifier**, haga clic en la opción de notificaciones de inserción "Push Notifications" de la sección de servicios de aplicaciones "App Services" y, a continuación, haga clic en **Continue**. En este ejemplo se usa el identificador **MobileServices.Quickstart** pero es posible que no pueda volver a usar ese mismo identificador, ya que los identificadores de aplicaciones deben ser exclusivos entre todos los usuarios. Por ese motivo, es recomendable que agregue su nombre completo o sus iniciales después del nombre de la aplicación.

	![][B103]
           
	De esta forma, se genera el identificador de la aplicación y se solicita que envíe (**Submit**) la información. Haga clic en **Submit**.
           
	![][B104] 
           
	Una vez que haga clic en **Submit**, verá la pantalla **Registration complete** como se muestra a continuación. Haga clic en **Done**.
           
	![][B105]

    > [WACOM.NOTE] Si elige proporcionar un valor **Bundle Identifier** distinto a *MobileServices.Quickstart*, también debe actualizar el identificador de agrupación de trabajos en el proyecto de Xcode.

3.  Busque el identificador de la aplicación que acaba de crear y haga clic en su fila.

	![][B106]
           
	Si hace clic en el identificador de la aplicación, se mostrará información sobre la aplicación y el identificador de la aplicación. Haga clic en el botón **Settings**.
           
	![][B107] 

4.  Desplácese a la parte inferior de la pantalla y haga clic en el botón **Create Certificate...** en la sección **Development Push SSL Certificate**.

	![][B108] 

	Se mostrará el asistente "Add iOS Certificate".
           
	![][B108] 

    > [WACOM.NOTE] Este tutorial usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producción. Asegúrese de que establece el mismo tipo de certificado cuando carga el certificado en Servicios móviles.

5.  Haga clic en **Choose File**, diríjase a la ubicación en la que guardó el archivo CSR que creó en la primera tarea y, a continuación, haga clic en **Generate**.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png)

1.  Una vez que el portal haya creado el certificado, haga clic en el botón **Download** y haga clic en **Done**.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png)
 De esta forma, se descarga el certificado de firma y se guarda en su equipo en la carpeta de descargas.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] De forma predeterminada, el certificado de desarrollo del archivo descargado tiene el nombre **aps_development.cer**.

1.  Haga doble clic en el certificado de inserción descargado **aps\_development.cer**.

	De esta forma, se instala un nuevo certificado en las llaves como se muestra a continuación:

	![][B10]

    > [WACOM.NOTE] El nombre del certificado puede ser distinto, pero tendrá el prefijo de los **servicios de notificaciones de inserción de iOS de desarrollo de Apple:**

A continuación, usará este certificado para generar un archivo .p12 y cargarlo en Servicios móviles para permitir la autenticación con APNS.

Aprovisionamiento de la aplicaciónCreación de un perfil de aprovisionamiento para la aplicación
-----------------------------------------------------------------------------------------------

1.  Vuelva al [portal de aprovisionamiento de iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) (en inglés), seleccione **Provisioning Profiles**, seleccione **All** y, a continuación, haga clic en el botón **+** para crear un nuevo perfil. De esta forma, aparecerá el asistente de **Add iOS Provisioning Profile**.

	![][120]

2.  Seleccione **iOS App Development** en **Development** como tipo de perfil de aprovisionamiento y haga clic en **Continue**.

	![][121]

3.  A continuación, seleccione el identificador de la aplicación para la aplicación Mobile Services Quickstart en la lista desplegable **App ID** y haga clic en **Continue**.

	![][122]

4.  En la pantalla **Select certificates**, seleccione el certificado creado anteriormente y haga clic en **Continue**.

	![][123]

5.  A continuación, seleccione los **dispositivos** que usará para la prueba y haga clic en **Continue**.

	![][124]

6.  Para terminar, seleccione un nombre para el perfil en **Profile Name** y haga clic en **Generate** y en **Done**.

	![][125]
           
	![][126]

    De esta forma, se creará un nuevo perfil de aprovisionamiento.

7.  En Xcode, abra el organizador, seleccione la vista de dispositivos, seleccione **Provisioning Profiles** en la sección **Library** del panel izquierdo e importe el perfil de aprovisionamiento que acaba de crear.

8.  En la izquierda, seleccione el dispositivo e importe de nuevo el perfil de aprovisionamiento.

9.  En Acceso a llaves, haga clic con el botón secundario en el nuevo certificado, haga clic en **Export**, escriba un nombre para el certificado, seleccione el formato **.p12** y, a continuación, haga clic en **Save**.

	![][18]

    Anote el nombre de archivo y la ubicación del certificado exportado.

Configuración de su Centro de notificacionesConfiguración de su Centro de notificaciones
----------------------------------------------------------------------------------------

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) y haga clic en **+NEW** en la parte inferior de la pantalla.

2.  Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y finalmente en **Quick Create**.

	![][27]

3.  Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Create a new Notification Hub**.

	![][28]

4.  Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, a continuación, haga clic en la pestaña **Configure** en la parte superior.

	![][29]

5.  Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

	![][210]

6.  Seleccione la pestaña **Configure** en la parte superior y, a continuación, haga clic en **Upload** para la configuración de notificaciones de Apple. A continuación, seleccione el certificado **.p12** que exportó anteriormente y la contraseña para el certificado. Asegúrese de seleccionar si desea usar el servicio de inserción **Production** (si desea enviar notificaciones de inserción a usuarios que adquirieron la aplicación desde la tienda) o **Sandbox** (durante el desarrollo).

	![][211]

7.  Haga clic en la pestaña **Panel** en la parte superior y, a continuación, haga clic en **Connection Information**. Anote las dos cadenas de conexión.

	![][212]

Su centro de notificaciones está ahora configurado para funcionar con APN y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones.

Conexión de su aplicaciónConexión de su aplicación al Centro de notificaciones
------------------------------------------------------------------------------

1.  En XCode, cree un nuevo proyecto iOS y seleccione la plantilla **Single View Application**.

	![][31]

2.  En **Targets**, haga clic en el nombre de proyecto y, a continuación, expanda **Code Signing Identity** y, a continuación, en **Debug**, seleccione el perfil de identidad de firma de código. Además, si usa una versión más reciente de XCode, cambie **Levels** de **Basic** a **All** y establezca el elemento de línea **Provisioning Profile** en el perfil de aprovisionamiento.

	![][32]

3.  Descargue Azure Mobile SDK para iOS. Abra el archivo .zip y arrastre la carpeta WindowsAzureMessaging.framework a la carpeta Framework en el proyecto XCode. Seleccione **Copy items in destination group's folder** y, a continuación, haga clic en **OK**.

	![][33]

4.  En el archivo AppDelegate.h, agregue la siguiente directiva de importación:

          #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  En el archivo AppDelegate.m, agregue el siguiente código en el método `didFinishLaunchingWithOptions`:

          [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  En el mismo archivo, agregue el siguiente método:

         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
             SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
                                       @"<connection string>" notificationHubPath:@"mynh"];
                
             [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                 if (error != nil) {
                     NSLog(@"Error de registro de notificaciones: %@", error);
                 }
            }];
         }

7.  *(opcional)* De nuevo, en el mismo archivo, agregue el siguiente método para ver **UIAlert** si la notificación se recibió cuando la aplicación estaba activa:

         - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
             NSLog(@"%@", userInfo);
             UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
             [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
             @"OK" otherButtonTitles:nil, nil];
             [alert show];
         }

8.  Ejecute la aplicación en el dispositivo.

Envío de notificacionesEnvío de notificaciones desde su back-end
----------------------------------------------------------------

Puede enviar notificaciones mediante los centros de notificaciones desde cualquier back-end que use la [interfaz REST](http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx). En este tutorial enviaremos notificaciones con una aplicación de consola .NET y con un Servicio móvil mediante un script de Node.

Para enviar notificaciones mediante una aplicación .NET:

1.  Cree una aplicación de consola nueva de Visual C\#:

    ![](./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png)

2.  Agregue una referencia al SDK de Bus de servicio de Azure con el [paquete WindowsAzure.ServiceBus NuGet](http://nuget.org/packages/WindowsAzure.ServiceBus/). En el menú principal de Visual Studio, haga clic en **Tools**, luego en **Library Package Manager** y finalmente en **Package Manager Console**. Posteriormente, en la ventana de la consola, escriba:

         Install-Package WindowsAzure.ServiceBus

    y presione Entrar.

3.  Abra el archivo Program.cs y agregue la siguiente instrucción using:

         using Microsoft.ServiceBus.Notifications;

4.  En la clase `Program`, agregue el siguiente método. Asegúrese de reemplazar el marcador de posición "nombre de centro" por el nombre del centro de notificaciones que aparece en el portal en la pestaña **Centros de notificaciones** (por ejemplo, **mynotificationhub2** del ejemplo anterior):

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{ private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;aps private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;:{ private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;alert private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;: private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;Hello from .NET! private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }

5.  Posteriormente, agregue la siguiente línea al método `Main`:

          SendNotificationAsync();
          Console.ReadLine();

6.  Presione la tecla F5 para ejecutar la aplicación. Debe recibir una alerta en el dispositivo. Si usa Wi-Fi, asegúrese de que la conexión funciona.

Puede buscar todas las cargas posibles en la guía [Local and Push Notification Programming Guide](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1) de Apple.

Para enviar una notificación usando un Servicio móvil, siga la [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-ios) y, a continuación, realice los siguientes procedimientos:

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) y seleccione su Servicio móvil.

2.  Seleccione la pestaña **Scheduler** en la parte superior.

	![][215]

3.  Cree un nuevo trabajo programado, inserte un nombre y, a continuación, seleccione **On demand**.

	![][216]

4.  Cuando se cree el trabajo, haga clic en el nombre del trabajo. A continuación, haga clic en la pestaña **Script** de la barra superior.

5.  Inserte el siguiente script en su función de programador. Asegúrese de reemplazar los marcadores de posición por su nombre del centro de notificaciones y la cadena de conexión para *DefaultFullSharedAccessSignature* que obtuvo anteriormente. Haga clic en **Save**.

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);

6.  Haga clic en **Run Once** en la barra inferior. Debe recibir una alerta en el dispositivo.

Pasos siguientes
----------------

En este sencillo ejemplo, se difunden notificaciones a todos los dispositivos de iOS. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios](/en-us/manage/services/notification-hubs/notify-users-aspnet), mientras que si desea dividir los usuarios por grupos de interés, puede leer [Uso de Centros de notificaciones para enviar noticias de último minuto](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Para obtener más información sobre el uso de los Centros de notificaciones, consulte la [información general acerca de los centros de notificaciones de Azure](http://msdn.microsoft.com/en-us/library/jj927170.aspx) y los [procedimientos de los Centros de notificaciones para iOS](http://msdn.microsoft.com/en-us/library/jj927168.aspx) (en inglés).


<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png


[105]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
[33]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png


[B102]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-02.png
[B103]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-03.png
[B104]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-04.png
[B105]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-05.png
[B106]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-06.png
[B107]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-07.png
[B108]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-08.png
[B110]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png
[B111]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png
[B9]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png
[B10]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step10.png


<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-ios
[Azure Management Portal]: https://manage.windowsazure.com/
[Notification Hubs Guidance]: http://msdn.microsoft.com/en-us/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/en-us/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Use Notification Hubs to push notifications to users]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /en-us/manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1