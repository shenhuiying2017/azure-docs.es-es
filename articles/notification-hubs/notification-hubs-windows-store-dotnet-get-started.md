<properties
	pageTitle="Introducción a los Centros de notificaciones de Azure | Microsoft Azure"
	description="En este tutorial aprende a usar Centros de notificaciones de Azure para enviar notificaciones push a una aplicación de la Tienda Windows o de Windows Phone 8.1 (no Silverlight)."
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor="dwrede"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/24/2015"
	ms.author="wesmc"/>

# Introducción a los Centros de notificaciones

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Información general

En este tutorial se muestra cómo se pueden utilizar los Centros de notificaciones de Azure para enviar notificaciones push a una aplicación de la Tienda Windows o de Windows Phone 8.1 (no Silverlight). Si su objetivo es Silverlight para Windows Phone 8.1, consulte la versión de [Windows Phone](notification-hubs-windows-phone-get-started.md). En este tutorial puede crear una aplicación de la Tienda Windows vacía que recibe notificaciones push mediante el Servicios de notificaciones de inserción de Windows (WNS). Cuando haya finalizado, podrá usar el centro de notificaciones para difundir notificaciones push a todos los dispositivos que ejecutan su aplicación.

En este tutorial se demuestra un escenario de difusión sencillo con Centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para aprender a usar los Centros de notificaciones para abordar usuarios y grupos de dispositivos específicos.


##Requisitos previos

Este tutorial requiere lo siguiente:

+ Microsoft Visual Studio Express 2013 para Windows con la actualización 2<br/>Esta versión de Visual Studio es necesaria para crear un proyecto de aplicación universal. Si solo desea crear un aplicación para la Tienda Windows, necesita Visual Studio 2012 Express para Windows 8.

+ Una cuenta de la Tienda Windows activa

+ Una cuenta activa de Azure<br/>En caso de no tener cuenta, puede crear una de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).

Completar este tutorial es un requisito previo para todos los tutoriales de Centros de notificaciones para aplicaciones de la Tienda Windows.

##Registro de la aplicación para la Tienda Windows

Para enviar notificaciones push a las aplicaciones de la Tienda Windows, debe asociar su aplicación a la Tienda Windows. A continuación, debe configurar su Centro de notificaciones para que se integre con WNS.

1. Si aún no ha registrado la aplicación, vaya al <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Centro de desarrollo de Windows</a>, inicie sesión en su cuenta Microsoft y, a continuación, haga clic en **Crear una nueva aplicación**.


2. Escriba un nombre para la aplicación y haga clic en **Reservar nombre de aplicación**.

   	![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-name.png)

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

7. (Opcional) Repita los pasos 4–6 para el proyecto de la aplicación de la Tienda de Windows Phone.

8. De nuevo en la página Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Servicios**, **Notificaciones push**, **Sitio de Servicios Live** en **Servicios de notificaciones de inserción de Windows (WNS) y Servicios móviles de Microsoft Azure**.

   	![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-live-services.png)

9. En la pestaña **Configuración de aplicaciones**, anote los valores de **Secreto del cliente** e **Identificador de seguridad de paquete (SID)**.

   	![][6]

 	> [AZURE.WARNING]El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No los comparta con nadie ni los distribuya con su aplicación.

##Configuración de su Centro de notificaciones

1. Inicie sesión en el [Portal de Azure] y, luego, haga clic en **NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, haga clic en **Bus de servicio**, haga clic en **Centro de notificaciones** y, luego, haga clic en **Creación rápida**.

   	![][7]

3. Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, luego, haga clic en **Crear una nueva base de datos central de notificaciones**.

   	![][8]

4. Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, luego, haga clic en la pestaña **Configurar** en la parte superior.

   	![][9]

5. Seleccione la pestaña **Centros de notificaciones** en la parte superior y, luego, haga clic en el centro de notificaciones que acaba de crear.

   	![][10]

6. Seleccione la pestaña **Configurar** en la parte superior, escriba los valores de **Secreto del cliente** y **SID del paquete** obtenidos de WNS en la sección anterior y, a continuación, haga clic en **Guardar**.

   	![][11]

7. Seleccione la pestaña **Panel** de la parte superior y, después, haga clic en el botón **Información de conexión** situado en la parte inferior de la página. Anote las dos cadenas de conexión.

   	![][12]

Su Centro de notificaciones está ahora configurado para funcionar con WNS y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones.

##Conexión de la aplicación al Centro de notificaciones

1. En Visual Studio, haga clic con el botón derecho en la solución y, luego, haga clic en **Administrar paquetes de NuGet**.

	De este modo aparece el cuadro de diálogo **Administrar paquetes de NuGet**.

2. Busque `WindowsAzure.Messaging.Managed` y haga clic en **Instalar**, seleccione todos los proyectos de la solución y acepte los términos de uso.

	![][20]

	Esta acción descarga, instala y agrega una referencia a todos los proyectos de la biblioteca de mensajería de Azure para Windows usando el <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">paquete de NuGet WindowsAzure.Messaging.Managed</a>.

