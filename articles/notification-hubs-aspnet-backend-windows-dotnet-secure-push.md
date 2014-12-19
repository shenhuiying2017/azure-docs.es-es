<properties title="Azure Notification Hubs Secure Push" pageTitle="Inserción segura de los Centros de notificaciones de Azure" metaKeywords="notificaciones de inserción de Azure, centros de notificaciones de Azure, mensajería de Azure, inserción segura" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

#Inserción segura de los Centros de notificaciones de Azure

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/es-es/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows Universal" class="current">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS">iOS</a>
		<a href="/es-es/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android">Android</a>
</div>

La compatibilidad con las notificaciones de inserción en Microsoft Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones de inserción tanto en aplicaciones de consumidor, como en aplicaciones empresariales para plataformas móviles. 

Debido a restricciones reguladoras o de seguridad, algunas veces una aplicación podría querer incluir algo en la notificación que no se puede trasmitir a través de la infraestructura de las notificaciones de inserción estándar. En este tutorial se describe cómo lograr la misma experiencia enviando información importante a través de una conexión segura y autenticada entre el dispositivo cliente y el back-end de la aplicación.

A un alto nivel, el flujo es el siguiente:

1. El back-end de la aplicación:
	- Almacena la carga segura en la base de datos back-end.
	- Envía el identificador de esta notificación al dispositivo (no se envía información segura).
2. La aplicación del dispositivo, cuando recibe la información:
	- El dispositivo entra en contacto con el back-end que solicita la carga segura.
	- La aplicación puede mostrar la carga como una notificación en el dispositivo.

Es importante tener en cuenta que en el flujo anterior (y en este tutorial), se supone que el dispositivo almacena un token de autenticación en el almacenamiento local, después de que el usuario inicia sesión. Esto garantiza una experiencia completamente transparente, dado que el dispositivo puede recuperar la carga segura de la notificación mediante este token. Si la aplicación no almacena tokens de autenticación en el dispositivo, o si estos tokens han expirado, la aplicación del dispositivo, al recibir la notificación, debe mostrar una notificación genérica pidiendo al usuario que inicie la aplicación. Después, la aplicación autentica al usuario y muestra la carga de la notificación.

Este tutorial Inserción segura muestra cómo enviar una notificación de inserción de forma segura. El tutorial se basa en el tutorial **Notificación a los usuarios**, por lo que debe completar los pasos de ese tutorial primero.

