<properties 
	pageTitle="Adición de notificaciones push a la aplicación Xamarin iOS con Servicios de aplicaciones de Azure" 
	description="Obtenga información acerca de cómo usar Servicios de aplicaciones de Azure para enviar notificaciones push a su aplicación Xamarin iOS." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="yuaxu"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="02/22/2015" 
	ms.author="yuaxu"/>

# Adición de notificaciones push a su aplicación Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

Este tema muestra cómo usar Servicios de aplicaciones de Azure para enviar notificaciones push a una aplicación Xamarin iOS 8. En este tutorial aprenderá a agregar notificaciones push con el servicio de notificación push de Apple (APN) al proyecto [Introducción a las aplicaciones móviles de Servicios de aplicaciones] proyecto. Cuando haya finalizado, el back-end móvil le enviará una notificación push cada vez que se inserte un registro.

Este tutorial requiere lo siguiente:

+ Un dispositivo iOS 8
+ Pertenencia al programa para desarrolladores de iOS
+ [Xamarin.iOS Studio]
+ [Componente de Servicios móviles de Azure]

   > [AZURE.NOTE] Debido a los requisitos de la configuración de las notificaciones de inserción, debe implementar y realizar una prueba de las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

El servicio de notificaciones de inserción de Apple (APN) usa certificados para autenticar su aplicación móvil. Siga estas instrucciones para crear los certificados necesarios y cargarlos en su aplicación móvil. Para consultar la documentación oficial de la característica APNS, consulte [Servicio de notificaciones push de Apple].

## <a name="certificates"></a>Generación del archivo de solicitud de firma de certificado

Primero debe generar el archivo de solicitud de firma de certificado (CSR) que Apple usa para generar un certificado firmado.

1. En Utilidades, ejecute la **herramienta de acceso a llaves**.

2. Haga clic en **Keychain Access** (Acceso a llaves), expanda **Certificate Assistant** (Asistente para certificados) y, a continuación, haga clic en **Request a Certificate from a Certificate Authority...** (Solicitar un certificado de una entidad de certificación...).

    ![][5]

3. Especifique la **dirección de correo electrónico de usuario**, escriba un valor de **nombre común**, asegúrese de que se ha seleccionado la opción de **guardado en el disco** y, a continuación, haga clic en **Continuar**.

    ![][6]

4. Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Save As** (Guardar como), seleccione la ubicación en **Where** (Dónde) y, a continuación, haga clic en **Guardar**.

    ![][7]
  
    Recuerde la ubicación que elija.

A continuación, se registrará la aplicación en Apple, se habilitarán las notificaciones de inserción y se cargará este archivo CSR exportado para crear un certificado de inserción.

## <a name="register"></a>Registro de la aplicación para notificaciones push

Para poder enviar notificaciones push a un dispositivo iOS desde la aplicación móvil, debe registrar su aplicación con Apple y registrar las notificaciones push. 

1. Si aún no ha registrado la aplicación, vaya al Portal de aprovisionamiento de iOS</a> en el Centro para desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identificadores**, en **Identificadores de Apple** y, a continuación, en el signo más **+** para crear un identificador de aplicaciones para la aplicación.
    
    ![][102]

2. Escriba un nombre para la aplicación en **Descripción**, escriba y recuerde el **Identificador de la agrupación de trabajos**, active la opción "Notificaciones push" en la sección "Servicios de aplicaciones" y, a continuación, haga clic en **Continuar**. En este ejemplo se usa el identificador **MobileServices.Quickstart** pero es posible que no pueda volver a usar ese mismo identificador, ya que los identificadores de aplicaciones deben ser exclusivos entre todos los usuarios. Por ese motivo, es recomendable que agregue su nombre completo o sus iniciales después del nombre de la aplicación. 

    ![][103]
   
    De esta forma, se genera el identificador de la aplicación y se solicita que **envíe** la información. Haga clic en **Enviar**.
   
    ![][104] 
   
    Una vez que haga clic en **Submit** (Enviar), verá la pantalla **Registration complete** (Registro completado), como se muestra a continuación. Haga clic en **Done** (Listo).
   
    ![][105]    

3. Busque el identificador de la aplicación que acaba de crear y haga clic en su fila. 

    ![][106]
   
    Al hacer clic en el id. de la aplicación aparecerán los detalles de la misma y su id. Haga clic en el botón **Settings** (Configuración).
   
    ![][107] 
   
4. Desplácese a la parte inferior de la pantalla y haga clic en el botón **Create Certificate...** (Crear certificado...) en la sección **Development Push SSL Certificate** (Certificado SSL de inserción de desarrollo).

    ![][108] 

    Se mostrará el asistente "Add iOS Certificate".
   
    Nota: Este tutorial usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producción. Asegúrese de que establece el mismo tipo de certificado cuando carga el certificado en la aplicación móvil.

5. Haga clic en **Elegir archivo**, vaya a la ubicación donde guardó el archivo CSR anteriormente, y a continuación, haga clic en **Generar**. 

    ![][110]
  
6. Una vez que el portal haya creado el certificado, haga clic en el botón **Download** (Descargar) y en **Done** (Listo).
 
    ![][111]  

    De esta forma, se descarga el certificado de firma y se guarda en su equipo en la carpeta de descargas. 

    ![][9] 

    Nota: De forma predeterminada, el certificado de desarrollo del archivo descargado tiene el nombre <strong>aps_development.cer</strong>.

