<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-aspnet-webapi-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Web API" pageTitle="Register the current user for push notifications by using Web API - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by ASP.NET Web API." metaCanonical="" services="notification-hubs" documentationCenter="" title="Register the current user for push notifications by using ASP.NET" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registro del usuario actual para las notificaciones de inserción mediante ASP.NET

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a><a href="/es-es/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/" title="iOS">iOS</a>
</div>

En este tema se describe cómo solicitar el registro de las notificaciones de inserción con los Centros de notificaciones de Azure al realizar el registro mediante ASP.NET Web API. Este tema amplía el tutorial [Notificación a los usuarios con los Centros de notificaciones][]. Debe haber completado ya los pasos necesarios de ese tutorial para crear el servicio móvil autenticado. Para obtener más información acerca del escenario de notificación a los usuarios, consulte [Notificación a los usuarios con los Centros de notificaciones][].

1.  En Visual Studio 2012, abra el archivo app.xaml.cs del proyecto que creó al finalizar el tutorial [Notificación a los usuarios con los Centros de notificaciones][], completado como requisito previo.

2.  Busque el método **InitNotificationsAsync** y añada comentarios al código.

    Se usará ASP.NET Web API para registrar las notificaciones.

3.  En el **Explorador de soluciones**, haga clic con el botón secundario en el nombre de proyecto y, a continuación, seleccione **Manage NuGet Packages**.

4.  En el panel izquierdo, seleccione la categoría **Online**, busque `json.net`, haga clic en **Install** en el paquete **Json.NET** y acepte el contrato de licencia.

    ![][]

    De este modo, se agrega el ensamblado Newtonsoft.Json.dll de terceros al proyecto.

5.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Agregar** y después en **Clase**; a continuación, escriba `LocalStorageManager` y haga clic en **Agregar**.

    ![][1]

    Se agrega un archivo de código al proyecto para la clase **LocalStorageManager**.

6.  Abra el nuevo archivo de proyecto LocalStorageManager.cs y agregue la siguiente instrucción **using**:

        using Windows.Storage;

7.  Reemplace la definición de clase **LocalStorageManager** por el código siguiente:

        class LocalStorageManager
        {
            private static ApplicationDataContainer GetLocalStorageContainer()
            {
                if (!ApplicationData.Current.LocalSettings
                    .Containers.ContainsKey("InstallationContainer"))
                {
                    ApplicationData.Current.LocalSettings
                        .CreateContainer("InstallationContainer",                                                            
                        ApplicationDataCreateDisposition.Always);
                }
                return ApplicationData.Current.LocalSettings
                    .Containers["InstallationContainer"];
            }

            public static string GetInstallationId()
            {
                var container = GetLocalStorageContainer();
                if (!container.Values.ContainsKey("InstallationId"))
                {
                    container.Values["InstallationId"] = Guid.NewGuid().ToString();
                }
                return (string)container.Values["InstallationId"];
            }
        }

    Este código sirve para crear y almacenar un identificador de instalación específico del dispositivo, que se usa como etiqueta al crear notificaciones. Si ya existe un identificador de la instalación, se usará este último en su lugar.

8.  Abra el archivo de proyecto MainPage.xaml y reemplace el elemento **Grid** raíz por el siguiente código XAML:

        <Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
            <Grid Margin="120, 58, 120, 80" >
                <Grid.RowDefinitions>
                    <RowDefinition />
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition MaxWidth="200"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  
                               TextWrapping="Wrap" Text="Push To Users with Notification Hubs" 
                               FontSize="42"  VerticalAlignment="Top"/>
                <TextBlock Grid.Row="1" Grid.Column="0" Text="Installation Id:" 
                               FontSize="24" VerticalAlignment="Center" />
                <TextBlock Grid.Row="1" Grid.Column="1" Name="InstId" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBlock Grid.Row="2" Grid.Column="0" Text="Username:" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBox Grid.Row="2" Grid.Column="1" Name="User" FontSize="24" 
                             Width="300" Height="40" HorizontalAlignment="Left"/>
                <TextBlock Grid.Row="3" Grid.Column="0" Text="Password:" FontSize="24" 
                               VerticalAlignment="Center" />
                <PasswordBox Grid.Row="3" Grid.Column="1" Name="Password" FontSize="24" 
                                 Width="300" Height="40" HorizontalAlignment="Left"/>
                <Button Name="Login" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" 
                            Content="Login and Register for Push" FontSize="24" Click="Login_Click" />
            </Grid>
        </Grid>

    Este código crea una interfaz de usuario para recopilar un nombre de usuario y una contraseña y mostrar el identificador de instalación.

9.  De vuelta en el archivo MainPage.xaml.cs, agregue las siguientes directivas **using**:

        using Newtonsoft.Json;
        using System.Net.Http;
        using System.Text;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

10. Agregue la siguiente definición a la clase **MainPage**:

        private string registerUri = "https://<SERVICE_ROOT_URL>/api/register";

    Reemplace *`<SERVICE_ROOT_URL>`* por el URI raíz de la Web API que creó en **Notificación a los usuarios con los Centros de notificaciones**.

11. Agregue el método siguiente a la clase **MainPage**:

        private async void Login_Click(object sender, RoutedEventArgs e)
        {
            // Get the info that we need to request registration.
            var installationId = InstId.Text = LocalStorageManager.GetInstallationId();
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();
            var user = User.Text;
            var pwd = Password.Password;

            // Define a registration to pass in the body of the POST request.
            var registration = new Dictionary<string, string>()
                                   {{"platform", "windows"},
                                   {"instId", installationId.ToString()},
                                   {"channelUri", channel.Uri}};

            // Create a client to send the HTTP registration request.
            var client = new HttpClient();
            var request =
                new HttpRequestMessage(HttpMethod.Post, new Uri(registerUri));

            // Add the Authorization header with the basic login credentials.
            var auth = "Basic " +
                Convert.ToBase64String(Encoding.UTF8.GetBytes(user + ":" + pwd));
            request.Headers.Add("Authorization", auth);
            request.Content = new StringContent(JsonConvert.SerializeObject(registration),
                Encoding.UTF8, "application/json");

            string message;

            try
            {
                // Send the registration request.
                HttpResponseMessage response = await client.SendAsync(request);

                // Get and display the response, either the registration ID
                // or an error message.
                message = await response.Content.ReadAsStringAsync();
                message = string.Format("Registration ID: {0}", message);
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Este método obtiene un ID de instalación y un canal para las notificaciones de inserción y los envía, junto con el tipo de dispositivo, al método de Web API autenticado que crea un registro en los Centros de notificaciones. Esta Web API se definió en [Notificación a los usuarios con los Centros de notificaciones][].

Ahora que la aplicación de cliente se ha actualizado, regrese a [Notificación a los usuarios con los Centros de notificaciones][] y actualice el servicio móvil para enviar notificaciones mediante Centros de notificaciones.


  [C# para Tienda Windows]: /es-es/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/ "C# para Tienda Windows"
  [iOS]: /es-es/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/ "iOS"
  [Notificación a los usuarios con los Centros de notificaciones]: /es-es/manage/services/notification-hubs/notify-users-aspnet
  []: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-add-nuget-package-json.png
  [1]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-create-aspnet-class.png
