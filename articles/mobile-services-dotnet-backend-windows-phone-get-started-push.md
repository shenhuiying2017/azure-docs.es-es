<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Windows Azure .Net runtime mobile services and Notification Hubs to send push notifications to your Windows phone app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" writer="wesmc" manager="" editor="" />

Introducción a las notificaciones de inserción en Servicios móviles
===================================================================

[C\# para Tienda Windows](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push "C# para Tienda Windows") [JavaScript para Tienda Windows](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push "JavaScript para Tienda Windows") [Windows Phone](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push "Windows Phone")

[Back-end de .NET](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push "Back-end de .NET") | [Back-end de JavaScript](/es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/ "Back-end de JavaScript")

Este tema muestra cómo puede utilizar Servicios móviles en tiempo de ejecución .NET de Azure para enviar notificaciones de inserción a una aplicación de Windows Phone 8. Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido. Cuando haya finalizado, el servicio móvil en tiempo de ejecución .NET le enviará una notificación de inserción con los Centros de notificaciones cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Actualización de la aplicación para registrarse a fin de recibir notificaciones](#update-app)
2.  [Actualización del servidor para enviar notificaciones de inserción](#update-server)
3.  [Inserción de datos para recibir notificaciones de inserción](#test)

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles](/es-es/documentation/articles/mobile-services-windows-store-get-started) o [Introducción a los datos](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data) para conectar su proyecto al servicio móvil.

Actualización de la aplicación para registrarse a fin de recibir notificaciones
-------------------------------------------------------------------------------

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1.  En Visual Studio, abra el archivo App.xaml.cs y agregue las siguientes instrucciones `using`:

         using Microsoft.Phone.Notification;

2.  Agregue el siguiente método `AcquirePushChannel` a la clase `App`:

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
                     // Registrarse para notificaciones con el nuevo canal.
                     System.Exception exception = null;
                     try
                     {
                         await MobileService.GetPush()
                             .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                     }
                     catch (System.Exception ex)
                     {
                         CurrentChannel.Close();
                         exception = ex;
                     }
                     if (exception != null)
                     {
                         Deployment.Current.Dispatcher.BeginInvoke(() =>
                         {
                             MessageBox.Show(exception.Message, 
                                             "Registering for Push Notifications",
                                             MessageBoxButton.OK);
                         });
                     }
             });
             CurrentChannel.ShellToastNotificationReceived += 
                 new EventHandler<NotificationEventArgs>((o, args) =>
                 {
                     string message = "";
                     foreach (string key in args.Collection.Keys)
                     {
                         message += key + " : " + args.Collection[key] + ", ";
                     }
                     Deployment.Current.Dispatcher.BeginInvoke(() =>
                     {
                         MessageBox.Show(message);
                     });
             });
         }

    Este código recupera el URI de canal para la aplicación, si existe. De lo contrario, se creará. De ese modo, el URI de canal se abre y se enlaza para las notificaciones del sistema. Una vez que el URI de canal está completamente abierto, se llama al controlador del método `ChannelUriUpdated` y el canal se registra para recibir notificaciones de inserción. En caso de que se produzca un error en el registro, el canal se cierra para que las ejecuciones subsiguientes de la aplicación puedan volver a intentar el registro. El controlador `ShellToastNotificationReceived` se configura de manera tal que la aplicación pueda recibir y controlar notificaciones de inserción mientras se ejecuta.

3.  En el controlador de eventos `Application_Launching` en App.xaml.cs, agregue la siguiente llamada al nuevo método `AcquirePushChannel`:

         AcquirePushChannel();

    Esto garantiza que se solicitará registro cada vez que se cargue la aplicación. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado.

4.  Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente con la clave de registro.

5.  En Visual Studio, abra el archivo Package.appxmanifest y asegúrese de que la opción **Capacidad de aviso** esté definida en **Sí** en la pestaña **IU de la aplicación**.

      ![][1]

      Esto asegura que la aplicación puede generar notificaciones del sistema. 

Actualización del servidor para enviar notificaciones de inserción
------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

1.  Inicie sesión en el [Portal de administración de Azure](%20https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

2.  Haga clic en la pestaña **Insertar**, marque **Habilite las notificaciones de inserción no autenticadas** y, a continuación, haga clic en **Guardar**.

![](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png)

> [WACOM.NOTE] Este tutorial usa MPNS en modo sin autenticar. En este modo, MPNS limita el número de notificaciones que se pueden enviar a un canal de dispositivo. Para quitar esta restricción, debe generar y cargar un certificado con un clic en **Upload** y seleccionando el certificado. Para obtener más información sobre la generación del certificado, consulte [Setting up an authenticated web service to send push notifications for Windows Phone](http://msdn.microsoft.com/es-es/library/windowsphone/develop/ff941099(v=vs.105).aspx).

Con esto se permite que el servicio móvil se conecte a MPNS en modo sin autenticar para enviar notificaciones de inserción.

Pruebas de notificaciones de inserción en su aplicación
-------------------------------------------------------

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  En la aplicación, escriba el texto "hello push" en el cuadro de texto y, a continuación, haga clic en **Guardar**.

      ![][2]

Esto envía una solicitud de inserción al servicio móvil para almacenar el elemento agregado. Observe que la aplicación recibe una notificación del sistema que dice **hello push**.

Pasos siguientes
----------------

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Introducción a los Centros de notificaciones](/es-es/manage/services/notification-hubs/getting-started-windows-dotnet/)
    Aprenda a aprovechar Centros de notificaciones en su aplicación de la Tienda Windows.

-   [Envío de notificaciones a los suscriptores](/es-es/manage/services/notification-hubs/breaking-news-dotnet/)
    Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

-   [Envío de notificaciones a los usuarios](/es-es/manage/services/notification-hubs/notify-users/)
    Sepa cómo enviar notificaciones de inserción desde un Servicio móvil a usuarios específicos en cualquier dispositivo.

-   [Envío de notificaciones entre plataformas a los usuarios](/es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    Sepa cómo utilizar las plantillas para enviar notificaciones de inserción desde un Servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)
    Obtenga más información sobre cómo almacenar y consultar datos con los servicios móviles en tiempo de ejecución .NET.

-   [Introducción a la autenticación](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users)
    Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuenta con los servicios móviles en tiempo de ejecución .NET.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
    Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con .NET](/es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library)
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png
