---
title: Envío de notificaciones a aplicaciones de Plataforma universal de Windows mediante Azure Notification Hubs | Microsoft Docs
description: En este tutorial aprenderá a usar Azure Notification Hubs para enviar notificaciones push a una aplicación de la plataforma universal de Windows.
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c3bb170800508d5a546573850f445b2a8991ea8c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-by-using-azure-notification-hubs"></a>Tutorial: Envío de notificaciones a aplicaciones de Plataforma universal de Windows mediante Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

En este tutorial se crea un Centro de notificaciones para enviar notificaciones push a una aplicación de Plataforma universal de Windows (UWP). Cree una aplicación vacía de la Tienda Windows que reciba notificaciones push a través de los Servicios de notificaciones de inserción de Windows (WNS). Luego, puede usar el Centro de notificaciones para difundir notificaciones push a todos los dispositivos que ejecuten su aplicación.

> [!NOTE]
> El código completo de este tutorial se encuentra en [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Crear una aplicación en Tienda Windows
> * Crear un Centro de notificaciones
> * Crear una aplicación de Windows de ejemplo
> * Enviar notificaciones de prueba


## <a name="prerequisites"></a>Requisitos previos
- **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
- [Microsoft Visual Studio Community 2015 o posterior](https://www.visualstudio.com/products/visual-studio-community-vs).
- [Herramientas de desarrollo de aplicaciones de la plataforma universal de Windows instaladas](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Una cuenta de la Tienda Windows activa

La realización de este tutorial es un requisito previo para todos los demás tutoriales de Notification Hubs para las aplicaciones de la plataforma universal de Windows.

## <a name="create-an-app-in-windows-store"></a>Crear una aplicación en Tienda Windows
Para enviar notificaciones push a las aplicaciones de la plataforma universal de Windows, asocie su aplicación a la Tienda Windows. A continuación, configure su centro de notificaciones para que se integre con los Servicios de notificaciones de inserción de Windows.

1. Vaya al [Centro de desarrollo de Windows](https://dev.windows.com/overview), inicie sesión en su cuenta Microsoft y seleccione **Create a new app** (Crear aplicación).

    ![Botón de aplicación nueva](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
1. Escriba el nombre de la aplicación y seleccione **Reserve product name** (Reservar nombre de producto). Así se crea un nuevo registro de la Tienda Windows para el registro de la aplicación.

    ![Nombre de aplicación de Store](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
1. Expanda **Administración de la aplicación**, seleccione **WNS/MPNS**, seleccione **WNS/MPNS** y, después, seleccione **Sitio de Servicios Live**. Iniciar sesión en su cuenta de Microsoft. Se abre el **Portal de registro de aplicaciones** en una pestaña nueva. Otra posibilidad es navegar directamente al [Portal de registro de aplicaciones](http://apps.dev.microsoft.com) y seleccionar el nombre de la aplicación para acceder a esta página.

    ![Página de WNS MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
1.   Anote la contraseña del **Secreto de aplicación** y el **identificador de seguridad de paquete (SID)**.

        >[!WARNING]
        >El secreto de aplicación y el SID del paquete son credenciales de seguridad importantes. No los comparta con nadie ni los distribuya con su aplicación.

## <a name="create-a-notification-hub"></a>Creación de un centro de notificaciones
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


### <a name="configure-wns-settings-for-the-hub"></a>Configuración de los valores de WNS del centro

1. Seleccione **Windows (WNS)** en la categoría **NOTIFICATION SETTINGS** (CONFIGURACIÓN DE NOTIFICACIONES). 
2. Especifique los valores de **SID del paquete** y **Clave de seguridad** que anotó en la sección anterior. 
3. Seleccione **Guardar** en la barra de herramientas.

    ![Cuadros SID del paquete y Clave de seguridad](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

El centro de notificaciones ya está configurado para funcionar con WNS. Tiene la cadena de conexión para registrar la aplicación y enviar notificaciones.

## <a name="create-a-sample-windows-app"></a>Crear una aplicación de Windows de ejemplo
1. En Visual Studio, seleccione **Archivo**, **Nuevo** y **Proyecto**. 
2. En el cuadro de diálogo **Nuevo proyecto** , haga lo siguiente: 

    1. Expanda **Visual C#**.
    2. Seleccione **Windows Universal**. 
    3. Seleccione **Aplicación vacía (Windows universal)**. 
    4. Escriba un **nombre** para el proyecto. 
    5. Seleccione **Aceptar**. 

        ![Cuadro de diálogo Nuevo proyecto](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)
1. Acepte los valores predeterminados de las versiones de plataforma **mínima** y **de destino** y seleccione **Aceptar**. 
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto de la aplicación para la Tienda Windows, seleccione **Tienda** y **Asociar aplicación con la Tienda**. Aparece el asistente **Asocie la aplicación con la Tienda Windows** .
3. En el asistente, inicie sesión con su cuenta de Microsoft.
4. Seleccione la aplicación que registró en el paso 2, **Siguiente** y **Asociar**. De esta manera, se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.
5. En Visual Studio, haga clic con el botón derecho en la solución y seleccione **Administrar paquetes NuGet**. Se abre la ventana **Administrar paquetes NuGet**.
6. En el cuadro de búsqueda, escriba **WindowsAzure.Messaging.Managed**, seleccione **Instalar** y acepte las condiciones de uso.
   
    ![Ventana Administrar paquetes NuGet][20]
   
    Esta acción descarga, instala y agrega una referencia a la biblioteca de Azure Notification Hubs para Windows mediante el [paquete NuGet Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).

3. Abra el archivo de proyecto App.xaml.cs y agregue las siguientes instrucciones `using`: 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. En App.xaml.cs, agregue también a la clase **App** la siguiente definición de método **InitNotificationsAsync**:
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    Este código recupera el URI del canal de la aplicación desde WNS y, luego, lo registra en el Centro de notificaciones.
   
    >[!NOTE]
    >* Reemplace el marcador de posición del **nombre del centro** por el nombre del centro de notificaciones tal y como aparece en Azure Portal. 
    >* Reemplace también el marcador de posición de la cadena de conexión por la cadena de conexión **DefaultListenSharedAccessSignature** que obtuvo en la página **Directivas de acceso** del centro de notificaciones en una sección anterior.
   > 
   > 
5. En la parte superior del controlador de eventos **OnLaunched** en App.xaml.cs, agregue la siguiente llamada al nuevo método **InitNotificationsAsync**:
   
        InitNotificationsAsync();
   
    Esta acción garantiza que el URI del canal se registra en su centro de notificaciones cada vez que se inicia la aplicación.

6. Seleccione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo que muestra la clave de registro. Para cerrar el cuadro de diálogo, haga clic en **Aceptar**. 

    ![El registro se completó correctamente.](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

La carpeta ahora ya está lista para recibir notificaciones.

## <a name="send-test-notifications"></a>Enviar notificaciones de prueba
Puede probar rápidamente la recepción de notificaciones en la aplicación si envía alguna desde [Azure Portal](https://portal.azure.com/). 

1. En Azure Portal, cambie a la pestaña Información general y seleccione **Envío de prueba** en la barra de herramientas.     

    ![Botón Envío de prueba](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. En la ventana **Envío de prueba**, lleve a cabo las siguientes acciones: 
    1. En **Plataformas**, seleccione **Windows**.
    2. En **Tipo de notificación**, seleccione ** 
Notificación del sistema**. 
    3. Seleccione **Enviar**. 
    
        ![Panel Envío de prueba](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Verá el resultado de la operación de envío en la lista **Resultado** de la parte inferior de la ventana. También verá un mensaje de alerta. 

    ![Resultado de la operación de envío](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
1. Verá el mensaje de notificación: **Mensaje de prueba** en el escritorio. 

    ![Mensaje de notificación](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha enviado notificaciones de emisión a todos los dispositivos con Windows mediante el portal o la aplicación de consola. Para aprender a enviar notificaciones push a dispositivos específicos, pase al siguiente tutorial: 

> [!div class="nextstepaction"]
>[Envío de notificaciones push a dispositivos específicos](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)


<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
