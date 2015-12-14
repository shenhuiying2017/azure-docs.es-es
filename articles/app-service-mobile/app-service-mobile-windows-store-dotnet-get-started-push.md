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
	ms.date="11/25/2015" 
	ms.author="glenga"/>

# Incorporación de notificaciones push a la aplicación universal Windows en tiempo de ejecución 8.1

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Información general

En este tema se muestra cómo enviar notificaciones push a una aplicación universal Windows en tiempo de ejecución 8.1 con Aplicaciones móviles de Azure y Centros de notificaciones de Azure. En este escenario, cuando se agrega un nuevo elemento, el back-end de la aplicación móvil envía una notificación push a todas las aplicaciones Windows registradas con el Servicio de notificación de Windows (WNS).

Este tutorial se basa en el inicio rápido de aplicaciones móviles del Servicio de aplicaciones. Antes de empezar este tutorial, debe completar primero el tutorial de inicio rápido [Creación de una aplicación Windows](../app-service-mobile-windows-store-dotnet-get-started.md). Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de notificaciones push al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una [cuenta Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) activa.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* Complete el [tutorial de inicio rápido](../app-service-mobile-windows-store-dotnet-get-started.md).  


##<a name="create-hub"></a>Creación de un centro de notificaciones

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Registro de la aplicación para notificaciones push

Para poder enviar notificaciones push a las aplicaciones Windows desde Azure, debe enviar la aplicación a la Tienda Windows. Después, podrá configurar el proyecto de servidor para integrarlo con WNS.

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


##Configuración del back-end para enviar notificaciones push

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>Actualización del servidor para enviar notificaciones de inserción

Tras habilitar las notificaciones push en la aplicación, debe actualizar el back-end de la aplicación para enviarlas.

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


## <a name="publish-the-service"></a>Publicación del back-end móvil en Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>Incorporación de notificaciones de inserción a la aplicación

1. Abra el archivo de proyecto **App.xaml.cs** compartido y agregue las siguientes instrucciones `using`:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. En el mismo archivo, agregue la siguiente definición de método **InitNotificationsAsync** para la clase **aplicación**:
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra con sus Aplicaciones móviles del Servicio de aplicaciones.
    
3. En la parte superior del controlador de eventos **OnLaunched**, en **App.xaml.cs**, agregue el modificador **async** a la definición del método y la siguiente llamada al nuevo método **InitNotificationsAsync**, como en el siguiente ejemplo:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Esto garantiza que el valor de ChannelURI de corta duración se registra cada vez que se inicia la aplicación.

4. En el Explorador de soluciones, haga doble clic en **Package.appxmanifest** de la aplicación Tienda Windows y, en **Notificaciones**, establezca **Capacidad de aviso** en **Sí**.

    En el menú **Archivo**, haga clic en **Guardar todo**.

5. Repita el paso anterior para el proyecto de la aplicación de la Tienda de Windows Phone.

La carpeta ahora ya está lista para recibir notificaciones.

##<a id="test"></a>Prueba de las notificaciones push en su aplicación

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

##Pasos siguientes

Por último, el tutorial [Enviar notificaciones entre plataformas a un usuario específico](app-service-mobile-windows-store-dotnet-push-notifications-to-users.md) le mostrará cómo enviar una notificación push a todos los registros de dispositivos que pertenecen a un usuario autenticado concreto, en cualquier plataforma de dispositivo.

##<a id="more"></a>Más

* Las plantillas proporcionan flexibilidad para enviar inserciones multiplataforma e inserciones localizadas. [Uso del cliente administrado para Aplicaciones móviles de Azure](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications) muestra cómo registrar plantillas.
* Las etiquetas permiten dirigirse a clientes segmentados con inserciones. [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) muestra cómo agregar etiquetas a la instalación de un dispositivo.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=AcomDC_1203_2015-->