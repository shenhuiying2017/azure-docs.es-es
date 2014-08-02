<properties linkid="notification-hubs-how-to-guides-howto-notify-users-aspnet" urlDisplayName="Notify Users" pageTitle="Notify Users of your ASP.NET service with Notification Hubs" metaKeywords="" description="Follow this tutorial to register to receive notifications from your ASP.NET service by using Notification Hubs" metaCanonical="" services="notification-hubs" documentationCenter="" title="Notify users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Notificación a los usuarios con los Centros de notificaciones
=============================================================

[Servicios móviles](/en-us/manage/services/notification-hubs/notify-users "Servicios móviles")[ASP.NET](/en-us/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

Este tutorial muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a un usuario de aplicaciones determinado en un dispositivos concreto. Se utiliza un back-end de ASP.NET Web API para autenticar clientes y para generar notificaciones. Este tutorial se basa en el centro de notificaciones que ha creado en el tutorial anterior, **Introducción a los Centros de notificaciones**. El código de registro de notificación se desplaza del cliente al servicio back-end. Esto asegura que el registro solo se completa una vez que el servicio haya autenticado de forma positiva al cliente. También significa que las credenciales del centro de notificaciones no se distribuyen con la aplicación cliente. El servicio también controla las etiquetas solicitadas durante el registro.

Este tutorial le guiará a través de los siguientes pasos básicos:

-   [Creación de una aplicación ASP.NET con autenticación](#create-application)
-   [Actualización de la aplicación ASP.NET para registrar las notificaciones](#register-notification)
-   [Actualización de la aplicación para iniciar sesión y solicitar el registro](#update-app)

Requisitos previos
------------------

-   Visual Studio 2012. También puede usar Visual Studio Express 2012 para Web y Visual Studio Express 2012 para Windows 8 para crear la aplicación ASP.NET y la aplicación de la Tienda Windows, respectivamente.
-   Este tutorial se basa en la aplicación y el centro de notificaciones que creó en **Introducción a los Centros de notificaciones**. Antes de comenzar este tutorial, debe completar primero el tutorial **Introducción a los Centros de notificaciones** ([C\# de Tienda Windows](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/en-us/manage/services/notification-hubs/get-started-notification-hubs-android)).

**Nota:**

El proyecto ASP.NET Web API que ha creado en este tutorial se ejecuta en el equipo local. También puede publicar un proyecto de ASP.NET Web API en Azure. Para obtener más información, consulte [Creación de un servicio REST para móviles mediante ASP.NET Web API y Base de datos SQL](/en-us/develop/net/tutorials/rest-service-using-web-api/).

Creación de la aplicación ASP.NETCreación de una aplicación ASP.NET con autenticación
-------------------------------------------------------------------------------------

En primer lugar, creará una aplicación ASP.NET Web API. Este servicio back-end autenticará clientes, registrará las notificaciones de inserción en nombre de un usuario autenticado y enviará notificaciones.

1.  En Visual Studio o Visual Studio Express 2012 para Web, haga clic en **Archivo**, a continuación en **Nuevo proyecto en el menú Archivo, amplíe **Plantillas**, **Visual C\#**, después haga clic en **Web** y en **Aplicación web de ASP.NET MVC 4**, introduzca el nombre *NotificationService* y haga clic en **OK**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-mvc-app.png)

2.  En el cuadro de diálogo **New ASP.NET MVC Project**, haga clic en **Empty** y, a continuación, en **OK**.

    Se crea un proyecto ASP.NET MVC.

3.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Add** y después en **Class**, a continuación escriba `AuthenticationTestHandler` y haga clic en **Add**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-aspnet-class.png)

    Se agrega un archivo de código al proyecto para la clase **AuthenticationTestHandler**.

4.  Abra el nuevo archivo de proyecto AuthenticationTestHandler.cs y reemplace la definición de clase por el código siguiente:

         using System;
         using System.Collections.Generic;
         using System.Linq;
         using System.Net;
         using System.Net.Http;
         using System.Threading;
         using System.Threading.Tasks;
         using System.Security.Principal;
         using System.Text;
         using System.Web;
            
         namespace NotificationService
         {
             public class AuthenticationTestHandler : DelegatingHandler
             {
                 protected override Task SendAsync(
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
                             // Adjuntar el nuevo objeto principal al objeto HttpContext actual
                             HttpContext.Current.User = 
                                 new GenericPrincipal(new GenericIdentity(user), new string[0]);
                             System.Threading.Thread.CurrentPrincipal = 
                                 System.Web.HttpContext.Current.User;
                         }
                         else return Unauthorised();
                     } else return Unauthorised();
            
                     return base.SendAsync(request, cancellationToken);
                 }
            
                 private bool verifyUserAndPwd(string user, string password)
                 {
                     // No es un esquema de autenticación real.
                     return user == password;
                 }
            
                 private Task Unauthorised()
                 {
                     var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
                     var tsc = new TaskCompletionSource();
                     tsc.SetResult(response);
                     return tsc.Task;
                 }
             }
         } 

    **Nota de seguridad**

    La clase **AuthenticationTestHandler** no proporciona una autenticación verdadera. Se utiliza únicamente para simular una autenticación básica y devolver un principio. Se requiere el nombre de usuario para crear registros del Centro de notificaciones. La implementación anterior no es segura. Debe implementar un mecanismo de autenticación seguro en las aplicaciones y servicios de producción.

5.  Expanda la carpeta **App\_Start**, abra el archivo WebApiConfig.cs y agregue la siguiente línea de código al final del método **Register**:

         config.MessageHandlers.Add(new AuthenticationTestHandler());

    Este código requiere que las solicitudes a la aplicación ASP.NET contengan el encabezado Authorization.

Ahora ya hemos creado la aplicación básica con un esquema de autenticación ficticio que nos proporcionará un nombre de usuario.

Registro de notificacionesActualización de la aplicación ASP.NET para registrar las notificaciones
--------------------------------------------------------------------------------------------------

El paso siguiente consiste en agregar la lógica del registro de los centros de notificaciones a la aplicación ASP.NET mediante la creación de un nuevo controlador **Registration**.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Bus de servicio**, su espacio de nombres, **Centros de notificaciones**, luego seleccione su centro den notificaciones y haga clic en **Connection Information**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-select-hub-connection.png)

2.  Anote el nombre de su centro de notificaciones y copie la cadena de conexión para **DefaultFullSharedAccessSignature**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-connection-strings.png)

    Utilizará esta cadena de conexión, junto con el nombre del centro de notificaciones, tanto para registrar como para enviar notificaciones.

3.  En el **Explorador de soluciones**, haga clic con el botón secundario en el nombre de proyecto y, a continuación, seleccione **Manage NuGet Packages**.

4.  En el panel izquierdo, seleccione la categoría **Online**, busque `WindowsAzure.ServiceBus`, haga clic en **Install** en el paquete **Bus de servicio de Azure** y acepte el contrato de licencia.

	![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-nuget-package.png) 

	Esto agrega el ensamblado Microsoft.ServiceBus.dll al proyecto.

1.  En el Explorador de soluciones, haga clic con el botón secundario en la carpeta **Controladores**, haga clic en **Add**, después en **Controller...**, escriba `RegisterController` en **Controller name**, elija **Empty API controller** y, finalmente, haga clic en **Add**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-register-controller2.png)

    Se agrega una clase de controlador al proyecto. Este controlador, cuando se invoca, se encargará de registrar un dispositivo con los Centros de notificaciones.

