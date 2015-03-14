<properties pageTitle="Introducción a los Centros de notificaciones de Azure" description="Aprenda a usar los Centros de notificaciones de Azure para las notificaciones de inserción." services="notification-hubs" documentationCenter="ios" authors="ysxu" manager="dwrede" editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="hero-article" 
	ms.date="10/10/2014" 
	ms.author="yuaxu"/>

# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación iOS.
En este tutorial puede crear una aplicación iOS vacía que recibe notificaciones de inserción mediante el servicio de notificaciones de inserción de Apple (APN). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su Centro de notificaciones.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Generación del archivo de solicitud de firma de certificado]
2. [Registro de la aplicación y habilitación para las notificaciones de inserción]
3. [Creación de un perfil de aprovisionamiento para la aplicación]
4. [Configuración del Centro de notificaciones]
5. [Conexión de la aplicación al Centro de notificaciones]
6. [Envío de notificaciones desde el back-end]

En este tutorial se demuestra el escenario de difusión sencillo con Centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para aprender a usar los Centros de notificaciones para abordar usuarios y grupos de dispositivos específicos. Este tutorial requiere los siguientes requisitos previos:

+ [SDK de iOS para Servicios móviles]
+ [XCode 4.5][Instalación de Xcode]
+ Dispositivo compatible con iOS 5.0 (o una versión posterior)
+ Pertenencia al programa para desarrolladores de iOS

   > [AZURE.NOTE] Debido a los requisitos de la configuración de las notificaciones de inserción, debe implementar y realizar una prueba de las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

Completar este tutorial es un requisito previo para todos los tutoriales de Centros de notificaciones para aplicaciones iOS.

> [AZURE.NOTE] para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20target="_blank).

[AZURE.INCLUDE [Habilitación de notificaciones de inserción de Apple](../includes/enable-apple-push-notifications.md)]

##<a name="configure-hub"></a>Configuración del Centro de notificaciones

1. En Acceso a llaves, haga clic con el botón derecho en el nuevo certificado de la aplicación de inicio rápido **My Certificates** (Mis certificados). Haga clic en **Export** (Exportar), dé un nombre al archivo, seleccione el formato **.p12** y, a continuación, haga clic en **Save** (Guardar).

    ![][26]

  Anote el nombre de archivo y la ubicación del certificado exportado.

>[AZURE.NOTE] Con este tutorial se crea un archivo QuickStart.p12. El nombre del archivo y la ubicación pueden ser diferentes.

2. Inicie sesión en el [Portal de administración de Azure] y luego haga clic en **+NUEVO** en la parte inferior de la pantalla.

3. Haga clic en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y, a continuación, en **Creación rápida**.

   	![][27]

4. Escriba un nombre para su Centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Crear un nuevo Centro de notificaciones**.

   	![][28]

5. Haga clic en el espacio de nombres que acaba de crear (por lo general ***nombre del Centro de notificaciones*-ns**) y, a continuación, haga clic en **Configurar** en la parte superior.

   	![][29]

6. Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el Centro de notificaciones que acaba de crear.

   	![][210]

7. Seleccione la pestaña **Configurar** en la parte superior y luego haga clic en **Cargar** para la configuración de notificaciones de Apple. A continuación, seleccione el certificado **.p12** que exportó anteriormente y la contraseña para el certificado. Asegúrese de seleccionar si desea usar el servicio de inserción **Production** (si desea enviar notificaciones de inserción a usuarios que adquirieron la aplicación desde la tienda) o **Sandbox** (durante el desarrollo).

   	![][211]

8. Haga clic en la pestaña **Panel** en la parte superior y luego haga clic en **Información de conexión**. Anote las dos cadenas de conexión.

   	![][212]

Su Centro de notificaciones está ahora configurado para funcionar con APN y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones.

##<a name="connecting-app"></a>Conexión de la aplicación al Centro de notificaciones

1. En XCode, cree un nuevo proyecto iOS y seleccione la plantilla **Single View Application** (Aplicación de vista sencilla).

   	![][31]

2. En **Targets** (Destinos), haga clic en el nombre de proyecto y, a continuación, expanda **Code Signing Identity** (Identidad de firma de código), luego en **Debug** (Depurar), seleccione el perfil de identidad de firma de código. Además, si usa una versión más reciente de XCode, cambie **Levels** (Niveles) de **Basic** (Básico) a **All** (Todos) y establezca la línea **Provisioning Profile** (Perfil de aprovisionamiento) en el perfil de aprovisionamiento.

   	![][32]

