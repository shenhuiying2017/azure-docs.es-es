<properties
	pageTitle="Use Servicios móviles de Azure y Centros de notificaciones para enviar notificaciones push a la aplicación de Windows Phone."
	description="Obtenga información acerca de cómo usar Centros de notificaciones con Servicios móviles de Azure y para enviar notificaciones de inserción a la aplicación de Windows Phone."
	services="mobile-services,notification-hubs"
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
	ms.date="09/28/2015" 
	ms.author="wesmc"/>

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../../includes/mobile-services-selector-get-started-push-legacy.md)]

##Información general

Este tema muestra cómo puede usar Servicios móviles de Azure con un back-end .NET para enviar notificaciones push a una aplicación de Windows Phone Silverlight 8 usando el Servicio de notificaciones de inserción de Microsoft (MPNS). Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción con Centros de notificaciones cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

Este tutorial está basado en el proyecto TodoList de Servicios móviles. Antes de iniciar este tutorial, primero debe completar [Incorporación de Servicios móviles a una aplicación existente] para conectar su proyecto al servicio móvil.

>[AZURE.NOTE]Este tutorial se dirige a las aplicaciones Silverlight de Windows Phone 8.1. Si, en cambio, está creando una aplicación de la Tienda Windows Phone 8.1, consulte la versión [Aplicación de la Tienda Windows](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) de este tutorial. Para obtener información sobre las aplicaciones Silverlight de Windows Phone y ver una comparación con las aplicaciones de la Tienda Windows Phone, vea [Aplicaciones Silverlight de Windows Phone 8.1].

##Actualización de la aplicación para registrarse a fin de recibir notificaciones

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1. En Visual Studio, abra el archivo App.xaml.cs y agregue la instrucción siguiente `using`:

        using Microsoft.Phone.Notification;

2. Agregue el siguiente método `AcquirePushChannel` a la clase `App`:

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

4. En el controlador de eventos `Application_Launching` en App.xaml.cs, agregue la siguiente llamada al nuevo método `AcquirePushChannel`:

        AcquirePushChannel();

	Esto garantiza que se solicitará registro cada vez que se cargue la aplicación. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado.

5. Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente con la clave de registro.

6. En Visual Studio, abra el archivo Package.appxmanifest y asegúrese de que la opción **Capacidad de aviso** esté definida en **Sí** en la pestaña **IU de la aplicación**.

   	![][1]

   	Esto asegura que la aplicación puede generar notificaciones del sistema.

##Actualización del servidor para enviar notificaciones push

1. En el Explorador de soluciones de Visual Studio, muestre la carpeta **Controladores** en el proyecto de servicio móvil. Abra TodoItemController.cs y actualice la definición del método `PostTodoItem` con el siguiente código:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
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

2. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en la aplicación.

3. Haga clic en la pestaña **Inserción**, marque **Habilite las notificaciones de inserción no autenticadas** y, a continuación, haga clic en **Guardar**.

   	![Pestaña Insertar de Servicios móviles](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png)

	>[AZURE.NOTE]Este tutorial usa MPNS en modo sin autenticar. En este modo, MPNS limita el número de notificaciones que se pueden enviar a un canal de dispositivo. Para quitar esta restricción, debe generar y cargar un certificado haciendo clic en <strong>Upload</strong> (Cargar) y seleccionando el certificado. Para obtener más información sobre la generación del certificado, consulte <a href="http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx">Configuración de un servicio web autenticado para enviar notificaciones de inserción para Windows Phone</a>.

Con esto se permite que el servicio móvil se conecte a MPNS en modo sin autenticar para enviar notificaciones de inserción.

##Habilitación de notificaciones de inserción para pruebas locales

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]


##Prueba de las notificaciones push en su aplicación

1. En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

    >[AZURE.NOTE]Es posible que se produzca una excepción 401 Unauthorized RegistrationAuthorizationException al realizar pruebas en el emulador de Windows Phone. Esta excepción puede producirse durante la llamada `RegisterNativeAsync()` debido a la manera en el que el emulador de Windows Phone sincroniza su reloj con el equipo host. Puede resultar en un token de seguridad rechazado. Para solucionarlo, establezca manualmente el reloj del emulador antes de realizar las pruebas.

5. En la aplicación, escriba el texto "hello push" en el cuadro de texto, haga clic en **Guardar** y luego haga clic inmediatamente en el botón Inicio o Atrás para abandonar la aplicación.

   	![Insertar texto][2]

  	Esto envía una solicitud de inserción al servicio móvil para almacenar el elemento agregado. Observe que el dispositivo recibe una notificación del sistema que dice **hello push**.

	![Notificación push recibida](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]No recibirá la notificación mientras permanezca en la aplicación. Para recibir una notificación del sistema mientras la aplicación esté activa, deberá gestionar el evento [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx).

##Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de Windows Phone para usar Servicios móviles y Centros de notificaciones con el fin de usar notificaciones de inserción. Le recomendamos que después realice el siguiente tutorial, [Envío de notificaciones de inserción a usuarios autenticados], que muestra cómo enviar estas notificaciones con etiquetas desde un servicio móvil a solo un usuario autenticado.

Considere la posibilidad de profundizar más en los siguientes temas sobre Servicios móviles y Centros de notificaciones:

* [Adición de autenticación a la aplicación] <br/>Obtenga información sobre cómo autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

* [¿Qué son los Centros de notificaciones?] <br/>Obtenga más información sobre el funcionamiento de los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas de cliente.

* [Depuración de aplicaciones de centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenga instrucciones de resolución de problemas y depuración de soluciones de centros de notificaciones.

* [Referencia conceptual de Servicios móviles con .NET] <br/>Obtenga más información sobre cómo utilizar Servicios móviles con .NET.



<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png

<!-- URLs. -->
[Incorporación de Servicios móviles a una aplicación existente]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[Adición de autenticación a la aplicación]: mobile-services-dotnet-backend-windows-phone-get-started-users.md

[Envío de notificaciones de inserción a usuarios autenticados]: mobile-services-dotnet-backend-windows-phone-push-notifications-app-users.md

[¿Qué son los Centros de notificaciones?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-windows-phone-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-windows-phone-send-localized-breaking-news.md


[Referencia conceptual de Servicios móviles con .NET]: mobile-services-html-how-to-use-client-library.md
[Aplicaciones Silverlight de Windows Phone 8.1]: http://msdn.microsoft.com/library/windowsphone/develop/dn642082(v=vs.105).aspx
[Portal de administración de Azure]: https://manage.windowsazure.com/

<!---HONumber=Oct15_HO1-->