2.  Abra el nuevo archivo el proyecto RegisterController.cs y agregue las siguientes instrucciones **using**.

         using Microsoft.ServiceBus.Notifications;
         using Newtonsoft.Json.Linq;
         using System.Threading.Tasks;
         using System.Web;

3.  Agregue el siguiente código a la nueva clase RegisterController:

         // Define the Notification Hubs client.
		private NotificationHubClient hubClient;

		// Create the client in the constructor.
        public RegisterController()
        {
            var cn = "<FULL_SAS_CONNECTION_STRING>";
            hubClient = NotificationHubClient
				.CreateClientFromConnectionString(cn, "<NOTIFICATION_HUB_NAME>");
         }

4.  Actualice el código del constructor para reemplazar _`<NOTIFICATION_HUB_NAME>`_ y _`<FULL_SAS_CONNECTION_STRING>`_ por valores para su centro de notificaciones y, a continuación, haga clic en **Save**.

    **Nota:**

    Asegúrese de utilizar **DefaultFullSharedAccessSignature** para _`<FULL_SAS_CONNECTION_STRING>`_. Esta notificación permite a la API web crear y actualizar registros.

5.  Agregue el siguiente código del método Post a la clase RegisterController:

         public async Task<RegistrationDescription> Post([FromBody]JObject registrationCall)
        {
            // Get the registration info that we need from the request. 
            var platform = registrationCall["platform"].ToString();
            var installationId = registrationCall["instId"].ToString();
            var channelUri = registrationCall["channelUri"] != null ? 
                registrationCall["channelUri"].ToString() : null;
            var deviceToken = registrationCall["deviceToken"] != null ? 
                registrationCall["deviceToken"].ToString() : null;       
            var userName = HttpContext.Current.User.Identity.Name;

            // Get registrations for the current installation ID.
            var regsForInstId = await hubClient.GetRegistrationsByTagAsync(installationId, 100);

            bool updated = false;
            bool firstRegistration = true;
            RegistrationDescription registration = null;

            // Check for existing registrations.
            foreach (var registrationDescription in regsForInstId)
            {
                if (firstRegistration)
                {
 					// Update the tags.
                    registrationDescription.Tags = new HashSet<string>() { installationId, userName };

                    // We need to handle each platform separately.
                    switch (platform)
                    {
                        case "windows":
                            var winReg = registrationDescription as WindowsRegistrationDescription;
                            winReg.ChannelUri = new Uri(channelUri);
                            registration = await hubClient.UpdateRegistrationAsync(winReg);                            
                            break;
                        case "ios":
                            var iosReg = registrationDescription as AppleRegistrationDescription;
                            iosReg.DeviceToken = deviceToken;
                            registration = await hubClient.UpdateRegistrationAsync(iosReg);
                            break;
                    }
                    updated = true;
                    firstRegistration = false;
                }
                else
                {
                    // We shouldn't have any extra registrations; delete if we do.
                    await hubClient.DeleteRegistrationAsync(registrationDescription);
                }
            }

            // Create a new registration.
            if (!updated)
            {
                switch (platform)
                {
                    case "windows":
                        registration = await hubClient.CreateWindowsNativeRegistrationAsync(channelUri, 
                            new string[] { installationId, userName });
                        break;
                    case "ios":
                        registration = await hubClient.CreateAppleNativeRegistrationAsync(deviceToken, 
                            new string[] { installationId, userName });
                        break;
                }
            }

            // Send out a test notification.
            sendNotification(string.Format("Test notification for {0}", userName), userName);

            return registration;
        }

    Este código lo invoca una solicitud POST y obtiene la información de la plataforma y del identificador del dispositivo del cuerpo de mensaje. Estos datos, junto con el identificador de instalación del encabezado de solicitud y el identificador de usuario del usuario que ha iniciado sesión, se utilizan para actualizar un registro. Si un registro no existe, se crea uno nuevo. Este registro se etiqueta con el identificador de usuario y el identificador de instalación.

