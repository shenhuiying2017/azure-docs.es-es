<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/24/2014" ms.author="krisragh" />

# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación iOS.
En este tutorial, creará una aplicación iOS que recibirá notificaciones de inserción con el servicio de notificación de inserción de Apple (APNS). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su centro de notificaciones.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Generación del archivo de solicitud de firma de certificado][Generación del archivo de solicitud de firma de certificado]
2.  [Registro de la aplicación y habilitación para las notificaciones de inserción][Registro de la aplicación y habilitación para las notificaciones de inserción]
3.  [Creación de un perfil de aprovisionamiento para la aplicación][Creación de un perfil de aprovisionamiento para la aplicación]
4.  [Configuración de su Centro de notificaciones][Configuración de su Centro de notificaciones]
5.  [Conexión de su aplicación al centro de notificaciones][Conexión de su aplicación al centro de notificaciones]
6.  [Envío de notificaciones desde su backend][Envío de notificaciones desde su backend]

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para aprender a usar los centros de notificaciones para abordar usuarios y grupos de dispositivos específicos. Este tutorial cuenta con los siguientes requisitos previos:

-   [SDK de iOS para Servicios móviles][SDK de iOS para Servicios móviles]
-   [XCode 4,5][XCode 4,5]
-   Dispositivo compatible con iOS 5.0 (o una versión posterior)
-   Pertenencia al programa para desarrolladores de iOS

<div class="dev-callout"><b>Nota:</b><br /> <p>Debido a los requisitos de la configuraci&oacute;n de las notificaciones de inserci&oacute;n, debe implementar y realizar una prueba de las notificaciones de inserci&oacute;n en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.</p><br /> </div>

Completar este tutorial es un requisito previo para todos los tutoriales de Centros de notificaciones para aplicaciones iOS.

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

[WACOM.INCLUDE [Habilitar notificaciones de inserción de Apple](../includes/enable-apple-push-notifications.md)]

## <a name="configure-hub"></a>Configuración de su Centro de notificaciones

1.  En Acceso a llaves, haga clic con el botón secundario en el nuevo certificado de la aplicación de inicio rápido **My Certificates**. Haga clic en **Export**, utilice un nombre para el archivo, seleccione el formato **.p12** y, a continuación, haga clic en **Save**.

    ![][0]

Anote el nombre de archivo y la ubicación del certificado exportado.

> [WACOM.NOTE] Con este tutorial se crea un archivo QuickStart.p12. El nombre del archivo y la ubicación pueden ser diferentes.

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure] y haga clic en **+NEW** en la parte inferior de la pantalla.

2.  Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y finalmente en **Quick Create**.

    ![][1]

3.  Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Create a new Notification Hub**.

    ![][2]

4.  Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, a continuación, haga clic en la pestaña **Configure** en la parte superior.

    ![][3]

5.  Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

    ![][4]

6.  Seleccione la pestaña **Configure** en la parte superior y, a continuación, haga clic en **Upload** para la configuración de notificaciones de Apple. A continuación, seleccione el certificado **.p12** que exportó anteriormente y la contraseña para el certificado. Asegúrese de seleccionar si desea usar el servicio de inserción **Production** (si desea enviar notificaciones de inserción a usuarios que adquirieron la aplicación desde la tienda) o **Sandbox** (durante el desarrollo).

    ![][5]

7.  Haga clic en la pestaña **Panel** en la parte superior y, a continuación, haga clic en **Connection Information**. Anote las dos cadenas de conexión.

    ![][6]

Su centro de notificaciones está ahora configurado para funcionar con APN y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones.

## <a name="connecting-app"></a>Conexión de su aplicación al centro de notificaciones

1.  En XCode, cree un nuevo proyecto iOS y seleccione la plantilla **Single View Application**.

    ![][7]

2.  En **Targets**, haga clic en el nombre de proyecto y, a continuación, expanda **Code Signing Identity** y, a continuación, en **Debug**, seleccione el perfil de identidad de firma de código. Además, si usa una versión más reciente de XCode, cambie **Levels** de **Basic** a **All** y establezca el elemento de línea **Provisioning Profile** en el perfil de aprovisionamiento.

    ![][8]

3.  Descargue Azure Mobile SDK para iOS. Abra el archivo .zip y arrastre la carpeta WindowsAzureMessaging.framework a la carpeta Framework en el proyecto XCode. Seleccione **Copy items in destination group's folder** y, a continuación, haga clic en **OK**.

    ![][9]

