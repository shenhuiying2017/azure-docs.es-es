<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

En este tema se muestra cómo puede usar los Centros de notificaciones de Azure para enviar notificaciones de inserción a aplicaciones Silverlight de Windows Phone 8 o Windows Phone 8.1. Si está interesado en Windows Phone 8.1 (no Silverlight), consulte la versión [Windows Universal][1].
Siguiendo este tutorial podrá crear una aplicación de Windows Phone 8 vacía que reciba notificaciones de inserción mediante el servicio de notificaciones de inserción de Microsoft (MPNS). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su centro de notificaciones.

> [AZURE.NOTE] El SDK de Windows Phone para Centros de notificaciones no soporta el uso de WNS con aplicaciones Silverlight de Windows Phone 8.1. Para usar WNS (en lugar de MPNS) con aplicaciones Silverlight de Windows Phone 8.1, tiene que configurar sus credenciales WNS como se muestra en [Get Started for Windows Universal][1] (Introducción a Windows Universal). Después, podrá registrarse desde el back-end, como se muestra en el tutorial [Notificación a usuarios][], o usar las [API de REST de Centros de notificaciones][].

Este tutorial le guiará a través de los siguientes pasos para habilitar las notificaciones de inserción:

1.  [Creación de su centro de notificaciones][]
2.  [Conexión de su aplicación al centro de notificaciones][]
3.  [Envío de notificaciones desde su backend][]

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para aprender a usar los centros de notificaciones para abordar usuarios y grupos de dispositivos específicos. Este tutorial requiere lo siguiente:

-   [Visual Studio 2012 Express para Windows Phone][], o una versión posterior.

Completar este tutorial es un requisito previo para todos los demás tutoriales de centros de notificaciones para aplicaciones de Windows Phone 8.

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

## <a name="configure-hub"></a>Creación de su centro de notificaciones

1.  Inicie sesión en el [Portal de administración de Azure][] y, a continuación, haga clic en **+NEW** en la parte inferior de la pantalla.

2.  Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y finalmente en **Quick Create**.

    ![][]

3.  Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Create a new Notification Hub**.

    ![][2]

4.  Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, a continuación, haga clic en la pestaña **Configure** en la parte superior.

    ![][3]

5.  Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

    ![][4]

6.  Haga clic en **Connection Information** en la parte inferior. Anote las dos cadenas de conexión.

    ![][5]

7.  Haga clic en la pestaña **Configure** y, a continuación, active la casilla **Enable unauthenticated push notifications** en la sección **Windows Phone notifications settings**.

    ![][6]

Ahora tiene las cadenas de conexión necesarias para registrar su aplicación de Windows Phone 8 y enviar notificaciones.

<div class="dev-callout"><b>Nota:</b>
        <p>Este tutorial usa MPNS en modo sin autenticar. El modo sin autenticar de MPNS viene con restricciones sobre las notificaciones que puede enviar a cada canal. Los Centros de notificaciones admiten el <a href="http://msdn.microsoft.com/es-es/library/windowsphone/develop/ff941099(v=vs.105).aspx">modo sin autenticar de MPNS</a> (en ingl&eacute;s). <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

## <a name="connecting-app"></a>Conexión de su aplicación al centro de notificaciones

1.  En Visual Studio, cree una aplicación nueva de Windows Phone 8.

    ![][7]

    En Visual Studio 2013 Update 2 o versiones posteriores, cree en su lugar una aplicación Silverlight de Windows Phone.

    ![][8]

2.  En Visual Studio, haga clic con el botón secundario en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**.

    Esto muestra el cuadro de diálogo Administrar paquetes de NuGet.

3.  Busque `WindowsAzure.Messaging.Managed` y haga clic en **Instalar**, seleccione todos los proyectos en la solución y acepte los términos de uso.

    ![][9]

    Esto descarga, instala y agrega una referencia a todos los proyectos de la biblioteca de Mensajería de Azure para Windows usando el [paquete de NuGet WindowsAzure.Messaging.Managed][].

4.  Abra el archivo App.xaml.cs y agregue las siguientes instrucciones `using`:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5.  En el siguiente código en la parte superior del método **Application\_Launching** en App.xaml.cs:

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

    > [WACOM.NOTE]Este tutorial envía una notificación del sistema al dispositivo. Cuando envía una notificación de icono, debe llamar al método **BindToShellTile** en el canal. Para admitir notificaciones del sistema y notificaciones de icono, llame a ambos métodos, **BindToShellTile** y **BindToShellToast**.

6.  En el Explorador de soluciones, expanda **Propiedades**, abra el archivo WMAppManifest.xml, haga clic en la pestaña **Funcionalidades** y asegúrese de que la funcionalidad \*\*ID\_**CAP**\_PUSH\_NOTIFICATION\*\* esté marcada.

    ![][10]

    Esto asegura que la aplicación pueda recibir notificaciones de inserción.

