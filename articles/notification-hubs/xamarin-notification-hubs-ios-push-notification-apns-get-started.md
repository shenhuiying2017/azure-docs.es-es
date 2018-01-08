---
title: "Introducción a Azure Notification Hubs para aplicaciones Xamarin.iOS | Microsoft Docs"
description: "En este tutorial, obtenga información acerca de cómo usar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación Xamarin iOS."
services: notification-hubs
keywords: "notificaciones push de ios,mensajes de inserción,notificaciones push,mensaje de notificación"
documentationcenter: xamarin
author: jwhiteDev
manager: kpiteira
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: edb48cd8de9b1f7357c40cea73fa4b9a422cb8fa
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-xamarinios-apps"></a>Introducción a Azure Notification Hubs para aplicaciones Xamarin.iOS
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Información general
> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener cuenta, puede crear una de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

Este tutorial muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación iOS. Creará una aplicación Xamarin.iOS vacía que recibe notificaciones push mediante el [Servicio de notificaciones push de Apple (APN)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html). 

Cuando haya finalizado, podrá usar el centro de notificaciones para difundir notificaciones push a todos los dispositivos que ejecutan su aplicación. El código acabado está disponible en el ejemplo de la [aplicación NotificationHubs][GitHub].

En este tutorial se demuestra el escenario sencillo de difusión de mensajes de inserción con Centros de notificaciones.

## <a name="prerequisites"></a>requisitos previos
Este tutorial requiere lo siguiente:

