<properties 
	pageTitle="Incorporación de notificaciones push a la aplicación Xamarin iOS con Servicio de aplicaciones de Azure" 
	description="Obtenga información acerca de cómo usar Servicio de aplicaciones de Azure para enviar notificaciones push a su aplicación Xamarin.iOS." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="08/22/2015" 
	ms.author="wesmc"/>

# Incorporación de notificaciones push a la aplicación Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Información general

En este tutorial, agregará notificaciones push al proyecto de [inicio rápido de Xamarin.iOS] para que cada vez que se inserte un registro, se envíe una notificación de inserción. Este tutorial está basado en el tutorial de [inicio rápido de Xamarin.iOS], que debe completar primero. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de notificaciones push al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

El [simulador de iOS no admite notificaciones push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), por lo que debe usar un dispositivo iOS físico. También deberá suscribirse como [miembro del programa para desarrolladores de Apple](https://developer.apple.com/programs/ios/).

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas. Puede seguir usándolas incluso después de que finalice el período de evaluación. Consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

* Un equipo Mac con [Xamarin Studio] y [Xcode] v4.4 o posterior instalado. Tenga en cuenta que es más fácil ejecutar la aplicación Xamarin.iOS en un equipo Mac con Xamarin Studio. Puede ejecutar la aplicación Xamarin.iOS mediante Visual Studio en el equipo Windows si lo desea, pero es un poco más complicado porque tiene que conectarse a un equipo Mac en red. Si está interesado en hacer eso, consulte Instalación de Xamarin.iOS en Windows.

* Un dispositivo iOS físico.

* Complete el [tutorial de inicio rápido](../app-service-mobile-xamarin-ios-get-started.md).

##<a name="create-hub"></a>Creación de un centro de notificaciones

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## <a id="register"></a>Registro de aplicaciones para notificaciones push

[AZURE.INCLUDE [Habilitación de notificaciones de inserción de Apple](../../includes/enable-apple-push-notifications.md)]

## Configuración de Azure para enviar notificaciones push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Actualización del proyecto de servidor para enviar notificaciones push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="publish-the-service"></a>Implementación del proyecto de servidor en Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## <a name="configure-app"></a>Configuración del proyecto de Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push"></a>Incorporación de notificaciones de inserción a la aplicación

1. En **QSTodoService**, agregue la siguiente propiedad para que **AppDelegate** pueda adquirir el cliente móvil:
        
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Agregue la siguiente instrucción `using` en la parte superior del archivo **AppDelegate.cs**.

        using Microsoft.WindowsAzure.MobileServices;

2. En **AppDelegate**, reemplace el evento **FinishedLaunching**:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. En el mismo archivo, reemplace el evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. A continuación, reemplace el evento **DidReceivedRemoteNotification**:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Ahora su aplicación está actualizada para que sea compatible con las notificaciones push.

## <a name="test"></a>Prueba de las notificaciones push en su aplicación

1. Presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **Aceptar** para aceptar las notificaciones push.
	
	> [AZURE.NOTE]Debe aceptar de forma explícita las notificaciones push desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2. En la aplicación, escriba una tarea y luego haga clic en el icono de signo de suma (**+**).

3. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **Aceptar** para descartarla.

4. Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra una notificación.

Ha completado correctamente este tutorial.

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=Nov15_HO2-->