7.  Presione la tecla F5 para ejecutar la aplicación.

    Se muestra un mensaje de registro.

## <a name="send"></a>Envíe notificaciones desde su backend

Puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end que use la [interfaz REST][]. En este tutorial puede enviar notificaciones con una aplicación de consola .NET. Para ver un ejemplo de cómo enviar notificaciones desde un backend de los Servicios móviles de Azure integrado en centros de notificaciones, vea **Introducción a las notificaciones de inserción en Servicios móviles** ([.NET backend][] | [JavaScript backend][.NET backend]). Para ver un ejemplo de cómo enviar notificaciones con REST API, vea **Uso de Centro de notificaciones desde Java/PHP** ([Java][] | [PHP][]).

1.  Haga clic con el botón secundario en la solución, seleccione **Agregar** y **Nuevo proyecto...**, a continuación en **Visual C#** haga clic en **Windows** y **Aplicación de consola** y haga clic en **OK**.

    ![][11]

    Esto agrega una aplicación de consola nueva de Visual C# a la solución. También puede hacer esto en una solución separada.

2.  Haga clic con el menú secundario en **Herramientas**, luego en **Administrador de paquetes de bibliotecav** y finalmente en **Consola del Administrador de paquetes**.

    Esto muestra la Consola del Administrador de paquetes.

3.  En la ventana de la consola, ajuste **Proyecto predeterminado** en su nuevo proyecto de aplicación de consola y después ejecute el siguiente comando en la ventana de la consola:

        Install-Package WindowsAzure.ServiceBus

    Esto agrega una referencia al SDK de Bus de servicio de Azure con el [paquete WindowsAzure.ServiceBus NuGet][].

4.  Abra el archivo Program.cs y agregue la siguiente instrucción `using`:

        using Microsoft.ServiceBus.Notifications;

5.  En la clase **Program**, agregue el siguiente método:

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

    > [WACOM.NOTE]Asegúrese de utilizar la cadena de conexión con acceso **Total**, no con acceso **Escuchar**. La cadena de acceso escuchar no tiene permisos para enviar notificaciones.

6.  Posteriormente, agregue la siguiente línea al método Main:

         SendNotificationAsync();
         Console.ReadLine();

7.  Con el emulador de Windows Phone en ejecución y su aplicación cerrada, establezca el proyecto de aplicación de consola como el proyecto de inicio predeterminado y, a continuación, presione la tecla F5 para ejecutar la aplicación.

    Recibirá una notificación del sistema. Al pulsar en el mensaje emergente, se carga la aplicación.

Puede encontrar todas las cargas posibles en el [catálogo de notificaciones del sistema][] y en los temas de [catálogo de iconos][] en MSDN.

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, difunde notificaciones a todos los dispositivos con Windows Phone 8. Para abordar usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios][]. Si desea segmentar a sus usuarios por grupos de interés, puede leer [Uso de Centros de notificaciones para enviar noticias de último minuto][]. Obtenga más información sobre el uso de Centros de notificaciones en la [orientación sobre los Centros de notificaciones][] (en inglés).


  [Windows Universal]: /es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /es-es/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /es-es/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /es-es/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /es-es/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [1]: /es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  [Notificación a usuarios]: /es-es/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
  [API de REST de Centros de notificaciones]: http://msdn.microsoft.com/es-es/library/dn223264.aspx
  [Creación de su centro de notificaciones]: #configure-hub
  [Conexión de su aplicación al centro de notificaciones]: #connecting-app
  [Envío de notificaciones desde su backend]: #send
  [Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  []: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
  [6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
  [modo sin autenticar de MPNS]: http://msdn.microsoft.com/es-es/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
  [8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
  [9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
  [paquete de NuGet WindowsAzure.Messaging.Managed]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [10]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
  [interfaz REST]: http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx
  [.NET backend]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Java]: /es-es/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /es-es/documentation/articles/notification-hubs-php-backend-how-to/
  [11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
  [paquete WindowsAzure.ServiceBus NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [catálogo de notificaciones del sistema]: http://msdn.microsoft.com/es-es/library/windowsphone/develop/jj662938(v=vs.105).aspx
  [catálogo de iconos]: http://msdn.microsoft.com/es-es/library/windowsphone/develop/hh202948(v=vs.105).aspx
  [Uso de Centros de notificaciones para insertar notificaciones en los usuarios]: /es-es/manage/services/notification-hubs/notify-users-aspnet
  [Uso de Centros de notificaciones para enviar noticias de último minuto]: /es-es/manage/services/notification-hubs/breaking-news-dotnet
  [orientación sobre los Centros de notificaciones]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