3. Abra el archivo de proyecto App.xaml.cs y agregue las siguientes instrucciones `using`. Este proyecto es universal, este archivo se encuentra en la carpeta `<project_name>.Shared`.

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;



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

    Este código recupera el URI del canal de la aplicación desde WNS y, luego, lo registra en el Centro de notificaciones.

    >[AZURE.NOTE]Asegúrese de reemplazar el marcador de posición "hub name" por el nombre del centro de notificaciones que aparece en el portal en la pestaña **Centros de notificaciones** (por ejemplo, **mynotificationhub2** en el ejemplo anterior). Sustituya también el marcador de posición de la cadena de conexión por la cadena de conexión **DefaultListenSharedAccessSignature** que obtuvo en la sección anterior.

5. En la parte superior del controlador de eventos **OnLaunched** en App.xaml.cs, agregue la siguiente llamada al nuevo método **InitNotificationsAsync**:

        InitNotificationsAsync();

    Esto garantiza que el URI del canal se registre en su Centro de notificaciones cada vez que se inicia la aplicación.

6. En el Explorador de soluciones, haga doble clic en **Package.appxmanifest** de la aplicación de la Tienda Windows y, en **Notificaciones**, establezca **Capacidad de aviso** en **Sí**:

   	![][18]

   	En el menú **Archivo**, haga clic en **Guardar todo**.

7. (Opcional) Repita los pasos anteriores para el proyecto de la aplicación de la Tienda de Windows Phone.

8. Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente que contiene la clave de registro.

   	![][19]

9. (Opcional) Repita el paso anterior para ejecutar el otro proyecto.

La carpeta ahora ya está lista para recibir notificaciones.

##Envío de notificaciones desde su back-end

Puede enviar notificaciones mediante Centros de notificaciones desde cualquier back-end a través de nuestra <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz REST</a>. En este tutorial enviará notificaciones con una aplicación de consola .NET. Para ver un ejemplo de cómo enviar notificaciones desde un back-end de Servicios móviles de Azure integrado en Centros de notificaciones, consulte "Introducción a las notificaciones push en Servicios móviles" ([back-end .NET](../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) | [back-end JavaScript](../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md)). Para ver un ejemplo de cómo enviar notificaciones con las API de REST, consulte "Uso de Centros de notificaciones desde Java o PHP" ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Haga clic con el botón derecho en la solución, seleccione **Agregar** y **Nuevo proyecto...**, luego, en **Visual C#** haga clic en **Windows**, **Aplicación de consola** y en **Aceptar**.

   	![][13]

	Esta acción agrega una aplicación de consola nueva de Visual C# a la solución. También puede hacer esto en una solución separada.

4. En Visual Studio, haga clic en **Herramientas**, en **Administrador de paquetes de NuGet** y, después, haga clic en **Consola del administrador de paquetes**.

	Esto muestra la Consola del administrador de paquetes en Visual Studio.

6. En la ventana de la Consola del administrador de paquetes, establezca el **Proyecto predeterminado** como el nuevo proyecto de aplicación de consola y, después, ejecute el siguiente comando en la ventana de la consola:

        Install-Package Microsoft.Azure.NotificationHubs

	Esto agrega una referencia al SDK de los Centros de notificaciones de Azure mediante el <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">paquete de NuGet de concentradores Microsoft.Azure.Notification</a>.

5. Abra el archivo Program.cs y agregue la siguiente instrucción `using`:

        using Microsoft.Azure.NotificationHubs;

6. En la clase **Program**, agregue el siguiente método.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	Asegúrese de reemplazar el marcador de posición "hub name" por el nombre del centro de notificaciones que aparece en el portal en la pestaña **Centros de notificaciones**. Reemplace también el marcador de posición de la cadena de conexión por la cadena de conexión llamada **DefaultFullSharedAccessSignature** que obtuvo en la sección "Configuración del centro de notificaciones".

	>[AZURE.NOTE]Asegúrese de usar la cadena de conexión con acceso **Total**, no con acceso **Escuchar**. La cadena de acceso de escucha no tiene permisos para enviar notificaciones.

7. Agregue las siguientes líneas al método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

8. Haga clic con el botón derecho en el proyecto de la aplicación de consola en Visual Studio y haga clic en **Establecer como proyecto de inicio** para establecerlo como proyecto de inicio. A continuación, presione la tecla **F5** para ejecutar la aplicación.

   	![][14]

	Debería recibir una notificación del sistema en todos los dispositivos registrados. Si hace clic o toca el banner de notificaciones se carga la aplicación.

Puede encontrar todas las cargas compatibles en los temas de [catálogo de notificaciones del sistema], [catálogo de iconos] e [información general sobre distintivos] de MSDN.

##Pasos siguientes

En este sencillo ejemplo, ha difundido notificaciones a todos los dispositivos con Windows. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de los Centros de notificaciones para insertar notificaciones para los usuarios]. Si desea segmentar los usuarios por grupos de interés, consulte [Uso de los Centros de notificaciones para enviar noticias de última hora]. Para obtener más información sobre el uso de Centros de notificaciones, consulte [Información general acerca de los Centros de notificaciones].



<!-- Images. -->
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
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
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Portal de Azure]: https://manage.windowsazure.com/
[Información general acerca de los Centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx

[Uso de los Centros de notificaciones para insertar notificaciones para los usuarios]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Uso de los Centros de notificaciones para enviar noticias de última hora]: notification-hubs-windows-store-dotnet-send-breaking-news.md

[catálogo de notificaciones del sistema]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[catálogo de iconos]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[información general sobre distintivos]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx

<!---HONumber=September15_HO1-->