7. Haga doble clic en el certificado de inserción descargado **aps_development.cer**.

    De esta forma, se instala un nuevo certificado en las llaves, como se muestra a continuación:

    ![][10]

    Nota: El nombre del certificado puede ser distinto, pero tendrá el prefijo de los <strong>servicios de notificaciones push de iOS de desarrollo de Apple:</strong>.

A continuación, usará este certificado para generar un archivo .p12 y cargarlo en la aplicación móvil para permitir la autenticación con APNS.

## <a name="profile"></a>Creación de un perfil de aprovisionamiento para la aplicación
 
1. Vuelva al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de aprovisionamiento de iOS</a>, seleccione **Perfiles de aprovisionamiento**, seleccione **Todo** y, a continuación, haga clic en el botón **+** para crear un nuevo perfil. De esta forma, se iniciará el asistente para **Agregar perfil de aprovisionamiento de iOS**.

    ![][112]

2. Seleccione **Desarrollo de aplicaciones de iOS** en **Desarrollo** como tipo de perfil de aprovisionamiento y haga clic en **Continuar**.

3. A continuación, seleccione el identificador de la aplicación para la aplicación de inicio rápido de aplicaciones móviles en la lista desplegable **Identificador de aplicaciones** y haga clic en **Continuar**.

    ![][113]

4. En la pantalla **Seleccionar certificados**, seleccione el certificado creado anteriormente y haga clic en **Continuar**.

    ![][114]

5. A continuación, seleccione los **Devices** (Dispositivos) que se van usar para la prueba y haga clic en **Continue** (Continuar).
  
    ![][115]

6. Para terminar, seleccione un nombre para el perfil en **Nombre de perfil**, haga clic en **Generar** y haga clic en **Listo**.

    ![][116]

    De esta forma, se creará un nuevo perfil de aprovisionamiento.

    ![][117]

## <a name="configure-appServiceMobile"></a>Configuración del back-end móvil de Servicios de aplicaciones para enviar solicitudes push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Actualización del servidor para enviar notificaciones de inserción

1. En Visual Studio, haga clic con el botón derecho en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**.

2. Busque **Microsoft.Azure.NotificationHubs** y haga clic en **Instalar** para todos los proyectos de la solución.

3. En el Explorador de soluciones de Visual Studio, muestre la carpeta **Controladores** en el proyecto de back-end móvil. Abra TodoItemController.cs. Al principio del archivo, agregue las siguientes instrucciones `using`:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Agregue el siguiente fragmento al método  `PostTodoItem` después de la llamada a **InsertAsync**:  

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

        // iOS payload
        var appleNotificationPayload = "{\"aps\":{\"alert\":\"" + item.Text + "\"}}";

        try
        {
            await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);
        }
        catch (System.Exception ex)
        {
            throw;
        }

    Este código indica al Centro de notificaciones asociado a esta aplicación móvil que envíe una notificación push después de la inserción del elemento de tareas pendientes.


<h2><a name="publish-the-service"></a>Publicación del back-end móvil en Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="configure-app"></a>Configuración de la aplicación Xamarin.iOS

1. En Xamarin.Studio, abra **Info.plist** y actualice el **identificador de agrupación de trabajos** con el identificador que ha creado anteriormente.

    ![][121]

2. Desplácese hacia abajo hasta **Modos en segundo plano** y active la casilla **Habilitar modos en segundo plano** y la casilla **Notificaciones remotas**. 

    ![][122]

3. Haga doble clic en el proyecto en el Panel de soluciones para abrir las **Opciones de proyecto**.

4.  Elija **Registro de agrupación de trabajos iOS** en **Compilar** y seleccione la **identidad** y el **perfil de aprovisionamiento** correspondientes que acaba de configurar para este proyecto. 

    ![][120]

    De esta forma, se garantiza que el proyecto de Xamarin usa el nuevo perfil para la firma de código. Para ver la documentación oficial de aprovisionamiento de dispositivos Xamarin, consulte [Aprovisionamiento de dispositivos Xamarin].

## <a name="add-push"></a>Agregar notificaciones de inserción a la aplicación

1. En **QSTodoService**, reemplace la declaración de cliente existente de manera que **AppDelegate** pueda adquirir el cliente móvil:
        
        public MobileServiceClient client { get; private set; }

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

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción.


<h2><a name="publish-the-service"></a>Publicación del back-end móvil en Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="test"></a>Prueba de las notificaciones push en su aplicación

1. Presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **Aceptar** para aceptar las notificaciones de inserción.
	
	> [AZURE.NOTE] Debe aceptar de forma explícita las notificaciones de inserción desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2. En la aplicación, escriba una tarea y, a continuación, haga clic en el icono de signo de suma (**+**).

3. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **Aceptar** para descartarla.

4. Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra una notificación.

Ha completado correctamente este tutorial.

<!-- Images. -->

[24]: ./media/mobile-services-ios-get-started-push/mobile-services-quickstart-push2-ios.png
[Introducción a las aplicaciones de servicio de Aplicaciones móviles]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[Introducción a las aplicaciones móviles de Servicios de aplicaciones]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[120]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-20.png
[121]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-21.png
[122]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Instalación de Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de aprovisionamiento de iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Servicio de notificación push de Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Servicio de notificaciones push de Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-xamarin-ios
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Notificaciones push para usuarios de la aplicación]: /es-es/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Aprovisionamiento de dispositivos Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Portal de administración de Azure]: https://manage.windowsazure.com/
[apns, objeto]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Componente de Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/
[proyecto de ejemplo completo]: http://go.microsoft.com/fwlink/p/?LinkId=331303


<!--HONumber=49-->