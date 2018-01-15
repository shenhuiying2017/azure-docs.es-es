---
title: "Incorporación de notificaciones push a una aplicación de la Plataforma universal de Windows (UWP) | Microsoft Docs"
description: "Obtenga información acerca de cómo usar Azure App Service Mobile Apps y Azure Notification Hubs para enviar notificaciones push a la aplicación de Universal Windows Platform (UWP)."
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: conceptdev
manager: crdun
editor: 
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: d51fa215ace8b33acb5d36b98e9a1a86e3296a65
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="add-push-notifications-to-your-windows-app"></a>Incorporación de notificaciones push a la aplicación de Windows
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Información general
En este tutorial, agregará notificaciones de inserción al proyecto de [inicio rápido de Windows](app-service-mobile-windows-store-dotnet-get-started.md) para que cada vez que se envíe una notificación de inserción al dispositivo, se inserte un registro.

Si no usa el proyecto de servidor de inicio rápido descargado, necesitará el paquete de extensión de la notificación de inserción. Vea [Trabajar con el SDK de servidor de back-end de .NET para Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obtener más información.

## <a name="configure-hub"></a>Configurar un Centro de notificaciones
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registro de la aplicación para notificaciones push
Debe enviar la aplicación a la Tienda Windows y, después, configurar el proyecto de servidor para integrarlo con los Servicios de notificaciones de Windows (WNS) para enviar la inserción.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto de aplicación UWP y luego haga clic en **Tienda** > **Asociar aplicación con la Tienda...**.

    ![Asociar aplicación con la Tienda Windows](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
2. En el asistente, haga clic en **Siguiente**, inicie sesión con su cuenta Microsoft, escriba un nombre para la aplicación en **Reserve un nuevo nombre de aplicación** y haga clic en **Reservar**.
3. Después de crear correctamente el registro de la aplicación, seleccione el nuevo nombre de la aplicación, haga clic en **Siguiente** y, después, en **Asociar**. Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.  
4. Navegue al [Centro de desarrollo de Windows](https://dev.windows.com/en-us/overview), inicie sesión con su cuenta Microsoft, haga clic en el nuevo registro de aplicación en **Mis aplicaciones** y, luego, expanda **Servicios** > **Notificaciones push**.
5. En la página **Notificaciones push**, haga clic en el **sitio de Servicios Live** en **Microsoft Azure Mobile Services**.
6. En la página de registro, anote los valores de **Secretos de aplicación** y **SID del paquete**, que va a utilizar a continuación para configurar el back-end de aplicación móvil.

    ![Asociar aplicación con la Tienda Windows](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No los comparta con nadie ni los distribuya con su aplicación. El **Id. de aplicación** se usa con el secreto para configurar la autenticación de la cuenta de Microsoft.
   >
   >

## <a name="configure-the-backend-to-send-push-notifications"></a>Configuración del back-end para enviar notificaciones push
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Actualización del servidor para enviar notificaciones de inserción
Use uno de los procedimientos siguientes que se ajusten al tipo de proyecto de back-end&mdash;: [back-end de .NET](#dotnet) o [back-end de Node.js](#nodejs).

### <a name="dotnet"></a>Proyecto de back-end de .NET
1. En Visual Studio, haga clic con el botón derecho en el proyecto de servidor, haga clic en **Administrar paquetes NuGet**, busque Microsoft.Azure.NotificationHubs y haga clic en **Instalar**. Esto instala la biblioteca de cliente de Notification Hubs.
2. Expanda **Controladores**, abra TodoItemController.cs y agregue las siguientes instrucciones using:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. En el método **PostTodoItem**, agregue el código siguiente después de la llamada a **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Este código indica al centro de notificaciones que envíe una notificación push después de la inserción de un elemento nuevo.
4. Vuelva a publicar el proyecto de servidor.

### <a name="nodejs"></a>Proyecto de back-end de Node.js
1. Si aún no lo ha hecho, [descargue el proyecto de inicio rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) o utilice el [editor en línea de Azure Portal](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Reemplace el código existente en el archivo todoitem.js por lo siguiente:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
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

    Esta acción envía una notificación del sistema de WNS que contiene el item.text cuando se inserta un nuevo elemento todo.
3. Cuando edite el archivo en el equipo local, vuelva a publicar el proyecto de servidor.

## <a id="update-app"></a>Incorporación de notificaciones de inserción a la aplicación
Después, debe registrarse la aplicación para recibir notificaciones push en el inicio. Cuando tenga habilitada la autenticación, asegúrese de que el usuario inicia sesión antes de intentar registrarse para recibir notificaciones push.

1. Abra el archivo de proyecto **App.xaml.cs** y agregue las siguientes instrucciones `using`:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
2. En el mismo archivo, agregue la siguiente definición del método **InitNotificationsAsync** a la clase **App**:

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra con sus Aplicaciones móviles de App Service.
3. En la parte superior del controlador de eventos **OnLaunched**, en **App.xaml.cs**, agregue el modificador **async** a la definición del método y agregue la siguiente llamada al nuevo método **InitNotificationsAsync**, como se muestra en el siguiente ejemplo:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    Esto garantiza que el valor de ChannelURI de corta duración se registra cada vez que se inicia la aplicación.
4. Recompile el proyecto de aplicación para UWP. La carpeta ahora ya está lista para recibir notificaciones.

## <a id="test"></a>Prueba de las notificaciones push en su aplicación
[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Pasos siguientes
Más información sobre las notificaciones de inserción:

* [Uso del cliente administrado para Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications)  
  : las plantillas proporcionan flexibilidad para enviar inserciones multiplataforma e inserciones localizadas. Sepa cómo registrar plantillas.
* [Diagnosticar problemas de notificaciones push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  : existen varias razones para que las notificaciones se pierdan o no lleguen a los dispositivos. En este tema se muestra cómo analizar y descubrir la causa principal de los errores de notificación de inserción.

También podría continuar con uno de los siguientes tutoriales:

* [Incorporación de la autenticación a la aplicación de Windows](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  : aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.
* [Activación de la sincronización sin conexión para la aplicación de Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  (Habilitación de la sincronización sin conexión para su aplicación): aprenda a agregar compatibilidad sin conexión a su aplicación con un back-end de aplicación móvil. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos) aun cuando no haya conexión de red.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
