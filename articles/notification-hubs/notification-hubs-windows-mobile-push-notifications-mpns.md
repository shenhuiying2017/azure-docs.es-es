---
title: "Introducción a Azure Notification Hubs para aplicaciones Windows Phone | Microsoft Docs"
description: "En este tutorial aprenderá a usar Azure Notification Hubs para enviar notificaciones push a aplicaciones Windows Phone 8 o Windows Phone 8.1 Silverlight."
services: notification-hubs
documentationcenter: windows
keywords: "notificación push, notificación push, notificación push de windows phone"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 7d44d0a0f8683ad6ad55136ad17879e98e26498b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-windows-phone-apps"></a>Introducción a Azure Notification Hubs para aplicaciones Windows Phone
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Información general
> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener cuenta, puede crear una de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).
> 
> 

Este tutorial muestra cómo puede usar Azure Notification Hubs para enviar notificaciones push a aplicaciones Windows Phone 8 o Windows Phone 8.1 Silverlight. Si va a desarrollar para Windows Phone 8.1 (no Silverlight), consulte entonces la versión [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
En este tutorial puede crear una aplicación de Windows Phone 8 vacía que recibe notificaciones push mediante el Servicio de notificaciones de inserción de Microsoft (MPNS). Cuando haya finalizado, podrá usar el centro de notificaciones para difundir notificaciones push a todos los dispositivos que ejecutan su aplicación.

> [!NOTE]
> El SDK de Windows Phone para Notification Hubs no admite el uso de Servicios de notificaciones de inserción de Windows (WNS) con aplicaciones de Windows Phone 8.1 Silverlight. Para usar WNS (en lugar de MPNS) con aplicaciones de Windows Phone 8.1 Silverlight, siga el [tutorial Notification Hubs: Windows Phone Silverlight], que usa API de REST.
> 
> 

En este tutorial se demuestra el escenario de difusión sencillo con Notification Hubs.

## <a name="prerequisites"></a>requisitos previos
Este tutorial requiere lo siguiente:

* [Visual Studio 2012 Express para Windows Phone], o una versión posterior.

La realización de este tutorial es un requisito previo para todos los demás tutoriales de Notification Hubs para aplicaciones de Windows Phone 8.

## <a name="create-your-notification-hub"></a>Creación de su centro de notificaciones
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>En la sección <b>Notification Services</b>, seleccione <b>Windows Phone (MPNS)</b> y, después, haga clic en la casilla <b>Habilitar notificaciones push sin autenticar</b>.</p>
</li>
</ol>

&emsp;&emsp;![Azure Portal: Habilitación de notificaciones push sin autenticar](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

El concentrador se crea y configura ahora para enviar una notificación sin autenticar para Windows Phone.

> [!NOTE]
> Este tutorial usa MPNS en modo sin autenticar. El modo sin autenticar de MPNS viene con restricciones sobre las notificaciones que puede enviar a cada canal. Notification Hubs admite el [modo autenticado de MPNS](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) al permitir que cargue su certificado.
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>Conexión de su aplicación al centro de notificaciones
1. En Visual Studio, cree una aplicación nueva de Windows Phone 8.
   
       ![Visual Studio - New Project - Windows Phone App][13]
   
    En Visual Studio 2013 Update 2 o versiones posteriores, cree en su lugar una aplicación Silverlight de Windows Phone.
   
    ![Visual Studio, nuevo proyecto, aplicación vacía, Windows Phone Silverlight][11]
2. En Visual Studio, haga clic con el botón derecho en la solución y, luego, haga clic en **Administrar paquetes de NuGet**.
   
    De este modo aparece el cuadro de diálogo **Administrar paquetes de NuGet** .
3. Busque `WindowsAzure.Messaging.Managed` , haga clic en **Instalar**y acepte los términos de uso.
   
    ![Visual Studio, Administrador de paquetes NuGet][20]
   
    A continuación, se descarga, instala y agrega una referencia a la biblioteca de Mensajería de Azure para Windows mediante el <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">paquete de NuGet WindowsAzure.Messaging.Managed</a>.
4. Abra el archivo App.xaml.cs y agregue las siguientes instrucciones `using` :
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. Agregue el siguiente código en la parte superior del método **Application_Launching** en App.xaml.cs:
   
        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }
   
        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
   
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
   
   > [!NOTE]
   > El valor **MyPushChannel** es un índice que se utiliza para buscar un canal existente en la colección [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) . Si no hay uno ya allí, cree una nueva entrada con ese nombre.
   > 
   > 
   
    Asegúrese de insertar el nombre del centro y la cadena de conexión llamada **DefaultListenSharedAccessSignature** que obtuvo en la sección anterior.
    Este código recupera el URI del canal de la aplicación desde MPNS y, luego, lo registra con su centro de notificaciones. Garantiza también que el URI del canal se registre en su centro de notificaciones cada vez que se inicia la aplicación.
   
   > [!NOTE]
   > Este tutorial envía una notificación del sistema al dispositivo. Cuando envía una notificación de icono, debe llamar al método **BindToShellTile** en el canal. Para admitir notificaciones del sistema y notificaciones de icono, llame a ambos métodos, **BindToShellTile** y  **BindToShellToast**.
   > 
   > 
6. En el Explorador de soluciones, expanda **Propiedades**, abra el archivo `WMAppManifest.xml`, haga clic en la pestaña **Funcionalidades** y asegúrese de que la funcionalidad **ID_CAP_PUSH_NOTIFICATION** esté marcada.
   
       ![Visual Studio - Windows Phone App Capabilities][14]
   
       This ensures that your app can receive push notifications. Without it, any attempt to send a push notification to the app will fail.
7. Presione la tecla `F5` para ejecutar la aplicación.
   
    Se muestra un mensaje de registro en la aplicación.
8. Cierre la aplicación.  
   
   > [!NOTE]
   > Para recibir una notificación push, la aplicación no se debe estar ejecutando en primer plano.
   >

## <a name="next-steps"></a>pasos siguientes
En este sencillo ejemplo, ha difundido notificaciones push a todos los dispositivos Windows Phone 8. 

Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Notification Hubs para insertar notificaciones para los usuarios] . 

Si desea segmentar a sus usuarios por grupos de interés, puede leer [Uso de Notification Hubs para enviar noticias de último minuto]. 

Obtenga más información sobre el uso de Notification Hubs en la [orientación sobre Notification Hubs](en inglés).

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[orientación sobre Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Uso de Notification Hubs para insertar notificaciones para los usuarios]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Uso de Notification Hubs para enviar noticias de último minuto]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[tutorial Notification Hubs: Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

