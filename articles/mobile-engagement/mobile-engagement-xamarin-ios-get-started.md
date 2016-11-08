---
title: Introducción a Azure Mobile Engagement para Xamarin.iOS
description: Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones push para aplicaciones Xamarin.iOS.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo

---
# <a name="get-started-with-azure-mobile-engagement-for-xamarin.ios-apps"></a>Introducción a Azure Mobile Engagement para aplicaciones Xamarin.iOS
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

En este tema se muestra cómo utilizar Azure Mobile Engagement para conocer el uso de las aplicaciones y enviar notificaciones push a usuarios segmentados en una aplicación Xamarin.iOS.
En este tutorial, puede crear una aplicación Xamarin.iOS vacía que recopile datos básicos y reciba notificaciones push mediante el Sistema de notificaciones push de Apple (APNS).

Este tutorial requiere lo siguiente:

* [Xamarin Studio](http://xamarin.com/studio). También puede utilizar Visual Studio con Xamarin, pero en este tutorial se usa Xamarin Studio. Consulte [Configuración e instalación](https://msdn.microsoft.com/library/mt613162.aspx)para obtener instrucciones. 
* [SDK de Mobile Engagement Xamarin](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).
> 
> 

## <a name="<a-id="setup-azme"></a>setup-mobile-engagement-for-your-ios-app"></a><a id="setup-azme"></a>Configuración de Mobile Engagement para una aplicación iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="<a-id="connecting-app"></a>connect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement
En este tutorial se presenta una "integración básica», que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción.

Crearemos una aplicación básica con Xamarin para demostrar la integración:

### <a name="create-a-new-xamarin.ios-project"></a>Creación de un nuevo proyecto de Xamarin.iOS
1. Inicie Xamarin Studio. Vaya a **File** (Archivo) -> **New** (Nuevo) -> **Solution** (Solución). 
   
    ![][1]
2. Seleccione **Single View App** (Aplicación de una sola vista), asegúrese de que el lenguaje seleccionado sea **C#** y luego haga clic en **Next** (Siguiente).
   
    ![][2]
3. Rellene los valores de **App Name** (Nombre de la aplicación) y **Organization Identifier** (Identificador de organización) y haga clic en **Next** (Siguiente). 
   
    ![][3]
   
   > [!IMPORTANT]
   > Asegúrese de que el perfil de publicación que usará finalmente para implementar su aplicación iOS emplea un id. de aplicación que coincide exactamente con el identificador de paquete que tiene aquí. 
   > 
   > 
4. Actualice los valores de **Project Name** (Nombre de proyecto), **Solution Name** (Nombre de solución) y **Location** (Ubicación) si es necesario y haga clic en **Create** (Crear).
   
    ![][4]

Xamarin Studio crea la aplicación de demostración en la que se integrará Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar la aplicación al back-end de Mobile Engagement
1. Haga clic con el botón derecho en la carpeta **Packages** de las ventanas de la solución y seleccione **Add Packages...** (Agregar paquetes).
   
    ![][5]
2. Busque el **SDK de Xamarin para Microsoft Azure Mobile Engagement** y agréguelo a la solución.  
   
    ![][6]
3. Abra **AppDelegate.cs** y agregue la siguiente instrucción using:
   
        using Microsoft.Azure.Engagement.Xamarin;
4. En el método **FinishedLaunching** , agregue el siguiente código para inicializar la conexión con el back-end de Mobile Engagement. Asegúrese de agregar su elemento **ConnectionString**. Este código también utiliza un elemento **NotificationIcon** ficticio que se agrega con el SDK de Mobile Engagement, que puede que quiera reemplazar. 
   
        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

## <a name="<a-id="monitor"></a>enabling-real-time-monitoring"></a><a id="monitor"></a>Habilitar supervisión en tiempo real
Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla al back-end de Mobile Engagement.

1. Abra **ViewController.cs** y agregue la siguiente instrucción using:
   
        using Microsoft.Azure.Engagement.Xamarin;
2. Reemplace la clase de la que `ViewController` hereda de `UIViewController` por `EngagementViewController`. 

## <a name="<a-id="monitor"></a>connect-app-with-real-time-monitoring"></a><a id="monitor"></a>Conexión de la aplicación con la supervisión en tiempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="<a-id="integrate-push"></a>enable-push-notifications-and-in-app-messaging"></a><a id="integrate-push"></a>Habilitación de las notificaciones push y la mensajería en aplicación
Mobile Engagement permite interactuar y llegar mediante notificaciones push y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement.
En las secciones siguientes se instala la aplicación para recibirlos.

### <a name="modify-your-application-delegate"></a>Modifique su delegado de la aplicación
1. Abra **AppDelegate.cs** y agregue la siguiente instrucción using:
   
        using System; 
2. Ahora, dentro del método `FinishedLaunching`, agregue el siguiente código para registrarse para los mensajes de inserción después de `EngagementAgent.init(...)`
   
        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }
3. Finalmente, actualice o agregue los siguientes métodos:
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }
   
        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }
   
        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }
4. En el archivo **Info.plist** de la solución, confirme que el **identificador de agrupación** coincide con el **identificador de la aplicación** que tiene en el perfil de aprovisionamiento del Centro para desarrolladores de Apple. 
   
    ![][7]
5. En el mismo archivo **Info.plist**, asegúrese de que ha activado la casillas **Enable Background Modes** (Habilitar modos en segundo plano) y **Remote Notifications** (Notificaciones remotas). 
   
    ![][8]
6. Ejecute la aplicación en el dispositivo asociado a este perfil de publicación. 

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png



<!--HONumber=Oct16_HO2-->