6.  Agregue el siguiente método sendNotification:

        // Implementación básica que envía una notificación a los clientes de aplicaciones iOS y Tienda Windows.
        private async Task sendNotification(string notificationText, string tag)
        {
            try
            {
                // Cree notificaciones para ambas plataformas, Tienda Windows y iOS.
                var toast = @"" +
                    notificationText + "";
                var alert = "{\"aps\":{\"alert\":\"" + notificationText + 
                    "\"}, \"inAppMessage\":\"" + notificationText + "\"}";

                // Envíe una notificación al usuario que ha iniciado sesión en ambas plataformas.
                await hubClient.SendWindowsNativeNotificationAsync(toast, tag);
                await hubClient.SendAppleNativeNotificationAsync(alert, tag);
            }
            catch(ArgumentException ex)
            {
                // Se espera cuando un registro APNS no existe.
                Console.WriteLine(ex.Message);
            }
        }

    Este método se llama para enviar una notificación inmediatamente cuando se completa el registro.

Después utilizaremos la aplicación cliente que ha creado al completar el tutorial **Introducción a los Centros de notificaciones**.

Actualización de la aplicaciónActualización de la aplicación para iniciar sesión y solicitar el registro
--------------------------------------------------------------------------------------------------------

La aplicación que ha creado cuando completó el tutorial **Introducción a los Centros de notificaciones** solicita el registro directamente desde el centro de notificaciones. Quitará este código de registro de la aplicación cliente y lo reemplazará por una llamada a la nueva API de registro en la aplicación ASP.NET Web API.

