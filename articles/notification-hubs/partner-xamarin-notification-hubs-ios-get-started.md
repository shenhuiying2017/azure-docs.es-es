<properties
	pageTitle="Introducción a los Centros de notificaciones para las aplicaciones Xamarin iOS"
	description="Obtenga información acerca de cómo usar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación Xamarin iOS."
	services="notification-hubs"
	documentationCenter="xamarin"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="04/14/2015"
	ms.author="yuaxu"/>

# Introducción a los Centros de notificaciones

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Información general

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación iOS. En este tutorial puede crear una aplicación Xamarin.iOS vacía que recibe notificaciones de inserción mediante el servicio de notificaciones de inserción de Apple (APN). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su Centro de notificaciones. El código acabado está disponible en el ejemplo de la [aplicación NotificationHubs][GitHub].

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones.

##Requisitos previos

Este tutorial cuenta con los siguientes requisitos previos:

+ [XCode 6.0][Install Xcode]
+ Dispositivo compatible con iOS 7.0 (o una versión posterior)
+ Pertenencia al programa para desarrolladores de iOS
+ [Xamarin.iOS]
+ [Componente de Servicios móviles de Azure]

   >[AZURE.NOTE]Debido a los requisitos de la configuración de las notificaciones de inserción, debe implementar y realizar una prueba de las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un simulador.

Completar este tutorial es un requisito previo para todos los demás tutoriales de Centros de notificaciones para aplicaciones Xamarin.iOS.

> [AZURE.IMPORTANT]Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

El servicio de notificaciones de inserción de Apple (APNS) usa certificados para autenticar su servicio móvil. Siga estas instrucciones para crear los certificados necesarios y cargarlos en su servicio móvil. Para consultar la documentación oficial de la característica APNS, consulte [Servicio de notificaciones push de Apple].


##<a name="certificates"></a>Generación del archivo de solicitud de firma de certificado

Primero debe generar el archivo de solicitud de firma de certificado (CSR) que Apple usa para generar un certificado firmado.

1. En la carpeta Utilities, ejecute la herramienta Acceso a llaves.

2. Haga clic en **Keychain Access** (Acceso a llaves), expanda **Certificate Assistant**, (Asistente para certificados) y, a continuación, haga clic en **Request a Certificate from a Certificate Authority...** (Solicitar un certificado de una entidad de certificación...).

  ![][5]

3. Seleccione su **User Email Address** (Dirección de correo electrónico del usuario), escriba los valores de **Common Name** (Nombre común) y **CA Email Address** (Dirección de correo electrónico de CA), asegúrese de que se haya seleccionado **Saved to disk** (Se guarda en disco) y, a continuación, haga clic en **Continue** (Continuar).

  ![][6]

4. Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Save As** (Guardar como), seleccione la ubicación en **Where** (Dónde) y, a continuación, haga clic en **Save** (Guardar).

  ![][7]

  De esta forma, el archivo CSR se guarda en la ubicación seleccionada. La ubicación predeterminada es el escritorio. Recuerde la ubicación seleccionada para este archivo.

A continuación, se registrará la aplicación en Apple, se habilitarán las notificaciones de inserción y se cargará este archivo CSR exportado para crear un certificado de inserción.

##<a name="register"></a>Registro de la aplicación para notificaciones de inserción

Para poder enviar notificaciones de inserción a una aplicación iOS desde servicios móviles, debe registrar su aplicación con Apple y también registrar las notificaciones de inserción.

1. Si aún no ha registrado su aplicación, diríjase al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portal de aprovisionamiento de iOS</a> (en inglés) del Centro para desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identifiers** (Identificadores) y, a continuación, en **App IDs** (Identificadores de aplicación). Para finalizar, haga clic en el signo **+** para registrar una nueva aplicación.

   ![][105]

2. Escriba un nombre para la aplicación en **Description** (Descripción) y un valor para **Bundle Identifier** (Identificador de agrupación), marque la opción "Push Notifications" (Notificaciones de inserción) en la sección "App Services" (Servicios de aplicaciones) y, a continuación, haga clic en **Continue** (Continuar).

   ![][106]

   ![][107]

   ![][108]


	De esta forma, se genera el identificador de la aplicación y se solicita que envíe la información. Haga clic en **Submit**(Enviar).

   ![][109]

	Una vez que haga clic en **Submit** (Enviar), verá la pantalla Registration **complete** (Registro completado), como se muestra a continuación. Haga clic en **Done**(Listo).

   ![][110]

	> [AZURE.NOTE] Si elige proporcionar un valor **Bundle Identifier** distinto a **MobileServices.Quickstart**, también debe actualizar el identificador de agrupación de trabajos en el proyecto de Xcode.

3. Busque el identificador de la aplicación que acaba de crear y haga clic en su fila.

   ![][111]

	Al hacer clic en el id. de la aplicación aparecerán los detalles de la misma y su id:

   ![][112]

   ![][113]

