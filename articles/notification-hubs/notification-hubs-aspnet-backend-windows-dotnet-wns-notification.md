---
title: Envío de notificaciones a usuarios concretos mediante Azure Notification Hubs | Microsoft Docs
description: Aprenda a enviar notificaciones a usuarios concretos que usen aplicaciones de la Plataforma universal de Windows (UWP).
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.author: dimazaid
ms.openlocfilehash: ee0e78402515a733731e6faf21b09886674cda51
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>Tutorial: Envío de notificaciones a usuarios concretos mediante Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Información general
Este tutorial muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a un usuario de aplicaciones determinado en un dispositivo concreto. Se usa un back-end de WebAPI de ASP.NET para autenticar a los clientes. Cuando el back-end autentica un usuario de la aplicación cliente, agrega automáticamente una etiqueta al registro de notificaciones. El back-end utiliza dicha etiqueta para enviar notificaciones al usuario concreto. 

> [!NOTE]
> El código completo de este tutorial se puede encontrar en [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). 

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Crear el proyecto WebAPI
> * Autenticar clientes en el back-end de WebAPI
> * Registrar notificaciones mediante el back-end de WebAPI
> * Enviar notificaciones desde el back-end de WebAPI
> * Publicar del nuevo back-end de WebAPI
> * Actualizar el código para el proyecto de cliente
> * Probar la aplicación


