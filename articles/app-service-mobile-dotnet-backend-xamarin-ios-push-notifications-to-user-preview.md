<properties 
	pageTitle="Envío de notificaciones push a un usuario específico con cliente Xamarin iOS" 
	description="Obtenga información acerca del envío de notificaciones push para todos los dispositivos de un usuario." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="yuaxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/17/2015"
	ms.author="yuaxu"/>

# Envío de notificaciones multiplataforma a un usuario específico

[AZURE.INCLUDE [app-service-mobile-selector-push-users-preview](../includes/app-service-mobile-selector-push-users-preview.md)]

En este tema se muestra cómo enviar notificaciones a todos los dispositivos registrados de un usuario concreto desde el back-end móvil. Incorpora el concepto de [plantillas], que proporciona a las aplicaciones cliente la libertad de especificar formatos de carga y marcadores de posición variables en el registro. Envíe coincidencias a todas las plataformas con estos marcadores de posición mediante la activación de las notificaciones multiplataforma.

> [AZURE.NOTE] Para que la inserción funcione con clientes multiplataforma, tendrá que completar este tutorial para cada plataforma que desea habilitar. Solo tendrá que realizar la [actualización de back-end de móvil](#backend) una vez para los clientes que comparten el mismo back-end móvil.
 
##Requisitos previos 

Antes de comenzar con este tutorial, debe haber completado estos tutoriales de Servicios de aplicaciones para cada plataforma cliente con la que desee trabajar:

+ [Introducción a la autenticación]<br/>Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Introducción a las notificaciones de inserción]<br/>Configura la aplicación de ejemplo TodoList para la notificación de inserción.

##<a name="client"></a>Actualización del cliente para registrar plantillas para administrar las inserciones multiplataforma

1. Mueva los fragmentos de registro de APN de **FinishedLaunching** en **AppDelegate.cs** a la definición de la tarea **RefreshAsync** en **QSTodoListViewController.cs**. Los registros deben realizarse una vez completada la autenticación.

        ...
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }

            // registro de la inserción para iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound, 
                new NSSet ());

            UIApplication.SharedApplication.RegisterUserNotificationSettings (settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        ...

2. En **AppDelegate.cs**, reemplace la llamada de **RegisterAsync** en **RegisteredForRemoteNotifications** por lo siguiente para trabajar con las plantillas:

        // eliminar await push.RegisterAsync (deviceToken);
        
        var notificationTemplate = "{\"aps\": {\"alert\":\"$(message)\"}}";

        JObject templateBody = new JObject();
        templateBody["body"] = notificationTemplate;

        JObject templates = new JObject();
        templates["testApnsTemplate"] = templateBody;

        // registrar con plantillas
        await push.RegisterAsync (deviceToken, templates);

##<a name="backend"></a>Actualización del back-end de servicio para enviar notificaciones a un usuario específico

1. En Visual Studio, actualice la definición del método  `PostTodoItem` con el siguiente código:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // obtener credenciales de los Centros de notificaciones asociadas a esta aplicación móvil
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // conectar a Centro de notificaciones
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // obtener el identificador de usuario actual y crear la etiqueta de usuario determinada
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

Vuelva a publicar el proyecto de back-end móvil y ejecute cualquiera de las aplicaciones cliente que ha configurado. En el proceso de inserción del elemento, el back-end enviará notificaciones a todas las aplicaciones cliente en las que el usuario haya iniciado sesión.

<!-- URLs. -->
[Introducción a la autenticación]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Introducción a las notificaciones push]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[plantillas]: https://msdn.microsoft.com/es-es/library/dn530748.aspx

<!--HONumber=49-->