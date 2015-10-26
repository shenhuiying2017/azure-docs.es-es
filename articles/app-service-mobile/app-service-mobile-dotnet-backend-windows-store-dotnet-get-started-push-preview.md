<properties 
	pageTitle="Incorporación de notificaciones push a la aplicación universal Windows en tiempo de ejecución 8.1 | Aplicaciones móviles de Azure" 
	description="Obtenga información acerca de cómo usar Aplicaciones móviles del Servicio de aplicaciones de Azure y Centros de notificaciones de Azure para enviar notificaciones push a la aplicación Windows." 
	services="app-service\mobile,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/14/2015" 
	ms.author="glenga"/>

# Incorporación de notificaciones push a la aplicación universal Windows en tiempo de ejecución 8.1

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Información general

En este tema se muestra cómo enviar notificaciones push a una aplicación universal Windows en tiempo de ejecución 8.1 con Aplicaciones móviles de Azure y Centros de notificaciones de Azure. En este escenario, cuando se agrega un nuevo elemento, el back-end de la aplicación móvil envía una notificación push a todas las aplicaciones Windows registradas con el Servicio de notificación de Windows (WNS).

Este tutorial se basa en el inicio rápido de aplicaciones móviles del Servicio de aplicaciones. Antes de empezar este tutorial, debe completar primero el tutorial de inicio rápido [Creación de una aplicación Windows](../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md). Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de notificaciones push al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una [cuenta Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) activa.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* Complete el [tutorial de inicio rápido](../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md).  



##<a name="create-gateway"></a>Creación de un centro de notificaciones

Siga estos pasos para crear un nuevo centro de notificaciones para controlar las notificaciones push. Si ya tiene un centro en el mismo grupo de recursos, no es necesario completar esta sección.

1. Visite el [Portal de Azure]. Haga clic en **Examinar todo** > **Aplicaciones móviles** > el back-end que acaba de crear. Haga clic en **Configuración** > **Móvil** > **Inserción**. 

2. Siga el flujo de trabajo para crear un centro de notificaciones. Deberá crear un nuevo espacio de nombres si no hay ninguno en el grupo de recursos actual. Haga clic en **Crear** una vez configurados todos los valores.

Luego, usará este centro de notificaciones para habilitar la inserción en la aplicación.

##Registro de la aplicación para notificaciones push

Para poder enviar notificaciones push a las aplicaciones Windows desde Azure, debe enviar la aplicación a la Tienda Windows. Después, podrá configurar el proyecto de servidor para integrarlo con WNS.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto de la aplicación de la Tienda Windows y, luego, haga clic en **Tienda** > **Asociar aplicación con la Tienda...**. 

    ![][3]
    
2. En el asistente, haga clic en **Siguiente**, inicie sesión con su cuenta Microsoft, escriba un nombre para la aplicación en **Reservar un nuevo nombre de aplicación** y, luego, haga clic en **Reservar**.

3. Una vez creado correctamente el registro de la aplicación, seleccione el nuevo nombre de la aplicación, haga clic en **Siguiente** y, por último, haga clic en **Asociar**. Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.

7. Repita los pasos 1 y 3 para el proyecto de aplicación de la Tienda de Windows Phone con el mismo registro que creó anteriormente para la aplicación de la Tienda Windows.

7. Vaya al [Centro de desarrollo de Windows](https://dev.windows.com/es-ES/overview), inicie sesión con su cuenta Microsoft, haga clic en el nuevo registro de aplicación en **Mis aplicaciones** y, por último, expanda **Servicios** > **Notificaciones push**.

8. En la página **Notificaciones push**, haga clic en el **sitio de Servicios Live** en **Servicios móviles de Microsoft Azure**.

9. En la pestaña **Configuración de aplicaciones**, anote los valores de **Secreto de cliente** y **SID del paquete**.

    ![][6]

    > [AZURE.IMPORTANT]El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No los comparta con nadie ni los distribuya con su aplicación.

##Configuración de aplicaciones móviles para enviar solicitudes de inserción

1. Inicie sesión en el [Portal de Azure] y seleccione **Examinar** > **Aplicación móvil** > su aplicación > **Servicios de notificaciones de inserción**.

2. En el **Servicio de notificaciones de Windows**, escriba la **clave de seguridad** (secreto de cliente) y el **SID del paquete** que ha obtenido en el sitio de Servicios Live. Por último, haga clic en **Guardar**.

El back-end de la aplicación móvil ya está configurado para funcionar con WNS.

##<a id="update-service"></a>Actualización del servidor para enviar notificaciones de inserción

Tras habilitar las notificaciones push en la aplicación, debe actualizar el back-end de la aplicación para enviarlas.

1. En Visual Studio, haga clic con el botón derecho en el proyecto de servidor, haga clic en **Administrar paquetes de NuGet**, busque `Microsoft.Azure.NotificationHubs` y, por último, haga clic en **Instalar**. Esto instala la biblioteca de cliente de Centros de notificaciones.

3. En el proyecto de servidor, abra **Controladores** > **TodoItemController.cs** y agregue las siguientes instrucciones using:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. En el método **PostTodoItem**, agregue el código siguiente después de la llamada a **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

		// Define a WNS payload
		var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" 
                                + item.Text + @"</text></binding></visual></toast>";

        try
        {
			// Send the push notification and log the results.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Este código indica al centro de notificaciones que envíe una notificación push después de la inserción de un elemento nuevo.


## <a name="publish-the-service"></a>Publicación del back-end móvil en Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>Incorporación de notificaciones de inserción a la aplicación

1. En Visual Studio, haga clic con el botón derecho en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**. 

    Esto muestra el cuadro de diálogo Administrar paquetes de NuGet.

2. Busque el SDK de cliente de Aplicaciones móviles del Servicio de aplicaciones que se administrará y haga clic en **Instalar**; seleccione todos los proyectos de cliente de la solución y acepte los términos de uso.

    De esta forma, se descarga, instala y agrega una referencia en todos los proyectos de cliente para la biblioteca de inserción móvil de Azure para Windows.

3. Abra el archivo de proyecto **App.xaml.cs** compartido y agregue las siguientes instrucciones `using`:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

4. En el mismo archivo, agregue la siguiente definición de método **InitNotificationsAsync** para la clase **aplicación**:
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra con sus Aplicaciones móviles del Servicio de aplicaciones.
    
5. En la parte superior del controlador de eventos **OnLaunched**, en **App.xaml.cs**, agregue el modificador **async** a la definición del método y la siguiente llamada al nuevo método **InitNotificationsAsync**, como en el siguiente ejemplo:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Esto garantiza que el valor de ChannelURI de corta duración se registra cada vez que se inicia la aplicación.

6. En el Explorador de soluciones, haga doble clic en **Package.appxmanifest** de la aplicación Tienda Windows y, en **Notificaciones**, establezca **Capacidad de aviso** en **Sí**.

    En el menú **Archivo**, haga clic en **Guardar todo**.

7. Repita el paso anterior para el proyecto de la aplicación de la Tienda de Windows Phone.

La carpeta ahora ya está lista para recibir notificaciones.

##<a id="test"></a>Prueba de las notificaciones push en su aplicación

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push-preview](../../includes/app-service-mobile-windows-universal-test-push-preview.md)]

<!-- Anchors. -->
<!-- URLs. -->
[Portal de Azure]: https://portal.azure.com/
<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-submit-win8-app.png
[1]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/notification-hub-associate-win8-app.png
[4]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-select-app-name.png
[5]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit-app.png
[6]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!---HONumber=Oct15_HO3-->