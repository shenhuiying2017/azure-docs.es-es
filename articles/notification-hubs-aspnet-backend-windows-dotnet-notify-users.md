<properties title="Azure Notification Hubs Notify Users" pageTitle="Notificación a los usuarios de Centros de notificaciones de Azure" metaKeywords="Azure push notifications, Azure notification hubs" description="Obtenga información acerca de cómo enviar notificaciones de inserción en Azure. Ejemplos de código escritos en C# con la API de .NET." documentationCenter="" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="glenga" manager="dwrede" services="notification-hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

#Notificación a los usuarios de Centros de notificaciones de Azure

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/en-us/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universal</a><a href="/en-us/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/en-us/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

La compatibilidad con las notificaciones de inserción en Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones de inserción tanto en aplicaciones de consumidor, como en aplicaciones empresariales para plataformas móviles. Este tutorial muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a un usuario de aplicaciones determinado en un dispositivo concreto. Un backend de ASP.NET WebAPI se usa para autenticar clientes y generar notificaciones, tal como se muestra en el tema de referencia [Registro desde el backend de la aplicación](http://msdn.microsoft.com/en-us/library/dn743807.aspx). Este tutorial se basa en el centro de notificaciones que creó en el tutorial **Introducción a los Centros de notificaciones**.

Este tutorial también es el requisito previo para el tutorial **Inserción segura**. Una vez completados los pasos de este tutorial **Notificación a los usuarios**, puede continuar con el tutorial **Inserción segura**, en el que se muestra cómo modificar el código de **Notificación a los usuarios** para enviar una notificación de inserción de forma segura. 

> [AZURE.NOTE] Este tutorial asume que ha creado y configurado el centro de notificaciones tal como se describe en [Introducción a los Centros de notificaciones (Tienda Windows)](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/).
> Si usa Servicios móviles como su servicio backend, consulte la [versión de Servicios móviles](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/) de este tutorial.
>Asimismo, tenga en cuenta que este tutorial creará una aplicación Windows Phone Store 8.1. Puede usar el mismo código para aplicaciones universales de Windows y de la Tienda Windows. Todas estas aplicaciones tienen que usar credenciales de Windows (no de Windows Phone).

## Creación y configuración del centro de notificaciones

Antes de empezar este tutorial, debe reservar un nombre de aplicación y después crear un Centro de notificaciones de Azure y conectarlo a esa aplicación. Siga los pasos de [Introducción a los Centros de notificaciones (Tienda Windows)](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/); especialmente las secciones [Registro de la aplicación para la Tienda Windows](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#register) y [Configuración de su Centro de notificaciones](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub). En concreto, asegúrese de que ha especificado los valores de **SID del paquete** y **Secreto de cliente** en el portal, en la pestaña **Configurar** correspondiente a su centro de notificaciones. Este procedimiento de configuración se describe en la sección [Configuración de su Centro de notificaciones](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub). Este es un paso importante: si las credenciales del portal no coinciden con las especificadas para el nombre de la aplicación que elija, la notificación de inserción no tendrá lugar.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Creación del proyecto de Windows Phone

El paso siguiente es crear la aplicación Windows Phone. Para agregar este proyecto a la solución actual, realice las siguientes tareas:

1. En el Explorador de soluciones, haga clic con el botón secundario en el nodo de nivel superior de la solución (**solución NotifyUsers** en este caso), después haga clic en **Agregar** y, por último, haga clic en **Nuevo proyecto**.

2. Expanda **Aplicaciones de la Tienda**, haga clic en **Aplicaciones Windows Phone** y después en **Aplicación en blanco (Windows Phone)**.

	![][9]

3. En el cuadro **Nombre**, escriba **NotifyUserWindowsPhone** y haga clic en **Aceptar** para generar el proyecto.

 
4. Asocie esta aplicación a la Tienda Windows Phone: en el Explorador de soluciones, haga clic con el botón secundario en **NotifyUserWindowsPhone (Windows Phone 8.1)**, después haga clic en **Tienda** y, a continuación, en **Asociar aplicación con la Tienda...**.

	![][10]
 
5. Siga los pasos del asistente para iniciar sesión y asociar la aplicación con la Tienda.

	![][11]
	
	> [AZURE.NOTE] Asegúrese de anotar el nombre de la aplicación que elija durante este procedimiento. Debe configurar el centro de notificaciones en el portal usando las credenciales que obtenga desde el [Centro de desarrollo de Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409) para este nombre de aplicación reservado específico. Este procedimiento de configuración se describe en [Configuración de su Centro de notificaciones](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub). Este es un paso importante: si las credenciales del portal no coinciden con las especificadas para el nombre de la aplicación que elija, la notificación de inserción no tendrá lugar.

6. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **NotifyUserWindowsPhone (Windows Phone 8.1)** y, a continuación, haga clic en **Administrar paquetes NuGet**.

7. A la izquierda, haga clic en **En línea**.

8. En el cuadro **Buscar**, escriba **Cliente http**.

9. En la lista de resultados, haga clic en **Bibliotecas de cliente HTTP de Microsoft** y, a continuación, haga clic en **Instalar**. Complete la instalación.

10. Nuevamente en el cuadro **Buscar** de NuGet, escriba **Json.net**. Instale el paquete **Json.NET** y, a continuación, cierre la ventana Administrador de paquetes NuGet.

11. En el Explorador de soluciones, en el proyecto **NotifyUserWindowsPhone (Windows Phone 8.1)**, haga doble clic en **MainPage.xaml** para abrirlo en el editor de Visual Studio.

12. En el código XML **MainPage.xaml**, reemplace la sección `<Grid>` por el siguiente código:

		<Grid>
	        <Grid.RowDefinitions>
	            <RowDefinition Height="Auto"/>
	            <RowDefinition Height="*"/>
	        </Grid.RowDefinitions>

	        <TextBlock Grid.Row="0" Text="Secure Push" HorizontalAlignment="Center" FontSize="48"/>

        	<StackPanel Grid.Row="1" VerticalAlignment="Center">
        	    <Grid>
        	        <Grid.RowDefinitions>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="*"/>
        	        </Grid.RowDefinitions>
            	    <TextBlock Grid.Row="0" Text="Username" FontSize="24" Margin="20,0,20,0"/>
            	    <TextBox Name="UsernameTextBox" Grid.Row="1" Margin="20,0,20,0"/>
            	    <TextBlock Grid.Row="2" Text="Password" FontSize="24" Margin="20,0,20,0" />
            	    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Margin="20,0,20,0"/>
	
            	    <Button Grid.Row="4" HorizontalAlignment="Center" VerticalAlignment="Center" Content="1. Login and register" Click="LoginAndRegisterClick" />

            	    <Button Grid.Row="5" HorizontalAlignment="Center" VerticalAlignment="Center" Content="2. Send push" Click="PushClick" />
            	</Grid>
        	</StackPanel>
    	</Grid>


13. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **NotifyUserWindowsPhone (Windows Phone 8.1)** y, a continuación, haga clic en **Agregar**y finalmente en **Clase**. Asigne un nombre a la clase **RegisterClient.cs** y después haga clic en **Aceptar** para generar la clase. Este componente implementa las llamadas REST requeridas para ponerse en contacto con el back-end de la aplicación con la finalidad de registrar notificaciones de inserción. También almacena localmente los *registrationIds* creados por el Centro de notificaciones tal como se detalla en [Registro desde el backend de la aplicación](http://msdn.microsoft.com/en-us/library/dn743807.aspx). Tenga en cuenta que usa un token de autorización almacenado localmente cuando hace clic en el botón **Iniciar sesión y registrarse**.

14. Agregue el siguiente código a la definición de clase `RegisterClient`. Asegúrese de reemplazar `{backend endpoint}` por el extremo backend obtenido en la sección anterior:

		private string POST_URL = "{backend endpoint}/api/register";

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        public async Task RegisterAsync(string handle, IEnumerable<string> tags)
        {
            var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

            var deviceRegistration = new DeviceRegistration
            {
                Platform = "wns",
                Handle = handle,
                Tags = tags.ToArray<string>()
            };

            var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

            if (statusCode == HttpStatusCode.Gone)
            {
                // regId is expired, deleting from local storage & recreating
                var settings = ApplicationData.Current.LocalSettings.Values;
                settings.Remove("__NHRegistrationId");
                regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
                statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
            }

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
            }
        }

        private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
        {
            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                var putUri = POST_URL + "/" + regId;

                string json = JsonConvert.SerializeObject(deviceRegistration);
                                var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
                return response.StatusCode;
            }
        }

        private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            if (!settings.ContainsKey("__NHRegistrationId"))
            {
                using (var httpClient = new HttpClient())
                {
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                    if (response.IsSuccessStatusCode)
                    {
                        string regId = await response.Content.ReadAsStringAsync();
                        regId = regId.Substring(1, regId.Length - 2);
                        settings.Add("__NHRegistrationId", regId);
                    }
                    else
                    {
                        throw new Exception();
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }


15. Agregue las siguientes instrucciones `using` en la parte superior del archivo RegisterClient.cs:

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		
16. Agregue código para los botones en MainPage.xaml.cs. La devolución de llamada para **Iniciar sesión y registrarse** almacena el token de autenticación básica en el almacenamiento local (tenga en cuenta que este representa cualquier token que use su esquema de autenticación) y luego usa `RegisterClient` para llamar al backend. La devolución de llamada para **AppBackend** llama al back-end para activar una notificación segura en todos los dispositivos de este usuario. 

	Agregue el siguiente código a MainPage.xaml.cs después del método `OnNavigatedTo()`. Asegúrese de reemplazar `{backend endpoint}` por el extremo backend obtenido en la sección anterior:

		private async void PushClick(object sender, RoutedEventArgs e)
        {
            var POST_URL = "{backend endpoint}/api/notifications";

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                await httpClient.PostAsync(POST_URL, new StringContent(""));
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();
            
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await new RegisterClient().RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;
            
            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }


17. Agregue las siguientes instrucciones `using` en la parte superior del archivo MainPage.xaml.cs:

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

## Ejecución de la aplicación

Para ejecutar la aplicación, realice las siguientes tareas:

1. En Visual Studio, ejecute la aplicación de Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)**. El emulador de Windows Phone se ejecuta y carga la aplicación automáticamente.

2. En la interfaz de usuario de la aplicación **NotifyUserWindowsPhone**, escriba un nombre de usuario y contraseña. Esta información puede ser cualquier cadena, pero deben tener el mismo valor.

3. En la interfaz de usuario de la aplicación **NotifyUserWindowsPhone**, haga clic en **Iniciar sesión y registrarse**. A continuación, haga clic en **Enviar inserción**.


[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push13.png

<!--HONumber=35.1-->
