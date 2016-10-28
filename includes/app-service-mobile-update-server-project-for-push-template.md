En esta sección, se actualiza el código del proyecto de back-end de Aplicaciones móviles existente con el objetivo de enviar una notificación de inserción cada vez que se agrega un nuevo elemento. Dado que los clientes se registran para recibir notificaciones de inserción mediante un registro de plantilla, puede enviar un único mensaje de notificación de inserción a todas las plataformas cliente. Cada registro de plantilla de cliente contiene un parámetro *messageParam*. Cuando se envía la notificación, *messageParam* contendrá una cadena, que es el texto del elemento que se va a insertar. Para obtener más información sobre cómo utilizar plantillas con Centros de notificaciones, consulte [Plantillas](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

Elija el procedimiento de abajo que se corresponda con el tipo de proyecto de back-end: [back-end de .NET](#dotnet) o [back-end de Node.js](#nodejs).

### <a name="dotnet"></a>Proyecto de back-end de .NET
1. En Visual Studio, haga clic con el botón derecho en el proyecto de servidor, haga clic en **Administrar paquetes de NuGet**, busque `Microsoft.Azure.NotificationHubs` y, por último, haga clic en **Instalar**. Esto instala la biblioteca de los Centros de notificaciones para enviar notificaciones desde el back-end.

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

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

	Esta acción envía una notificación de plantilla que contiene el archivo item.text cuando se inserta un nuevo elemento.

4. Vuelva a publicar el proyecto de servidor.

### <a name="nodejs"></a>Proyecto de back-end de Node.js

1. Si no lo ha hecho todavía, [descargue el proyecto de back-end inicio rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) o utilice el [editor en línea del Portal de Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Reemplace el código existente en el archivo todoitem.js por lo siguiente:

		var azureMobileApps = require('azure-mobile-apps'),
	    promises = require('azure-mobile-apps/src/utilities/promises'),
	    logger = require('azure-mobile-apps/src/logger');
	
		var table = azureMobileApps.table();
		
		table.insert(function (context) {
	    // For more information about the Notification Hubs JavaScript SDK, 
	    // see http://aka.ms/nodejshubs
	    logger.info('Running TodoItem.insert');
	    
	    // Define the template payload.
	    var payload = '{"messageParam": "' + context.item.text + '" }';  
	    
	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a template notification.
	                context.push.send(null, payload, function (error) {
	                    if (error) {
	                        logger.error('Error while sending push notification: ', error);
	                    } else {
	                        logger.info('Push notification sent successfully!');
	                    }
	                });
	            }
	            // Don't forget to return the results from the context.execute()
	            return results;
	        })
	        .catch(function (error) {
	            logger.error('Error while running context.execute: ', error);
	        });
		});

		module.exports = table;  

	Esta acción envía una notificación de plantilla que contiene el archivo item.text cuando se inserta un nuevo elemento.

2. Cuando edite el archivo en el equipo local, vuelva a publicar el proyecto de servidor.

<!---HONumber=AcomDC_0629_2016-->