4.  En el archivo AppDelegate.h, agregue la siguiente directiva de importación:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  En su archivo AppDelegate.m, agregue el siguiente código al método `didFinishLaunchingWithOptions`:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  En el mismo archivo, agregue el siguiente método:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
                                      @"<connection string>" notificationHubPath:@"mynh"];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
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

## <a name="send"></a>Envíe notificaciones desde su backend

Puede enviar notificaciones mediante los centros de notificaciones desde cualquier back-end que use la [interfaz REST][interfaz REST]. En este tutorial puede enviar notificaciones con una aplicación de consola .NET. Para ver un ejemplo de cómo enviar notificaciones desde un backend de los Servicios móviles de Azure integrado en centros de notificaciones, vea **Introducción a las notificaciones de inserción en Servicios móviles** ([.NET backend][.NET backend] | [JavaScript backend][.NET backend]). Para ver un ejemplo de cómo enviar notificaciones con REST API, vea **Uso de Centro de notificaciones desde Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  En Visual Studio, en el menú **Archivo**, seleccione **Nuevo** y luego **Proyecto...**, a continuación en **Visual C#** haga clic en **Windows** y **Aplicación de consola** y haga clic en **Aceptar**.

    [][20]

    Esto crea un nuevo proyecto de aplicación de consola.

2.  En el menú **Tools**, haga clic en **Library Package Manager** y, a continuación, en **Package Manager Console**.

    Esto muestra la Consola del Administrador de paquetes.

3.  En la ventana de la consola, ejecute el siguiente comando:

        Install-Package WindowsAzure.ServiceBus

    Esto agrega una referencia al SDK de Bus de servicio de Azure con el [paquete WindowsAzure.ServiceBus NuGet][paquete WindowsAzure.ServiceBus NuGet].

4.  Abra el archivo Program.cs y agregue la siguiente instrucción `using`:

        using Microsoft.ServiceBus.Notifications;

5.  En la clase **Program**, agregue el siguiente método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

    Asegúrese de reemplazar el marcador de posición "nombre de centro" por nombre del centro de notificaciones que aparece en el portal en la pestaña **Centros de notificaciones**. Sustituya también el marcador de la cadena de conexión por la cadena de conexión llamada **DefaultListenSharedAccessSignature** que obtuvo en la sección "Configurar el centro de notificaciones".

    > [WACOM.NOTE]Asegúrese de utilizar la cadena de conexión con acceso **Total**, no con acceso **Escuchar**. La cadena de acceso escuchar no tiene permisos para enviar notificaciones.

6.  Agregue las siguientes líneas al método **Main**:

         SendNotificationAsync();
         Console.ReadLine();

7.  Presione la tecla F5 para ejecutar la aplicación de consola.

    Debe recibir una alerta en el dispositivo. Si usa Wi-Fi, asegúrese de que la conexión funciona.

Puede buscar todas las cargas posibles en la guía [Local and Push Notification Programming Guide][Local and Push Notification Programming Guide] de Apple.

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, se difunden notificaciones a todos los dispositivos iOS. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios][Uso de Centros de notificaciones para insertar notificaciones en los usuarios], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora][Uso de los Centros de notificaciones para enviar noticias de última hora]. Obtenga más información sobre el uso de Centros de notificaciones en la [orientación sobre los Centros de notificaciones][orientación sobre los Centros de notificaciones] (en inglés).


  [Generación del archivo de solicitud de firma de certificado]: #certificates
  [Registro de la aplicación y habilitación para las notificaciones de inserción]: #register
  [Creación de un perfil de aprovisionamiento para la aplicación]: #profile
  [Configuración de su Centro de notificaciones]: #configure-hub
  [Conexión de su aplicación al centro de notificaciones]: #connecting-app
  [Envío de notificaciones desde su backend]: #send
  [SDK de iOS para Servicios móviles]: http://go.microsoft.com/fwLink/?LinkID=266533
  [XCode 4,5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [0]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [1]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [6]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [7]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [8]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
  [9]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png
  [interfaz REST]: http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx
  [.NET backend]: /es-es/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Java]: /es-es/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /es-es/documentation/articles/notification-hubs-php-backend-how-to/
  [paquete WindowsAzure.ServiceBus NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [Uso de Centros de notificaciones para insertar notificaciones en los usuarios]: /es-es/manage/services/notification-hubs/notify-users-aspnet
  [Uso de los Centros de notificaciones para enviar noticias de última hora]: /es-es/manage/services/notification-hubs/breaking-news-dotnet
  [orientación sobre los Centros de notificaciones]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
