## Crear el proyecto de WebAPI

Siga estos pasos para crear un nuevo modelo de WebAPI de ASP.NET para autenticar a clientes y generar notificaciones o modificar un back-end existente de proyectos anteriores o consulte el tutorial [Envío de notificaciones de inserción a usuarios autenticados](../articles/mobile-services-dotnet-backend-ios-push-notifications-app-users.md) .

> [AZURE.NOTE] **Importante**: antes de iniciar este tutorial, asegúrese de que tiene instalada la versión más reciente del Administrador de paquetes de NuGet. Para comprobarlo, inicie Visual Studio. En el menú **Herramientas**, haga clic en **Extensiones y actualizaciones**. Busque **Administrador de paquetes de NuGet para Visual Studio 2013** y asegúrese de que tiene la versión 2.8.50313.46 o posterior. Si no es así, desinstale e instale de nuevo el Administrador de paquetes de NuGet.
> 
> ![][4]

> [AZURE.NOTE] Asegúrese de que ha instalado el [SDK de Azure](http://azure.microsoft.com/downloads/) para Visual Studio para la implementación de sitios web.

1. Inicie Visual Studio o Visual Studio Express.
2. En Visual Studio, haga clic en **Archivo** y, a continuación, haga clic en **Nuevo** y en **Proyecto**, expanda **Plantillas**, **Visual C#** y, a continuación, haga clic en **Web** y **Aplicación web ASP.NET**, escriba el nombre **AppBackend**y, a continuación, haga clic en **Aceptar**. 
	
	![][1]

3. En el cuadro de diálogo **Nuevo proyecto ASP.NET**, haga clic en **API web** y, a continuación, en **Aceptar**.

	![][2]

4. En el cuadro de diálogo **Configurar sitio de Azure**, elija una suscripción, una región y una base de datos para usarlas con este proyecto. A continuación, haga clic en **Aceptar** para crear el proyecto.

	![][5]

5. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **AppBackend** y, a continuación, seleccione **Administrar paquetes de NuGet**.

6. En el lado izquierdo, haga clic en **En línea** y busque **servicebus** en el cuadro **Búsqueda**.

7. En la lista de resultados, haga clic en **Bus de servicio de Microsoft Azure** y, a continuación, haga clic en **Instalar**. Complete la instalación y, a continuación, cierre la ventana del administrador de paquetes de NuGet.

	![][14]

8. Ahora crearemos una nueva clase **Notifications.cs**. Vaya al Explorador de soluciones, haga clic en la carpeta **Modelos**, haga clic en **Agregar** y, a continuación, en **Clase**. Después de asignar el nombre a la nueva clase **Notifications.cs**, haga clic en **Agregar** para generar la clase. Este módulo representa las diferentes notificaciones seguras que se enviarán. En una implementación completa, las notificaciones se almacenan en una base de datos. Por simplicidad, este tutorial las almacena en memoria.

	![][6]

9. En Notifications.cs, agregue la siguiente instrucción  `using` en la parte superior del archivo:

        using Microsoft.ServiceBus.Notifications;

10. continuación, reemplace la definición de clase  `Notifications` por lo siguiente y asegúrese de reemplazar los dos marcadores de posición con la cadena de conexión (con acceso total) para el Centro de notificaciones y el nombre del centro (disponible en el [Portal de administración de Azure](http://manage.windowsazure.com)):

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. Ahora crearemos una nueva clase **AuthenticationTestHandler.cs**. En el Explorador de soluciones, haga clic en el proyecto **AppBackend**, haga clic en **Agregar** y, a continuación, haga clic en **Clase**. Asigne a la nueva clase el nombre **AuthenticationTestHandler.cs** y haga clic en **Agregar** para generar la clase. Esta clase se usa para autenticar usuarios mediante  *Basic Authentication*. Tenga en cuenta que su aplicación puede utilizar cualquier esquema de autenticación.

12. En AuthenticationTestHandler.cs, agregue las siguientes instrucciones  `using`:

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;

13. En AuthenticationTestHandler.cs, reemplace la definición de clase  `AuthenticationTestHandler` con lo siguiente:

		public class AuthenticationTestHandler : DelegatingHandler
	    {
	        protected override Task<HttpResponseMessage> SendAsync(
	        HttpRequestMessage request, CancellationToken cancellationToken)
	        {
	            var authorizationHeader = request.Headers.GetValues("Authorization").First();
	
	            if (authorizationHeader != null && authorizationHeader
	                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
	            {
	                string authorizationUserAndPwdBase64 =
	                    authorizationHeader.Substring("Basic ".Length);
	                string authorizationUserAndPwd = Encoding.Default
	                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
	                string user = authorizationUserAndPwd.Split(':')[0];
	                string password = authorizationUserAndPwd.Split(':')[1];
	
	                if (verifyUserAndPwd(user, password))
	                {
	                    // Attach the new principal object to the current HttpContext object
	                    HttpContext.Current.User =
	                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
	                    System.Threading.Thread.CurrentPrincipal =
	                        System.Web.HttpContext.Current.User;
	                }
	                else return Unauthorised();
	            }
	            else return Unauthorised();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorised()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **Nota de seguridad:** La clase  `AuthenticationTestHandler` no proporciona una autenticación verdadera. Se utiliza únicamente para simular una autenticación básica y no es segura. Debe implementar un mecanismo de autenticación seguro en las aplicaciones y servicios de producción.				

14. Agregue el siguiente código al final del método  `Register` en la clase **App_Start/WebApiConfig.cs**:

		config.MessageHandlers.Add(new AuthenticationTestHandler());

15. Next we create a new controller **RegisterController**. In Solution Explorer, right-click the **Controllers** folder, then click **Add**, then click **Controller**. Click the **Web API 2 Controller -- Empty** item, and then click **Add**. Name the new class **RegisterController**, and then click **Add** again to generate the controller.

	![][7]

	![][8]

16. En RegiterController.cs, agregue las siguientes instrucciones  `using`:

        using Microsoft.ServiceBus.Notifications;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

17. Agregue el siguiente código a la definición de clase  `RegisterController`. Observe que, en este código, agregamos la etiqueta de usuario al usuario autenticado por el controlador. También puede realizar comprobaciones opcionales para comprobar que el usuario puede registrarse para las etiquetas solicitadas.

		private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            string newRegistrationId = null;
            
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

18. Cree un controlador nuevo **NotificationsController**, de la misma manera que hemos creado **RegisterController**. Este componente expone una forma para que el dispositivo recupere de forma segura la notificación y proporciona una forma para que un usuario active una inserción segura en los dispositivos. Tenga en cuenta que al enviar la notificación al Centro de notificaciones, enviamos una notificación sin procesar solo con el identificador de la notificación (no el mensaje real).

19. En NotificationsController.cs, agregue las siguientes instrucciones  `using`:

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

20. Agregue el código siguiente dentro de la definición de clase **NotificationsController** y asegúrese de comentar los fragmentos de código para plataformas con las que no trabaja.

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;

            // windows
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

            // apns
            var alert = "{\"aps\":{\"alert\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);

            // gcm
            var notif = "{ \"data\" : {\"msg\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

21. Presione **F5** para ejecutar la aplicación y comprobar que hasta ahora todo es correcto. La aplicación debería iniciar un explorador web y mostrar la página principal de ASP.NET. 

22. Ahora implementaremos esta aplicación en un sitio web de Azure a fin de que sea accesible para todos los dispositivos. Haga clic con el botón secundario en el proyecto **AppBackend** y, a continuación, seleccione **Publicar**.

23. Seleccione Sitio web Azure como destino de publicación.

    ![][B15]

24. Inicie sesión con su cuenta de Azure y seleccione un sitio web nuevo o existente.

    ![][B16]

25. Tome nota de la propiedad **URL de destino** en la pestaña **Conexión**. Más tarde en este tutorial haremos referencia a esta URL como  *backend endpoint*. Haga clic en **Publicar**.

    ![][B18]


[1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG

<!--HONumber=49-->