4. Haga clic en **Edit** (Editar), luego desplácese al final de la pantalla y haga clic en **Create Certificate...** (Crear certificado...) en la sección **Development Push SSL Certificate** (Certificado SSL de inserción de desarrollo).

   ![][114]

	Se mostrará el asistente "Add iOS Certificate".

   ![][115]

	> [AZURE.NOTE] Este tutorial usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producción. Asegúrese de que establece el mismo tipo de certificado cuando carga el certificado en Servicios móviles.

5. Haga clic en **Choose File** (Elegir archivo), desplácese a la ubicación donde guardó el archivo CSR que creó en la primera tarea y, a continuación, haga clic en **Generate** (Generar).

  ![][116]

6. Una vez que el portal haya creado el certificado, haga clic en **Download** (Descargar) y, a continuación, en **Done** (Listo).

  ![][118]

  ![][119]

   De esta forma, se descarga el certificado de firma y se guarda en su equipo en la carpeta de **descargas**.

  ![][9]

    > [AZURE.NOTE] De forma predeterminada, el certificado de desarrollo del archivo descargado tiene el nombre **aps_development.cer**.

7. Haga doble clic en el certificado de inserción **aps_development.cer** descargado.

	De esta forma, se instala un nuevo certificado en las llaves, como se muestra a continuación:

   ![][10]

	> [AZURE.NOTE]
	> El nombre del certificado puede ser distinto, pero tendrá el prefijo de los **servicios de notificaciones de inserción de iOS de desarrollo de Apple**.

	A continuación, usará este certificado para generar un archivo .p12 y cargarlo en su Centro de notificaciones para habilitar las notificaciones de inserción mediante APNS.

##<a name="profile"></a>Creación de un perfil de aprovisionamiento para la aplicación

1. Vuelva al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de aprovisionamiento de iOS</a>, seleccione **Provisioning Profiles** (Perfiles de aprovisionamiento), seleccione **All** (Todo) y, a continuación, haga clic en el botón **+** para crear un nuevo perfil. De esta forma, aparecerá el asistente **Add iOS Provisioning Profile** (Agregar perfil de aprovisionamiento de iOS).

   ![][120]

2. Seleccione **iOS App Development** (Desarrollo de aplicaciones de iOS) bajo **Development** (Desarrollo) como tipo de perfil de aprovisionamiento y, a continuación, haga clic en **Continue** (Continuar).

   ![][121]

3. A continuación, seleccione el identificador de la aplicación para la aplicación Mobile Services Quickstart en la lista desplegable **App ID** y haga clic en **Continue** (Continuar).

   ![][122]

4. En la pantalla **Select certificates** (Seleccionar certificados), seleccione el certificado creado anteriormente y haga clic en **Continue** (Continuar).

   ![][123]

5. A continuación, seleccione en **Devices** los dispositivos que usará para la prueba y haga clic en **Continue** (Continuar).

   ![][124]

6. Para terminar, elija un nombre para el perfil en **Profile Name** (Nombre de perfil), haga clic en **Generate** (Generar) y, por último, en **Done** (Listo).

   ![][125]

   ![][126]

  De esta forma, se creará un nuevo perfil de aprovisionamiento.

7. En Xcode, abra el organizador, seleccione la vista de dispositivos, seleccione **Provisioning Profiles** (Perfiles de aprovisionamiento) en la sección **Library** (Biblioteca) del panel izquierdo e importe el perfil de aprovisionamiento que acaba de crear.

8. En la izquierda, seleccione el dispositivo e importe de nuevo el perfil de aprovisionamiento.

9. En Keychain Access (Acceso a llaves), haga clic con el botón derecho en el nuevo certificado, haga clic en **Export** (Exportar), escriba un nombre para el certificado, seleccione el formato **.p12** y, después, haga clic en **Save** (Guardar).

   ![][18]

  Anote el nombre de archivo y la ubicación del certificado exportado.

De esta forma, se garantiza que el proyecto de Xcode usa el nuevo perfil para la firma de código. A continuación, debe cargar el certificado en su Centro de notificaciones.

##<a name="configure-hub"></a>Configuración del Centro de notificaciones

1. Inicie sesión en el [Portal de administración de Azure] y haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y, a continuación, en **Creación rápida**.

   ![][27]

3. Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Crear una nueva base de datos central de notificaciones**.

   ![][28]

4. Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, a continuación, haga clic en la pestaña **Configurar** en la parte superior.

   ![][29]

5. Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

   ![][210]

6. Seleccione la pestaña **Configurar** en la parte superior y, a continuación, haga clic en **Cargar** para la configuración de notificaciones de Apple. A continuación, seleccione el certificado **.p12** que exportó anteriormente y la contraseña para el certificado. Asegúrese de seleccionar si desea usar el servicio de inserción **Production** (si desea enviar notificaciones de inserción a usuarios que adquirieron la aplicación desde la tienda) o **Sandbox** (durante el desarrollo).

   ![][211]

7. Haga clic en la pestaña **Panel** en la parte superior y, a continuación, haga clic en **Información de conexión**. Anote las dos cadenas de conexión.

   ![][212]

Su Centro de notificaciones está ahora configurado para funcionar con APN y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones.

##<a name="connecting-app"></a>Conexión de la aplicación al Centro de notificaciones