3. Descargue Azure Mobile SDK para iOS. Abra el archivo .zip y arrastre la carpeta WindowsAzureMessaging.framework a la carpeta Framework en el proyecto XCode. Seleccione **Copy items in destination group's folder** (Copiar elementos en la carpeta del grupo de destino) y, a continuación, haga clic en **OK** (Aceptar).

   	![][33]

4. En el archivo AppDelegate.h, agregue la siguiente directiva de importación:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5. En su archivo AppDelegate.m, agregue el siguiente código al método `didFinishLaunchingWithOptions`:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6. En el mismo archivo, agregue el siguiente método:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
		                              @"<connection string>" notificationHubPath:@"mynh"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
	    	}];
		}

7. *(opcional)* De nuevo, en el mismo archivo, agregue el siguiente método para ver un **UIAlert** si la notificación se recibió cuando la aplicación estaba activa:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		    @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

8. Ejecute la aplicación en el dispositivo.

##<a name="send"></a>Envío de notificaciones desde el back-end

Puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end que use la <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz REST</a>. En este tutorial puede enviar notificaciones con una aplicación de consola .NET. Para ver un ejemplo de cómo enviar notificaciones desde un back-end de los Servicios móviles de Azure integrado en Centros de notificaciones, consulte **Introducción a las notificaciones de inserción en Servicios móviles** (back-end de [.NET](/es-es/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/) | [back-end de JavaScript](/es-es/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/)).  Para obtener un ejemplo de cómo enviar notificaciones mediante las API de REST, consulte **Uso de los Centros de notificaciones de Java y PHP** ([Java](/es-es/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/es-es/documentation/articles/notification-hubs-php-backend-how-to/)).

1. En Visual Studio, en el menú **Archivo**, seleccione **Nuevo** y luego **Proyecto...**, a continuación en **Visual C#** haga clic en **Windows** y **Aplicación de consola** y haga clic en **Aceptar**.  

   	![][20]

	Esto crea un nuevo proyecto de aplicación de consola.

2. En el menú **Herramientas**, haga clic en **Administrador de paquetes de biblioteca** y, a continuación, en **Consola del Administrador de paquetes**.

	Esto muestra la Consola del Administrador de paquetes.

6. En la ventana de la consola, ejecute el siguiente comando:

        Install-Package WindowsAzure.ServiceBus

	Esta acción agrega una referencia al SDK de Bus de servicio de Azure con el <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">paquete WindowsAzure.ServiceBus de NuGet</a>.

5. Abra el archivo Program.cs y agregue la siguiente instrucción  `using`:

        using Microsoft.ServiceBus.Notifications;

6. En la clase **Program**, agregue el siguiente método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

	Asegúrese de reemplazar el marcador de posición "nombre de centro" por el nombre del Centro de notificaciones que aparece en el portal en la pestaña **Centros de notificaciones**. Reemplace también el marcador de posición de la cadena de conexión por la cadena de conexión llamada **DefaultFullSharedAccessSignature** que obtuvo en la sección "Configuración del Centro de notificaciones".

	>[AZURE.NOTE]Asegúrese de utilizar la cadena de conexión con acceso **Total**, no con acceso de **Escucha**. La cadena de acceso escuchar no tiene permisos para enviar notificaciones.

5. Después agregue las siguientes líneas al método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

5. Presione la tecla F5 para ejecutar la aplicación de consola.

	Debe recibir una alerta en el dispositivo. Si usa Wi-Fi, asegúrese de que la conexión funciona.

Puede buscar todas las cargas posibles en la guía [de programación de notificaciones de inserción y local] de Apple.

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, se difunden notificaciones a todos los dispositivos iOS. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora]. Obtenga más información sobre el uso de Centros de notificaciones en la [Orientación sobre los Centros de notificaciones].

<!-- Anchors. -->
[Generación del archivo de solicitud de firma de certificado]: #certificates
[Registro de la aplicación y habilitación para las notificaciones de inserción]: #register
[Creación de un perfil de aprovisionamiento para la aplicación]: #profile
[Configuración del Centro de notificaciones]: #configure-hub
[Conexión de la aplicación al Centro de notificaciones]: #connecting-app
[Envío de notificaciones desde el back-end]: #send
[Pasos siguientes]:#next-steps

<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[26]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png


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
[SDK de iOS para Servicios móviles]: http://go.microsoft.com/fwLink/?LinkID=266533
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-ios
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Orientación sobre los Centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx
[Instalar Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de aprovisionamiento de iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Uso de los Centros de notificaciones para insertar notificaciones en los usuarios]: /es-es/manage/services/notification-hubs/notify-users-aspnet
[Uso de los Centros de notificaciones para enviar noticias de última hora]: /es-es/manage/services/notification-hubs/breaking-news-dotnet

[Guía de programación de notificaciones de inserción y local]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1

<!--HONumber=45--> 
