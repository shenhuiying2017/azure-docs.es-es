<properties 
	pageTitle="Envío de notificaciones push a un usuario específico con cliente Xamarin iOS" 
	description="Obtenga información acerca del envío de notificaciones push para todos los dispositivos de un usuario" 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/01/2015"
	ms.author="yuaxu"/>

# Envío de notificaciones entre plataformas a un usuario específico

[AZURE.INCLUDE [app-service-mobile-selector-push-users](../../includes/app-service-mobile-selector-push-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tema se muestra cómo enviar notificaciones a todos los dispositivos registrados de un usuario concreto desde su back-end móvil. Introduce las [plantillas], que proporcionan a las aplicaciones cliente la libertad para especificar los formatos de carga y marcadores de posición de variables en el registro. Cuando se envía una notificación de plantilla desde un servidor, la base de datos central de notificaciones la dirige a todas las plataformas con estos marcadores de posición, habilitando notificaciones entre plataformas.

> [AZURE.NOTE]Para obtener la inserción (push) cuando trabaja con clientes multiplataforma, es necesario completar este tutorial para cada plataforma que desea habilitar. Solo tendrá que hacer la [actualización de back-end móvil](#backend) una vez para los clientes que comparten el mismo back-end móvil.
 
##Requisitos previos 

Antes de iniciar este tutorial, debe haber completado estos tutoriales del Servicio de aplicaciones para cada plataforma cliente que desee que esté en funcionamiento:

+ [Introducción a la autenticación]<br/>Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Introducción a las notificaciones de inserción]<br/>Configura la aplicación de ejemplo TodoList para notificaciones push.

##<a name="client"></a>Actualización del cliente para registrarlo para plantillas con el fin de controlar las inserciones multiplataforma

1. Mueva los fragmentos de registro de APN de **FinishedLaunching** en **AppDelegate.cs** a la definición de la tarea **RefreshAsync** en **QSTodoListViewController.cs**. Los registros deben realizarse una vez completada la autenticación.

        ...
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound, 
                new NSSet ());

            UIApplication.SharedApplication.RegisterUserNotificationSettings (settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        ...



##<a name="backend"></a>Actualización del servidor back-end de servicio para enviar notificaciones a un usuario específico

1. En Visual Studio, actualice la definición del método `PostTodoItem` por el código siguiente:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // get notification hubs credentials associated with this mobile app
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // connect to notification hub
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // get the current user id and create given user tag
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            // Sending the message so that all template registrations that contain "messageParam"
            // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string,string> templateParams = new Dictionary<string,string>();
            templateParams["messageParam"] = item.Text + " was added to the list.";

            try
            {
                await Hub.SendTemplateNotificationAsync(templateParams, userTag);
            }
            catch (System.Exception ex)
            {
                throw;
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

##<a name="test"></a>Prueba de la aplicación

Vuelva a publicar el proyecto de back-end móvil y ejecute cualquiera de las aplicaciones cliente que ha configurado. En el elemento de inserción, el back-end enviará notificaciones a todas las aplicaciones cliente en las que el usuario ha iniciado sesión.

<!-- URLs. -->
[Introducción a la autenticación]: app-service-mobile-xamarin-ios-get-started-users.md
[Introducción a las notificaciones de inserción]: app-service-mobile-xamarin-ios-get-started-push.md
[plantillas]: ../notification-hubs/notification-hubs-templates.md

<!---HONumber=AcomDC_1203_2015--->