### Crear un nuevo proyecto

1. En Xamarin Studio, cree un nuevo proyecto iOS y seleccione la plantilla **Unified API > Single View Application** (API unificada > Aplicación de vista simple).

   ![][31]

2. En primer lugar, agregue una referencia al componente de mensajería de Azure. En la vista de soluciones, haga clic con el botón derecho en la carpeta **Components** (Componentes) del proyecto y elija **Get More Components** (Obtener más componentes). Busque el componente **Azure Messaging** (Mensajería de Azure) y agréguelo a su proyecto.

3. En **AppDelegate.cs**, agregue la siguiente instrucción using:

    using WindowsAzure.Messaging;

4. Declare una instancia de **SBNotificationHub**:

		private SBNotificationHub Hub { get; set; }

5. Cree una clase **Constants.cs** con las siguientes variables:

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. En **AppDelegate.cs**, actualice el elemento **FinishedLaunching()** para que coincida con lo siguiente:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert |
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);

            return true;
        }

7. Invalide el método **RegisteredForRemoteNotifications()** en **AppDelegate.cs**:

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

8. Invalide el método **ReceivedRemoteNotification()** en **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Cree el método **ProcessNotification()** siguiente en **AppDelegate.cs**:

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
                // "  aps:{alert:"alert msg here"}  " this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the json gets dumped right into a NSDictionary,
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

    > [AZURE.NOTE]Puede elegir invalidar **FailedToRegisterForRemoteNotifications()** para controlar determinadas situaciones, por ejemplo, cuando no haya conexión de red, entre otras.


10. Ejecute la aplicación en el dispositivo.

##<a name="send"></a>Envío de notificaciones desde el back-end

Puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end que use la <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz REST</a>. En este tutorial enviará notificaciones con una aplicación de consola .NET y con un servicio móvil mediante un script de Node.

Para enviar notificaciones mediante una aplicación .NET:

1. Cree una aplicación de consola nueva de Visual C#:

   ![][213]

2. Agregue una referencia al SDK de Bus de servicio de Azure con el paquete<a href="http://nuget.org/packages/WindowsAzure.ServiceBus/"> WindowsAzure.ServiceBus NuGet</a>. En el menú principal de Visual Studio, haga clic en **Herramientas**, **Administrador de paquetes de biblioteca** y finalmente en **Consola del administrador de paquetes**. Posteriormente, en la ventana de la consola, escriba:

        Install-Package WindowsAzure.ServiceBus and press Enter.

2. Abra el archivo Program.cs y agregue la siguiente instrucción using:

        using Microsoft.ServiceBus.Notifications;

3. En la clase `Program`, agregue el método siguiente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{"aps":{"alert":"Hello from .NET!"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. A continuación, agregue la siguiente línea al método `Main`:

         SendNotificationAsync();
		 Console.ReadLine();

5. Presione la tecla F5 para ejecutar la aplicación. Debe recibir una alerta en el dispositivo. Si usa Wi-Fi, asegúrese de que la conexión funciona.

Puede buscar todas las cargas posibles en la guía [Local and Push Notification Programming Guide] (Guía de programación de notificaciones locales y de inserción) de Apple.

Para enviar una notificación mediante un servicio móvil, siga la [Introducción a los Servicios móviles] y, a continuación:

1. Inicie sesión en el [Portal de administración de Azure] y seleccione su servicio móvil.

2. Seleccione la pestaña **Programador** en la parte superior.

   ![][215]

3. Cree un nuevo trabajo programado, inserte un nombre y seleccione **A petición**.

   ![][216]

4. Cuando se cree el trabajo, haga clic en el nombre del trabajo. A continuación,haga clic en la pestaña **Script** en la barra superior.

5. Inserte el siguiente script en su función de programador. Asegúrese de reemplazar los marcadores de posición por su nombre del centro de notificaciones y la cadena de conexión para *DefaultFullSharedAccessSignature* que obtuvo anteriormente. Haga clic en **Guardar**.

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);


6. Haga clic en **Ejecutar una vez** en la barra inferior. Debe recibir una alerta en el dispositivo.

## <a name="next-steps"></a>Pasos siguientes

En este sencillo ejemplo, se difunden notificaciones a todos los dispositivos iOS. Para dirigirse a usuarios específicos, consulte el tutorial [Notificación a los usuarios con los Centros de notificaciones de Azure], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora]. Para obtener más información sobre el uso de los Centros de notificaciones, consulte [Introducción a los centros de notificaciones] y los [procedimientos de los Centros de notificaciones para iOS].

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

<!-- Images. -->
[5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
[18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[105]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introducción a los Servicios móviles]: /develop/mobile/tutorials/get-started-xamarin-ios
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Introducción a los centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx
[procedimientos de los Centros de notificaciones para iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Notificación a los usuarios con los Centros de notificaciones de Azure]: /manage/services/notification-hubs/notify-users-aspnet
[Uso de los Centros de notificaciones para enviar noticias de última hora]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Servicio de notificaciones push de Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Componente de Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS

<!--HONumber=52-->
 