> [AZURE.NOTE] Este tutorial asume que ha creado y configurado el centro de notificaciones tal y como se describe en [Introducción a los Centros de notificaciones (Tienda Windows)](http://azure.microsoft.com/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/).
Asimismo, tenga en cuenta que Windows Phone 8.1 requiere credenciales de Windows (no de Windows Phone) y que las tareas en segundo plano no funcionan en Windows Phone 8.0 o Silverlight 8.1. Para aplicaciones de la Tienda Windows, puede recibir notificaciones a través de una tarea en segundo plano solamente si la aplicación tiene la pantalla de bloqueo habilitada (haga clic en la casilla en el manifiesto de la aplicación).

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Modificación del proyecto de Windows Phone

1. En el proyecto **NotifyUserWindowsPhone**, agregue el siguiente código a App.xaml.cs para registrar la tarea de segundo plano de inserción. Agregue la siguiente línea de código al final del método `OnLaunched()`:

		RegisterBackgroundTask();

2. Todavía en App.xaml.cs, agregue el siguiente código inmediatamente después del método `OnLaunched()`:

		private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3. Agregue las siguientes instrucciones `using` en la parte superior del archivo App.xaml.cs:

		using Windows.Networking.PushNotifications;
		using Windows.ApplicationModel.Background;

4. En el menú **Archivo** de Visual Studio, haga clic en **Guardar todo**.
		
## Creación del componente de segundo plano de inserción

El paso siguiente es crear el componente de segundo plano de inserción.

1. En el Explorador de soluciones, haga clic con el botón secundario en el nodo de nivel superior de la solución (**Solution SecurePush** en este caso) y luego haga clic en **Agregar**, y, por último, en **Nuevo proyecto**.

2. Expanda **Aplicaciones de la Tienda**, haga clic en **Aplicaciones Windows Phone** y luego en **Componente de Windows en tiempo de ejecución (Windows Phone)**. Asigne al proyecto el nombre **PushBackgroundComponent** y haga clic en **Aceptar** para crear el proyecto.

	![][12]

3. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **PushBackgroundComponent (Windows Phone 8.1)**, luego haga clic en **Agregar** y en **Clase**. Asigne un nombre a la nueva clase **PushBackgroundTask.cs**. Haga clic en **Agregar** para generar la clase.

4. Reemplace todo el contenido de la definición del espacio de nombres **PushBackgroundComponent** por el siguiente código, sustituyendo el marcador de posición `{extremo de back-end}` por el extremo de back-end obtenido mientras implementaba el back-end:

		public sealed class Notification
    		{
        		public int Id { get; set; }
        		public string Payload { get; set; }
        		public bool Read { get; set; }
    		}
    
		    public sealed class PushBackgroundTask : IBackgroundTask
    		{
        		private string GET_URL = "{back-end endpoint}/api/notifications/";
		
        		async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
		        {
        		    // Store the content received from the notification so it can be retrieved from the UI.
		            RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
            		var notificationId = raw.Content;

            		// retrieve content
		            BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
            		var httpClient = new HttpClient();
		            var settings = ApplicationData.Current.LocalSettings.Values;
		            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

		            var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

            		var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

		            ShowToast(notification);

		            deferral.Complete();
		        }

		        private void ShowToast(Notification notification)
		        {
		            ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
		            XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
            		XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
		            toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
    	        	ToastNotification toast = new ToastNotification(toastXml);
		            ToastNotificationManager.CreateToastNotifier().Show(toast);
    		    }
    		}

5. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **PushBackgroundComponent (Windows Phone 8.1)** y haga clic en **Administrar paquetes de NuGet**.

6. A la izquierda, haga clic en **En línea**.

7. En el cuadro **Buscar**, escriba **cliente http**.

8. En la lista de resultados, haga clic en **Bibliotecas de cliente HTTP de Microsoft** y después haga clic en **Instalar**. Complete la instalación.

9. Vuelva al cuadro **Buscar** de NuGet, escriba **Json.net**. Instale el paquete **Json.NET** y cierre la ventana Administrador de paquetes de NuGet.

10. Agregue las siguientes instrucciones `using` en la parte superior del archivo **PushBackgroundTask.cs**:

		using Windows.ApplicationModel.Background;
		using Windows.Networking.PushNotifications;
		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		using Windows.UI.Notifications;
		using Windows.Data.Xml.Dom;

11. En el Explorador de soluciones, en el proyecto **NotifyUserWindowsPhone (Windows Phone 8.1)**, haga clic con el botón secundario en **Referencias** y haga clic en **Agregar referencia...**. En el cuadro de diálogo Administrador de referencias, active la casilla situada junto a **PushBackgroundComponent** y haga clic en **Aceptar**.

12. En el Explorador de soluciones, haga doble clic en **Package.appxmanifest** en el proyecto **NotifyUserWindowsPhone (Windows Phone 8.1)**. En **Notificaciones**, establezca **Capacidad de aviso** en **Sí**.

	![][3]

13. Todavía en **Package.appxmanifest**, haga clic en el menú **Declaraciones** cerca de la parte superior. En el cuadro desplegable **Declaraciones disponibles**, haga clic en **Tareas en segundo plano** y haga clic en **Agregar**.
 
14. En **Package.appxmanifest**, en **Propiedades**, active **Notificación de inserción**.

15. En **Package.appxmanifest**, en **Configuración de la aplicación**, escriba **PushBackgroundComponent.PushBackgroundTask** en el campo **Punto de entrada**.

	![][13]

16. En el menú **Archivo**, haga clic en **Guardar todo**.

## Ejecución de la aplicación

Para ejecutar la aplicación, realice las siguientes tareas:

1. En Visual Studio, ejecute la aplicación Web API **AppBackend**. Se mostrará una página web ASP.NET.

2. En Visual Studio, ejecute la aplicación Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)**. El emulador de Windows Phone se ejecuta y carga la aplicación automáticamente.

3. En la interfaz de usuario de la aplicación **NotifyUserWindowsPhone**, escriba un nombre de usuario y contraseña. Esta información puede ser cualquier cadena, pero deben tener el mismo valor.

4. En la interfaz de usuario de la aplicación **NotifyUserWindowsPhone**, haga clic en **Log in and register** (Iniciar sesión y registrarse). Después, haga clic en **Send push** (Enviar inserción).

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
