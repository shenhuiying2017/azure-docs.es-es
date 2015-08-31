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
	ms.date="06/25/2015"
	ms.author="yuaxu"/>

# Envío de notificaciones entre plataformas a un usuario específico

[AZURE.INCLUDE [app-service-mobile-selector-push-users-preview](../../includes/app-service-mobile-selector-push-users-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

En este tema se muestra cómo enviar notificaciones a todos los dispositivos registrados de un usuario concreto desde su back-end móvil. Introdujo el concepto de [plantillas], que proporciona a las aplicaciones cliente la libertad de especificar formatos de carga y marcadores de posición de variables en el registro. A continuación, envíe aciertos a todas las plataformas con estos marcadores de posición, y habilite las notificaciones multiplataforma.

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

2. En **AppDelegate.cs**, reemplace la llamada de**RegisterAsync** en **RegisteredForRemoteNotifications** por lo siguiente para trabajar con las plantillas:

        // delete await push.RegisterAsync (deviceToken);
        
        var notificationTemplate = "{"aps": {"alert":"$(message)"}}";

        JObject templateBody = new JObject();
        templateBody["body"] = notificationTemplate;

        JObject templates = new JObject();
        templates["testApnsTemplate"] = templateBody;

        // register with templates
        await push.RegisterAsync (deviceToken, templates);

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

            var notification = new Dictionary<string, string>{{"message", item.Text}};

            try
            {
                await Hub.Push.SendTemplateNotificationAsync(notification, userTag);
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
[Introducción a la autenticación]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Introducción a las notificaciones de inserción]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[plantillas]: https://msdn.microsoft.com/es-es/library/dn530748.aspx

<!---HONumber=August15_HO8-->