* La versión más reciente de [Xcode][Install Xcode]
* Un dispositivo compatible con iOS 10 (o una versión posterior)
* [programa para desarrolladores de Apple](https://developer.apple.com/programs/)
* [Visual Studio para Mac]
  
  > [!NOTE]
  > Debido a los requisitos de configuración de las notificaciones push de iOS, debe implementar y probar la aplicación de ejemplo en un dispositivo iOS físico (iPhone o iPad) en lugar de hacerlo en un simulador.
  > 
  > 

La realización de este tutorial es un requisito previo para todos los demás tutoriales de Notification Hubs para aplicaciones Xamarin.iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configuración del Centro de notificaciones para notificaciones push de iOS
Esta sección le guía en la creación de un nuevo centro de notificaciones y la configuración de la autenticación con APNs mediante el certificado push **.p12** que creó anteriormente. Si desea usar un centro de notificaciones ya creado, puede omitir los pasos hasta el paso 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Haga clic en el botón <b>Notification Services</b> y, después, seleccione <b>Apple (APNs)</b>. Asegúrese de seleccionar <b>Certificado</b>, haga clic en el icono de archivo y seleccione el archivo <b>.p12</b> que exportó anteriormente. Asegúrese de que también especifica la contraseña correcta.</p>

<p>Asegúrese de seleccionar el modo <b>Espacio aislado</b> ya que esto es para desarrollo. Use el modo <b>Producción</b> únicamente si desea enviar notificaciones de inserción a los usuarios que compraron la aplicación en la tienda.</p>
</li>
</ol>

&emsp;&emsp;&emsp;&emsp;![Configuración de APNs en Azure Portal][6]

&emsp;&emsp;&emsp;&emsp;![Configuración del certificado de APNs en Azure Portal][7]

Su centro de notificaciones está ahora configurado para funcionar con APNS, y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones push.

## <a name="connect-your-app-to-the-notification-hub"></a>Conexión de la aplicación al Centro de notificaciones
#### <a name="create-a-new-project"></a>Crear un nuevo proyecto
1. En Visual Studio, cree un nuevo proyecto de iOS y seleccione la plantilla **Aplicación de una vista**. A continuación, haga clic en **Siguiente**.
   
     ![Visual Studio: selección del tipo de aplicación][31]

2. Escriba el nombre de la aplicación y el identificador de la organización y, a continuación, haga clic en **Siguiente** y en **Crear**.

3. En la vista Solución, haga doble clic en *Into.plist* y, en **Identidad**, asegúrese de que el identificador del conjunto coincida con el usado al crear el perfil de aprovisionamiento. En **Firma**, asegúrese de que la cuenta de desarrollador esté seleccionada en **Equipo**, "Automatically manage signing" (Administrar automáticamente la firma) esté seleccionado, y las opciones Certificado de firma y Perfil de aprovisionamiento estén seleccionadas automáticamente.

    ![Visual Studio: configuración de aplicaciones iOS][32]

4. Agregue el paquete de mensajería de Azure. En la vista Solución, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Agregar paquetes NuGet**. Busque **Xamarin.Azure.NotificationHubs.iOS** y agregue el paquete al proyecto.

5. Agregue un nuevo archivo a su clase, asígnele el nombre **Constants.cs**. A continuación, agregue las siguientes variables y reemplace los marcadores de posición de literal de cadena por su *nombre del centro* y el valor de *DefaultListenSharedAccessSignature* que anotó anteriormente.
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. En **AppDelegate.cs**, agregue la siguiente instrucción using:
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. Declare una instancia de **SBNotificationHub**:
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. En **AppDelegate.cs**, actualice el elemento **FinishedLaunching()** para que coincida con lo siguiente:
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

9. Invalide el método **RegisteredForRemoteNotifications()** en **AppDelegate.cs**:
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

10. Invalide el método **ReceivedRemoteNotification()** en **AppDelegate.cs**:
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. Cree el siguiente método **ProcessNotification()** en **AppDelegate.cs**:
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > Puede elegir invalidar **FailedToRegisterForRemoteNotifications()** para controlar determinadas situaciones, por ejemplo, cuando no haya conexión de red. Esto es especialmente importante si el usuario quisiera iniciar la aplicación en modo sin conexión (por ejemplo, Avión) y desea administrar los escenarios de mensajes push específicos de su aplicación.
  

12. Ejecute la aplicación en el dispositivo.

## <a name="sending-test-push-notifications"></a>Envío de notificaciones push de prueba
Puede probar de recibir notificaciones en la aplicación con la opción *Envío de prueba* opción en [Azure Portal]. Se enviará una notificación push de prueba al dispositivo.

![Azure Portal: envío de prueba][30]

Las notificaciones push se envían normalmente en un servicio back-end como Mobile Apps o ASP.NET mediante una biblioteca compatible. También puede usar la API de REST directamente para enviar mensajes de notificación si no hay disponible una biblioteca para su back-end.

A continuación, se recomienda consultar el tutorial sobre el [uso de Notification Hubs para enviar notificaciones push a los usuarios](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) como paso siguiente para enviar notificaciones desde un back-end de ASP.NET. Sin embargo, se pueden usar los siguientes enfoques para enviar notificaciones:

A continuación, presentamos una lista de algunos otros tutoriales que podría interesarle revisar para enviar notificaciones:
* Interfaz de REST: puede admitir notificaciones push en cualquier plataforma de back-end mediante la [interfaz de REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **SDK para .NET de Centros de notificaciones de Microsoft Azure**: en el Administrador de paquetes NuGet para Visual Studio, ejecute [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* Node.js: [uso de Notification Hubs desde Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* Java/PHP**: para ver un ejemplo de cómo enviar notificaciones push mediante las API de REST, consulte "Uso de Notification Hubs desde Java/PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="next-steps"></a>pasos siguientes
En este sencillo ejemplo, se difunden notificaciones push a todos los dispositivos iOS. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de los Centros de notificaciones para insertar notificaciones para los usuarios]. Si desea segmentar a sus usuarios por grupos de interés, puede leer [Uso de Centros de notificaciones para enviar noticias de último minuto]. Para más información sobre el uso de Notification Hubs, consulte [Introducción a Notification Hubs] y [Procedimientos de Notification Hubs para iOS].

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Introducción a Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedimientos de Notification Hubs para iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio para Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Uso de los Centros de notificaciones para insertar notificaciones para los usuarios]: /manage/services/notification-hubs/notify-users-aspnet
[Uso de Centros de notificaciones para enviar noticias de último minuto]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Portal de Azure]: https://portal.azure.com
