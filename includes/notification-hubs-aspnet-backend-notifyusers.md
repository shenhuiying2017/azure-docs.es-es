## Creación del proyecto WebAPI

El primer paso es crear un proyecto de ASP.NET WebAPI. Este es el back-end que se utiliza para autenticar clientes y generar notificaciones.

> [AZURE.NOTE] **Importante**: antes de iniciar este tutorial, asegúrese de que tiene instalada la versión más reciente del Administrador de paquetes de NuGet. Para comprobarlo, inicie Visual Studio. En el menú **Herramientas**, haga clic en **Extensiones y actualizaciones**. Busque **Administrador de paquetes de NuGet para Visual Studio 2013** y asegúrese de que tiene la versión 2.8.50313.46 o posterior. Si no es así, desinstale e instale de nuevo el Administrador de paquetes de NuGet.
>
> ![][0]

1.  Inicie Visual Studio con privilegios elevados (ejecutar como administrador).
2.  En Visual Studio o Visual Studio Express, haga clic en **Archivo**, a continuación en **Nuevo**, luego en **Proyecto**, expanda **Plantillas** y **Visual C\#**. A continuación, haga clic en **Web** y **Aplicación ASP.NET Web** , escriba el nombre **AppBackend** y haga clic en **Aceptar**.

    ![][1]

3.  En el cuadro de diálogo **Nuevo proyecto ASP.NET**, haga clic en **Web API** y, a continuación, en **Aceptar**.

    ![][2]

4.  En el cuadro de diálogo **Configurar sitio de Azure**, seleccione una suscripción, una región y una base de datos para utilizarlas con este proyecto. A continuación, haga clic en **Aceptar** para crear el proyecto.

    ![][3]

5.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **AppBackend** y, a continuación, seleccione **Administrar paquetes de NuGet**.

6.  A la izquierda, haga clic en **En línea**.

7.  En el cuadro **Buscar**, escriba **bus servicio**.

8.  En la lista de resultados, haga clic en **Bus de servicio de Windows Azure** y, a continuación, haga clic en **Instalar**. Complete la instalación y, a continuación, cierre la ventana del administrador de paquetes de NuGet.

    ![][4]

9.  En el Explorador de soluciones, haga clic con el botón secundario en la carpeta **Modelos**, a continuación en **Agregar** y, por último, en **Clase**. Llame a esta nueva clase **Notificationes.cs**. Haga clic en **Agregar** para generar la clase. Este módulo representa las diferentes notificaciones seguras que se enviarán. En una implementación completa, las notificaciones se almacenan en una base de datos. En este caso, las almacenaremos en la memoria para simplificar el proceso.

    ![][5]

10. Agregue código a Notifications.cs, sustituyendo la definición de clase `Notifications` por lo siguiente:

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. Agregue la siguiente instrucción `using` en la parte superior del archivo:

        using Microsoft.ServiceBus.Notifications;

12. En el método `Notifications()`, reemplace los dos marcadores de posición de la siguiente línea de código por la cadena de conexión (con acceso completo) para su centro de notificaciones y el nombre del centro. Puede obtener estos valores desde el [Portal de administración de Azure][Portal de administración de Azure]:

        Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");

13. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **AppBackend**, a continuación en **Agregar** y, por último, en **Clase**. Llame a esta nueva clase **AuthenticationTestHandler.cs**. Haga clic en **Agregar** para generar la clase. Esta clase se utiliza para autenticar a los usuarios de la *Autenticación básica*. Tenga en cuenta que su aplicación puede utilizar cualquier esquema de autenticación.

14. Agregue código a AuthenticationTestHandler.cs, sustituyendo la definición de clase `AuthenticationTestHandler` por lo siguiente:

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

    > [AZURE.NOTE] **Nota de seguridad**: Laclase `AuthenticationTestHandler` no proporciona una autenticación verdadera. Se utiliza únicamente para simular una autenticación básica y devolver un principio. Se requiere el nombre de usuario para crear registros del Centro de notificaciones. La implementación anterior no es segura. Debe implementar un mecanismo de autenticación seguro en las aplicaciones y servicios de producción.

15. Agregue las siguientes instrucciones `using` en la parte superior del archivo AuthenticationTestHandler.cs:

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;               

16. Agregue el siguiente código al final del método `Register` en la clase **App\_Start/WebApiConfig.cs**:

        config.MessageHandlers.Add(new AuthenticationTestHandler());

17. En el Explorador de soluciones, haga clic con el botón secundario en la carpeta **Controladores**, a continuación en **Agregar** y, por último, en **Controlador**. Haga clic en **Controlador Web API 2 - Vacío** y, a continuación, haga clic en **Agregar**.

    ![][6]

18. Llame a la nueva clase **RegisterController** y, a continuación, haga clic de nuevo en **Agregar** para generar el controlador.

    ![][7]

19. Agregue el siguiente código a la definición de clase `RegisterController`:

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

20. Agregue las siguientes instrucciones `using` en la parte superior del archivo RegisterController.cs:

        using Microsoft.ServiceBus.Notifications;
        using SecurePush.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

21. Observe que, en este código, agregamos la etiqueta de usuario al usuario autenticado por el controlador. También puede realizar comprobaciones opcionales para comprobar que el usuario puede registrarse para las etiquetas solicitadas.

22. En el Explorador de soluciones, haga clic con el botón secundario en la carpeta **Controladores**, a continuación en **Agregar** y, por último, en **Controlador**. Haga clic en **Controlador Web API 2 - Vacío** y, a continuación, haga clic en **Agregar**. Llame a la nueva clase **NotificationsController** y, a continuación, haga clic de nuevo en **Agregar** para generar el controlador. Este componente dota al dispositivo de una ruta para recuperar la notificación de forma segura, y además ofrece a un usuario una manera (para los fines de este tutorial) de desencadenar una inserción segura en sus dispositivos. Tenga en cuenta que al enviar la notificación al Centro de notificaciones, enviamos una notificación sin procesar solo con el identificador de la notificación (no el mensaje real).

23. Agregue el siguiente código a la definición de clase **NotificationsController**:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);

        }

24. Agregue las siguientes instrucciones `using` en la parte superior del archivo NotificationsController.cs:

        using SecurePush.Models;
        using System.Threading.Tasks;
        using System.Web;

25. Presione **F5** para ejecutar la aplicación y comprobar que hasta ahora todo es correcto. La aplicación debería iniciar un explorador web y mostrar la página principal de ASP.NET.

26. Ahora implementaremos esta aplicación en un sitio web de Azure a fin de que sea accesible para todos los dispositivos. Haga clic con el botón secundario en el proyecto **AppBackend** y, a continuación, seleccione **Publicar**.

27. Seleccione Sitio web Azure como destino de publicación.

    ![][8]

28. Inicie sesión con su cuenta de Azure y seleccione un sitio web nuevo o existente.

    ![][9]

29. Tome nota de la propiedad **URL de destino** en la pestaña **Conexión**. Más tarde en este tutorial haremos referencia a esta URL como *extremo de backend*. Haga clic en **Publicar**.

    ![][10]

  [0]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
  [1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
  [2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
  [3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
  [4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
  [5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
  [7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
  [8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
  [9]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
  [10]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
