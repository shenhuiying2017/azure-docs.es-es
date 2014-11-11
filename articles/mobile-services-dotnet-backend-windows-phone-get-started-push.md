<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Windows Azure .Net runtime mobile services and Notification Hubs to send push notifications to your Windows phone app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc"  solutions="" writer="wesmc" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Introducción a las notificaciones de inserción en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tema muestra cómo puede usar Servicios móviles de Azure con un back-end de .NET para enviar notificaciones de inserción a una aplicación Silverlight de Windows Phone 8. Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción con Centros de notificaciones cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Actualización de la aplicación para registrarse a fin de recibir notificaciones][Actualización de la aplicación para registrarse a fin de recibir notificaciones]
2.  [Actualización del servidor para enviar notificaciones de inserción][Actualización del servidor para enviar notificaciones de inserción]
3.  [Habilitación de notificaciones de inserción para pruebas locales][Habilitación de notificaciones de inserción para pruebas locales]
4.  [Inserción de datos para recibir notificaciones de inserción][Inserción de datos para recibir notificaciones de inserción]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos] para conectar su proyecto al servicio móvil.

> [WACOM.NOTE] Este tutorial se dirige a las aplicaciones "Silverlight" de Windows Phone 8.1. Si, en cambio, está creando una aplicación de la Tienda Windows Phone 8.1, consulte la versión [Aplicación de la Tienda Windows][Aplicación de la Tienda Windows] de este tutorial. Para obtener información sobre las aplicaciones Silverlight de Windows Phone y ver una comparación con las aplicaciones de la Tienda Windows Phone, consulte [Aplicaciones Silverlight de Windows Phone 8.1][Aplicaciones Silverlight de Windows Phone 8.1].

## <span id="update-app"></span></a> Actualización de la aplicación para registrarse a fin de recibir notificaciones

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1.  En Visual Studio, abra el archivo App.xaml.cs y agregue la instrucción siguiente `using`:

        using Microsoft.Phone.Notification;

2.  Agregue el siguiente método `AcquirePushChannel` siguiente a la clase `App`:

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

3.  En el controlador de eventos `Application_Launching`, agregue la siguiente llamada al nuevo método `AcquirePushChannel`:

        AcquirePushChannel();

    Esto garantiza que se solicitará registro cada vez que se cargue la aplicación. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado.

4.  Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente con la clave de registro.

5.  En Visual Studio, abra el archivo Package.appxmanifest y asegúrese de que la opción **Capacidad de aviso** esté definida en **Sí** en la pestaña **IU de la aplicación**.

    ![][0]

    Esto asegura que la aplicación puede generar notificaciones del sistema.

## <span id="update-server"></span></a> Actualización del servidor para enviar notificaciones de inserción

1.  En el Explorador de soluciones de Visual Studio, muestre la carpeta **Controladores** en el proyecto de servicio móvil. Abra TodoItemController.cs y actualice la definición de método `PostTodoItem` con el código siguiente:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>" + item.Text + "</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Este código enviará una notificación de inserción (con el texto del elemento insertado) tras insertar un elemento todo. En caso de error, el código agregará una entrada al registro de errores que aparecerá en la pestaña **Registros** del servicio móvil en el Portal de administración.

2.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

3.  Haga clic en la pestaña **Insertar**, marque **Habilite las notificaciones de inserción no autenticadas** y, a continuación, haga clic en **Guardar**.

    ![][1]

    > [WACOM.NOTE] Este tutorial usa MPNS en modo sin autenticar. En este modo, MPNS limita el número de notificaciones que se pueden enviar a un canal de dispositivo. Para quitar esta restricción, debe generar y cargar un certificado con un clic en **Upload** y seleccionando el certificado. Para obtener más información sobre la generación del certificado, consulte [Setting up an authenticated web service to send push notifications for Windows Phone][Setting up an authenticated web service to send push notifications for Windows Phone].

Con esto se permite que el servicio móvil se conecte a MPNS en modo sin autenticar para enviar notificaciones de inserción.

## <span id="local-testing"></span></a>Habilitación de notificaciones de inserción para pruebas locales

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a>Pruebas de notificaciones de inserción en su aplicación

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

    > [WACOM.NOTE] Es posible que se produzca una excepción 401 Autorización de registro no autorizada al realizar pruebas en el emulador de Windows Phone. Esta excepción puede producirse durante la llamada `RegisterNativeAsync()` debido a la manera en el que el emulador de Windows Phone sincroniza su reloj con el equipo host. Puede resultar en un token de seguridad rechazado. Para solucionarlo, establezca manualmente el reloj del emulador antes de realizar las pruebas.

2.  En la aplicación, escriba el texto "hello push" en el cuadro de texto, haga clic en **Guardar** y luego haga clic inmediatamente en el botón Inicio o Atrás para abandonar la aplicación.

    ![][2]

    Esto envía una solicitud de inserción al servicio móvil para almacenar el elemento agregado. Observe que el dispositivo recibe una notificación del sistema que dice **hello push**.

    ![][3]

    > [WACOM.NOTE] No recibirá la notificación mientras permanezca en la aplicación. Para recibir una notificación del sistema mientras la aplicación esté activa, deberá gestionar el evento [ShellToastNotificationReceived][ShellToastNotificationReceived].

## <a name="next-steps">Pasos siguientes</a>

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de Windows Phone para usar Servicios móviles y Centros de notificaciones con el fin de usar notificaciones de inserción. A continuación, si lo desea, complete el siguiente tutorial, [Enviar notificaciones de inserción a los usuarios autenticados][Enviar notificaciones de inserción a los usuarios autenticados], en el que se muestra cómo usar las etiquetas para enviar notificaciones de inserción desde un servicio móvil solo a un usuario autenticado.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in. -->

Considere la posibilidad de profundizar más en los siguientes temas sobre Servicios móviles y Centros de notificaciones:

-   [Introducción a los datos][Introducción a los datos]
    Obtenga más información sobre cómo almacenar y consultar datos con los servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]
    Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

-   [¿Qué son los Centros de notificaciones?][¿Qué son los Centros de notificaciones?]
    Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

-   [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET]
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [Actualización de la aplicación para registrarse a fin de recibir notificaciones]: #update-app
  [Actualización del servidor para enviar notificaciones de inserción]: #update-server
  [Habilitación de notificaciones de inserción para pruebas locales]: #local-testing
  [Inserción de datos para recibir notificaciones de inserción]: #test
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Aplicación de la Tienda Windows]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Aplicaciones Silverlight de Windows Phone 8.1]: http://msdn.microsoft.com/es-es/library/windowsphone/develop/dn642082(v=vs.105).aspx
  [0]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png
  [Setting up an authenticated web service to send push notifications for Windows Phone]: http://msdn.microsoft.com/es-es/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
  [ShellToastNotificationReceived]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx
  [Enviar notificaciones de inserción a los usuarios autenticados]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users/
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library