1.  Presione F5 para iniciar la aplicación ASP.NET e intente cargar la página predeterminada.

    Cuando se muestra el explorador, tome nota del nombre de host del sitio solicitado. Necesitará esta dirección URL raíz cuando actualice la aplicación cliente.

    **Nota:**

    Si está utilizando el servidor web de IIS o el Servidor de desarrollo de Visual Studio, también puede especificar el número de puerto. Tenga en cuenta que se devuelve un error 404 porque no ha implementado una página predeterminada en esa aplicación.

2.  Siga los pasos de una de las versiones siguientes de **Registro del usuario actual para notificaciones de inserción mediante ASP.NET Web API**, dependiendo de la plataforma de cliente:

    -   [Versión C\# de Tienda Windows](/en-us/manage/services/notification-hubs/register-users-aspnet-dotnet)
    -   [Versión de iOS](/en-us/manage/services/notification-hubs/howto-register-user-with-aspnet-ios)

3.  Ejecute la aplicación actualizada, inicie sesión con el servicio utilizando la misma cadena para el nombre de usuario y contraseńa y, a continuación, verifique que se muestra el identificador de registro asignado.

    También recibirá una notificación de inserción.

    **Nota:**

    Se genera un error en el back-end cuando no hay ningún registro de una plataforma para la cual se solicita se envíe una notificación. En este caso, se puede ignorar el error. Para ver cómo utilizar las plantillas con el fin de evitar esta situación, consulte [Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet).

4.  (Opcional) Implemente la aplicación cliente en un segundo dispositivo y después ejecute la aplicación e inserte el texto.

    Se mostrará una notificación en cada dispositivo.

Pasos siguientes
----------------

Ahora que ha completado este tutorial, considere la realización de los tutoriales siguientes:

-   **Uso de los Centros de notificaciones para enviar noticias de última hora ([Versión C\# de Tienda Windows](/en-us/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/en-us/manage/services/notification-hubs/breaking-news-dotnet))**
    Este tutorial específico de plataforma muestra cómo utilizar las etiquetas para que los usuarios puedan suscribirse a los tipos de notificaciones en los que están interesados.

-   **[Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet)**
    Este tutorial amplía el tutorial actual **Notificación a los usuarios con los Centros de notificaciones** para utilizar plantillas específicas de plataforma para registrarse para las notificaciones. Esto le permite enviar notificaciones desde un único método en su código del servidor.

Para obtener más información acerca de los Centros de notificaciones, consulte [Centros de notificaciones de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj927170.aspx).