## <a name="prerequisites"></a>requisitos previos
Este tutorial se basa en el Centro de notificaciones y en el proyecto de Visual Studio que creó en el tutorial [Introducción a Notification Hubs para aplicaciones de la plataforma universal de Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Por consiguiente, complételo antes de iniciar este. 

> [!NOTE]
> Si usa Mobile Apps como su servicio back-end en Azure App Service, consulte la [versión de Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) de este tutorial.


&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>Actualización del código para el proyecto de cliente
En esta sección se actualiza el código del proyecto que completó para el tutorial [Introducción a Azure Notification Hubs para aplicaciones Windows Phone](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). El proyecto ya debe estar asociado a la Tienda Windows. También debe estar configurado para usar el Centro de notificaciones. En esta sección, agregue código para llamar el nuevo back-end de WebAPI y usarlo para registrar y enviar notificaciones.

1. En Visual Studio, abra la solución que creó para el tutorial [Introducción a Azure Notification Hubs para aplicaciones Windows Phone](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **WindowsApp** y luego haga clic en **Administrar paquetes NuGet**.
3. A la izquierda, haga clic en **En línea**.
4. En el cuadro **Buscar**, escriba **Cliente http**.
5. En la lista de resultados, haga clic en **System.Net.Http**y haga clic en **Instalar**. Complete la instalación.
6. Nuevamente en el cuadro **Buscar** de NuGet, escriba **Json.net**. Instale el paquete **Newtonsoft.json** y cierre la ventana Administrador de paquetes NuGet.
8. En el Explorador de soluciones, en el proyecto **WindowsApp**, haga doble clic en **MainPage.xaml** para abrirlo en el editor de Visual Studio.
9. En el código XML de **MainPage.xaml**, reemplace la sección `<Grid>` por el código siguiente: este código agrega un cuadro de texto con el nombre de usuario y contraseña con que el usuario se autentica. También agrega cuadros de texto al mensaje de notificación y la etiqueta de nombre de usuario que debería recibir la notificación:

    ```xml   
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

        <StackPanel Grid.Row="1" VerticalAlignment="Center">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                            Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
            </Grid>
        </StackPanel>
    </Grid>
    ```
11. En el Explorador de soluciones, abra el archivo **MainPage.xaml.cs** para los proyectos **(Windows 8.1)** y **(Windows Phone 8.1)**. Agregue las siguientes instrucciones `using` en la parte superior de ambos archivos:

    ```csharp    
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```
12. En **MainPage.xaml.cs** para el proyecto **WindowsApp**, agregue el miembro siguiente a la clase `MainPage`. Asegúrese de reemplazar `<Enter Your Backend Endpoint>` por el punto de conexión del back-end obtenido anteriormente. Por ejemplo, `http://mybackend.azurewebsites.net`.
    
    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```
13. Agregue el código siguiente a la clase MainPage en **MainPage.xaml.cs** para los proyectos **(Windows 8.1)** y **(Windows Phone 8.1)**.
    
    El método `PushClick` es el controlador de clics para el botón **Enviar inserción** . Llama al back-end para desencadenar una notificación a todos los dispositivos con una etiqueta de nombre de usuario que coincida con el parámetro `to_tag` . El mensaje de notificación se envía como contenido JSON en el cuerpo de la solicitud.
    
    El método `LoginAndRegisterClick` es el controlador de clics del botón **Login and register** (Iniciar sesión y registrarse). Almacena el token de autenticación básico (representa cualquier token que usa el esquema de autenticación) localmente y después usa `RegisterClient` para registrar las notificaciones que usan el back-end.

    ```csharp
    private async void PushClick(object sender, RoutedEventArgs e)
    {
        if (toggleWNS.IsChecked.Value)
        {
            await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleGCM.IsChecked.Value)
        {
            await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleAPNS.IsChecked.Value)
        {
            await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

        }
    }

    private async Task sendPush(string pns, string userTag, string message)
    {
        var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
            pns + "&to_tag=" + userTag;

        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                alert.ShowAsync();
            }
        }
    }

    private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
    {
        SetAuthenticationTokenInLocalStorage();

        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // The "username:<user name>" tag gets automatically added by the message handler in the backend.
        // The tag passed here can be whatever other tags you may want to use.
        try
        {
            // The device handle used is different depending on the device and PNS. 
            // Windows devices use the channel uri as the PNS handle.
            await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            SendPushButton.IsEnabled = true;
        }
        catch (Exception ex)
        {
            MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
            alert.ShowAsync();
        }
    }

    private void SetAuthenticationTokenInLocalStorage()
    {
        string username = UsernameTextBox.Text;
        string password = PasswordTextBox.Password;

        var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
        ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
    }
    ```
1. Abra el archivo **App.xaml.cs**. Encuentre la llamada a `InitNotificationsAsync()` in the `OnLaunched()` . Marque como comentario o elimine la llamada a `InitNotificationsAsync()`. El controlador del botón inicializa los registros de notificaciones.

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```
1. Haga clic con el botón derecho en el proyecto **WindowsApp**, haga clic en **Agregar** y, después, en **Clase**. Asigne un nombre a la clase **RegisterClient.cs** y después haga clic en **Aceptar** para generar la clase.
   
   Esta clase contiene las llamadas REST requeridas para ponerse en contacto con el back-end de la aplicación con la finalidad de registrarlas para las notificaciones push. También almacena localmente los identificadores *registrationIds* creados por el Centro de notificaciones, como se detalla en la sección [Administración de registros desde un back-end](http://msdn.microsoft.com/library/dn743807.aspx). Usa un token de autorización almacenado localmente cuando hace clic en el botón **Login and register** (Iniciar sesión y registrarse).
2. Agregue las siguientes instrucciones `using` en la parte superior del archivo RegisterClient.cs:

    ```csharp   
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```
3. Agregue el siguiente código a la definición de clase `RegisterClient` .
   
    ```csharp
    private string POST_URL;

    private class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    public RegisterClient(string backendEndpoint)
    {
        POST_URL = backendEndpoint + "/api/register";
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

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
        {
            // log or throw
            throw new System.Net.WebException(statusCode.ToString());
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
                    throw new System.Net.WebException(response.StatusCode.ToString());
                }
            }
        }
        return (string)settings["__NHRegistrationId"];

    }
    ```
4. Guarde todos los cambios.

## <a name="test-the-application"></a>Prueba de la aplicación
1. Inicie la aplicación en ambas versiones de Windows.
2. Especifique un **nombre de usuario** y una **contraseña**, tal como se muestra en la pantalla siguiente. Debe diferir del nombre de usuario y contraseña que escriba en Windows Phone.
3. Haga clic en **Iniciar sesión y registrarse** y compruebe que el cuadro de diálogo se muestre que ha iniciado sesión. Este código también habilita el botón **Send Push** (Enviar inserción).
   
    ![][14]
5. Después, en el campo **Recipient Username Tag** (Etiqueta de nombre de usuario destinatario), especifique el nombre de usuario registrado. Escriba un mensaje de notificación y haga clic en **Enviar inserción**.
6. Solo los dispositivos que se han registrado con la etiqueta de nombre de usuario coincidente reciben el mensaje de notificación.
   
    ![][15]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a enviar notificaciones push a usuarios concretos que tienen etiquetas asociadas a sus registros. Para aprender a enviar notificaciones push en función de la ubicación, pase al tutorial siguiente: 

> [!div class="nextstepaction"]
>[Envío de notificaciones push en función de la ubicación](notification-hubs-push-bing-spartial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
