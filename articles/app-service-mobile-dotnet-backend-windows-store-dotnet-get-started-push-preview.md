<properties 
	pageTitle="Incorporación de notificaciones push a la aplicación universal de Windows con el Servicio de aplicaciones de Azure" 
	description="Obtenga información acerca de cómo usar Servicio de aplicaciones de Azure para enviar notificaciones push a su aplicación universal de Windows." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="yuaxu"/>

# Incorporación de notificaciones push a la aplicación Tienda Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

En este tema se muestra cómo enviar notificaciones push a una aplicación universal de Windows desde un servidor back-end de .NET mediante el Servicio de aplicaciones de Azure. Al finalizar, enviará notificaciones push desde el back-end de .NET a todas las aplicaciones universales de Windows registradas en la inserción de registros.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones push:

1. [Registro de la aplicación para notificaciones de inserción](#register)
2. [Configuración](#configure)
3. [Actualización del servicio para enviar notificaciones push](#update-service)
4. [Incorporación de notificaciones de inserción a la aplicación](#add-push)
5. [Prueba de las notificaciones push en su aplicación](#test)

Este tutorial se basa en el inicio rápido de aplicaciones móviles del Servicio de aplicaciones. Antes de iniciar este tutorial, debe completar primero [Introducción a las aplicaciones móviles del Servicio de aplicaciones].

Para completar este tutorial, necesitará lo siguiente:

* Una [cuenta Microsoft Store activa](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>.

##<a id="register"></a>Registro de la aplicación para notificaciones de inserción

Para enviar notificaciones push a las aplicaciones universales de Windows con Servicio de aplicaciones de Azure, debe enviar su aplicación a la Tienda Windows. A continuación, debe configurar las credenciales del servicio de notificaciones push para integrarlo con WNS.

1. Si aún no ha registrado la aplicación, vaya a la página <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Enviar una aplicación</a> en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión con su cuenta de Microsoft y, a continuación, haga clic en **Nombre de la aplicación**.

    ![][0]

2. Escriba el nombre de la aplicación en **Nombre de la aplicación**, haga clic en **Reservar nombre de aplicación** y, a continuación, haga clic en **Guardar**.

    ![][1]

    Se crea un nuevo registro de la Tienda Windows para su aplicación.

4. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto de la aplicación para la Tienda Windows, haga clic en **Tienda** y, a continuación, en **Asociar aplicación con la Tienda...**.

    ![][3]

    Aparece el asistente **Asocie la aplicación con la Tienda Windows**.

5. En el asistente, haga clic en **Iniciar sesión** y, a continuación, inicie sesión con su cuenta Microsoft.

6. Haga clic en la aplicación que registró en el paso 2, haga clic en **Siguiente** y, después, en **Asociar**.

    ![][4]

    Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.

7. (Opcional) Repita los pasos 4-6 para el proyecto de la aplicación de la Tienda de Windows Phone.

7. De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Servicios**.

    ![][5]

8. En la página **Servicios**, haga clic en el **sitio de Servicios de Live** en **Servicios móviles de Microsoft Azure**.

    ![][17]

9. En la pestaña **Configuración de aplicaciones**, anote los valores de **Secreto del cliente** e **Identificador de seguridad de paquete (SID)**.

    ![][6]

    > [AZURE.NOTE]**Nota de seguridad**El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No los comparta con nadie ni los distribuya con su aplicación.

##<a id="configure"></a>Configuración de aplicaciones móviles para enviar solicitudes de inserción

1. Inicie sesión en el [Portal de vista previa de Azure], seleccione **Examinar**, **Aplicaciones móviles** y, a continuación, haga clic en la aplicación. Haga clic en los servicios de notificaciones push.

2. En el servicio de notificaciones de Windows, escriba su **Secreto del cliente** e **Identificador de seguridad de paquete (SID)** y guárdelo.

La aplicación móvil del Servicio de aplicaciones ya está configurada para poder funcionar con WNS.

<!-- URLs. -->
[Portal de vista previa de Azure]: https://portal.azure.com/

##<a id="update-service"></a>Actualización del servicio para enviar notificaciones push

Tras habilitar las notificaciones push en la aplicación, debe actualizar el back-end de la aplicación para enviarlas.

1. En Visual Studio, haga clic con el botón derecho en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**.

2. Busque **Microsoft.Azure.NotificationHubs** y haga clic en **Instalar** para todos los proyectos de la solución.

3. En el Explorador de soluciones de Visual Studio, expanda la carpeta **Controladores** en el proyecto de back-end móvil. Abra TodoItemController.cs. Al principio del archivo, agregue las siguientes instrucciones `using`:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

4. Agregue el siguiente fragmento al método `PostTodoItem` después de la llamada **InsertAsync**:

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // windows payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + item.Text + @"</text></binding></visual></toast>";

        await Hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

    Este código indica a la base de datos central de notificaciones asociada a esta aplicación móvil que envíe una notificación push después de la inserción de un elemento de tareas pendientes.


## <a name="publish-the-service"></a>Publicación del back-end móvil en Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>Incorporación de notificaciones de inserción a la aplicación

1. En Visual Studio, haga clic con el botón derecho en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**. 

    Esto muestra el cuadro de diálogo Administrar paquetes de NuGet.

2. Busque el SDK de cliente de Aplicaciones móviles del Servicio de aplicaciones que se administrará y haga clic en **Instalar**, seleccione todos los proyectos de la solución y acepte los términos de uso.

    De esta forma, se descarga, instala y agrega una referencia en todos los proyectos para la biblioteca de inserción móvil de Azure para Windows.

3. Abra el archivo de proyecto **App.xaml.cs** y agregue las siguientes instrucciones `using`:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.MobileServices;

    Este proyecto es universal, este archivo se encuentra en la carpeta `<project_name>.Shared`.

4. En el mismo archivo, agregue la siguiente definición de método **InitNotificationsAsync** para la clase **aplicación**:
    
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            
            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra con sus Aplicaciones móviles del Servicio de aplicaciones.
    
5. En la parte superior del controlador de eventos **OnLaunched** en **App.xaml.cs**, agregue la siguiente llamada al nuevo método **InitNotificationsAsync**:

        InitNotificationsAsync();

    Esto garantiza que el valor de ChannelURI de corta duración se registra cada vez que se inicia la aplicación.

6. En el Explorador de soluciones, haga doble clic en **Package.appxmanifest** de la aplicación Tienda Windows y, en **Notificaciones**, establezca **Capacidad de aviso** en **Sí**.

    En el menú **Archivo**, haga clic en **Guardar todo**.

7. (Opcional) Repita los pasos anteriores para el proyecto de la aplicación de la Tienda de Windows Phone.

8. Presione la tecla **F5** para ejecutar las aplicaciones.

La carpeta ahora ya está lista para recibir notificaciones.

##<a id="test"></a>Prueba de las notificaciones push en su aplicación

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-windows-universal-test-push-preview](../includes/app-service-mobile-dotnet-backend-windows-universal-test-push-preview.md)]

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
<!--HONumber=54-->