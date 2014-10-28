<properties linkid="develop-notificationhubs-tutorials-get-started-xamarin-ios" urlDisplayName="Get Started" pageTitle="Get Started with Notification Hubs for Xamarin iOS apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin iOS application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" authors="donnam" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación iOS.
En este tutorial, creará una aplicación Xamarin.iOS que recibirá notificaciones de inserción con el servicio de notificación de inserción de Apple (APNS). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su centro de notificaciones. El código acabado está disponible en el ejemplo de la [aplicación NotificationHubs][aplicación NotificationHubs].

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Generación del archivo de solicitud de firma de certificado][Generación del archivo de solicitud de firma de certificado]
2.  [Registro de la aplicación y habilitación para las notificaciones de inserción][Registro de la aplicación y habilitación para las notificaciones de inserción]
3.  [Creación de un perfil de aprovisionamiento para la aplicación][Creación de un perfil de aprovisionamiento para la aplicación]
4.  [Configuración de su Centro de notificaciones][Configuración de su Centro de notificaciones]
5.  [Conexión de su aplicación al Centro de notificaciones][Conexión de su aplicación al Centro de notificaciones]
6.  [Envío de notificaciones desde su back-end][Envío de notificaciones desde su back-end]

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones. Este tutorial cuenta con los siguientes requisitos previos:

-   [XCode 5.0][XCode 5.0]
-   Dispositivo compatible con iOS 5.0 (o una versión posterior)
-   Pertenencia al programa para desarrolladores de iOS
-   [Xamarin.iOS][1]
-   [Componente de Servicios móviles de Azure][Componente de Servicios móviles de Azure]

<div class="dev-callout"><b>Nota:</b><br /> <p>Debido a los requisitos de la configuraci&oacute;n de las notificaciones de inserci&oacute;n, debe implementar y realizar una prueba de las notificaciones de inserci&oacute;n en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un simulador.</p><br /> </div>

</p>
Completar este tutorial es un requisito previo para todos los demás tutoriales de centros de notificaciones para aplicaciones Xamarin.iOS.

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

El servicio de notificaciones de inserción de Apple (APNS) usa certificados para autenticar su servicio móvil. Siga estas instrucciones para crear los certificados necesarios y cargarlos en su servicio móvil. Para consultar la documentación oficial de la característica APNS, consulte [Apple Push Notification Service][Apple Push Notification Service].

## <a name="certificates"></a><span class="short-header">Generación de un archivo CSR</span>Generación del archivo de solicitud de firma de certificado

Primero debe generar el archivo de solicitud de firma de certificado (CSR) que Apple usa para generar un certificado firmado.

1.  En la carpeta Utilities, ejecute la herramienta Acceso a llaves.

2.  Haga clic en **Acceso a llaves**, expanda **Certificate Assistant** y, a continuación, haga clic en **Request a Certificate from a Certificate Authority...**.

    ![][]

3.  Seleccione una dirección de correo electrónico de usuario en **User Email Address**, escriba un nombre común en **Common Name** y una dirección de correo electrónico en **CA Email Address**, asegúrese de que **Saved to disk** se encuentra seleccionado, y, a continuación, haga clic en **Continue**.

    ![][2]

4.  Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Save As**, seleccione la ubicación en **Where** y, a continuación, haga clic en **Save**.

    ![][3]

    De esta forma, se guarda el archivo CSR en la ubicación seleccionada; el escritorio es la ubicación predeterminada. Recuerde la ubicación seleccionada para este archivo.

A continuación, se registrará la aplicación en Apple, se habilitarán las notificaciones de inserción y se cargará este archivo CSR exportado para crear un certificado de inserción.

## <a name="register"></a><span class="short-header">Registro de la aplicación</span>Registro de la aplicación para las notificaciones de inserción

Para poder enviar notificaciones de inserción a una aplicación iOS desde servicios móviles, debe registrar su aplicación con Apple y también registrar las notificaciones de inserción.

1.  Si aún no ha registrado su aplicación, diríjase al [portal de aprovisionamiento de iOS][portal de aprovisionamiento de iOS] (en inglés) del Centro para desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identifiers** y, a continuación, en **App IDs**. Para finalizar, haga clic en el signo **+** para registrar una nueva aplicación.

    ![][4]

2.  Escriba un nombre para la aplicación en **Description** y un valor para **Bundle Identifier**, active la opción "Push Notifications" de la sección "Servicios de aplicaciones" y, a continuación, haga clic en **Continue**.

    ![][5]

    ![][6]

    ![][7]

    De esta forma, se genera el identificador de la aplicación y se solicita que envíe la información. Haga clic en **Enviar**.

    ![][8]

    Una vez que haga clic en **Submit**, verá la pantalla **Registration complete**, como se muestra a continuación. Haga clic en **Done**.

    ![][9]

    <div class="dev-callout"><b>Nota:</b>
<p>Si elige proporcionar un valor <strong>Bundle Identifier</strong> distinto a <b>MobileServices.Quickstart</b>, tambi&eacute;n debe actualizar el identificador de agrupaci&oacute;n de trabajos en el proyecto de Xcode.</p>
 </div>

3.  Busque el identificador de la aplicación que acaba de crear y haga clic en su fila.

    ![][10]

    Al hacer clic en el id. de la aplicación aparecerán los detalles de la misma y su id.:

    ![][11]

    ![][12]

4.  Haga clic en **Edit**, luego desplácese al final de la pantalla y haga clic en **Create Certificate...**, debajo de la sección **Development Push SSL Certificate**.

    ![][13]

    Se mostrará el asistente "Add iOS Certificate".

    ![][14]

    <div class="dev-callout"><b>Nota:</b>
<p>Este tutorial usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producci&oacute;n. Aseg&uacute;rese de que establece el mismo tipo de certificado cuando carga el certificado en Servicios m&oacute;viles.</p>
</div>

5.  Haga clic en **Choose File**, diríjase a la ubicación en la que guardó el archivo CSR que creó en la primera tarea y, a continuación, haga clic en **Generate**.

    ![][15]

6.  Una vez que el portal haya creado el certificado, haga clic en **Download** y, a continuación, en **Done**.

    ![][16]

    ![][17]

    De esta forma, se descarga el certificado de firma y se guarda en la carpeta **Downloads** de su equipo.

    ![][18]

    <div class="dev-callout"><b>Nota:</b>
<p>De forma predeterminada, el certificado de desarrollo del archivo descargado tiene el nombre <strong>aps_development.cer</strong>.</p>
</div>

7.  Haga doble clic en el certificado de inserción descargado **aps\_development.cer**.

    De esta forma, se instala un nuevo certificado en las llaves, como se muestra a continuación:

    ![][19]

    > [WACOM.NOTE]
    > El nombre del certificado puede ser distinto, pero tendrá el prefijo de los **servicios de notificaciones de inserción de iOS de desarrollo de Apple:**.

    A continuación, usará este certificado para generar un archivo .p12 y cargarlo en su centro de notificaciones para habilitar las notificaciones de inserción mediante APNS.

## <a name="profile"></a><span class="short-header">Aprovisionamiento de la aplicación</span>Creación de un perfil de aprovisionamiento para la aplicación

1.  Vuelva al [portal de aprovisionamiento de iOS][portal de aprovisionamiento de iOS] (en inglés), seleccione **Provisioning Profiles**, seleccione **All** y, a continuación, haga clic en el botón **+** para crear un nuevo perfil. De esta forma, aparecerá el asistente **Add iOS Provisioning Profile**.

    ![][20]

2.  Seleccione **iOS App Development** en **Development** como tipo de perfil de aprovisionamiento y haga clic en **Continue**.

    ![][21]

3.  A continuación, seleccione el identificador de la aplicación para la aplicación Mobile Services Quickstart en la lista desplegable **App ID** y haga clic en **Continue**.

    ![][22]

4.  En la pantalla **Select certificates**, seleccione el certificado creado anteriormente y haga clic en **Continue**.

    ![][23]

5.  A continuación, seleccione los **dispositivos** que usará para la prueba y haga clic en **Continue**.

    ![][24]

6.  Para terminar, seleccione un nombre para el perfil en **Profile Name** y haga clic en **Generate** y en **Done**.

    ![][25]

    ![][26]

    De esta forma, se creará un nuevo perfil de aprovisionamiento.

7.  En Xcode, abra el organizador, seleccione la vista de dispositivos, seleccione **Provisioning Profiles** en la sección **Library** del panel izquierdo e importe el perfil de aprovisionamiento que acaba de crear.

8.  En la izquierda, seleccione el dispositivo e importe de nuevo el perfil de aprovisionamiento.

9.  En Acceso a llaves, haga clic con el botón secundario en el nuevo certificado, haga clic en **Export**, escriba un nombre para el certificado, seleccione el formato **.p12** y, a continuación, haga clic en **Save**.

    ![][27]

    Anote el nombre de archivo y la ubicación del certificado exportado.

De esta forma, se garantiza que el proyecto de Xcode usa el nuevo perfil para la firma de código. A continuación, debe cargar el certificado en su centro de notificaciones.

## <a name="configure-hub"></a><span class="short-header">Configuración de su Centro de notificaciones</span>Configuración de su Centro de notificaciones

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure] y haga clic en **+NEW** en la parte inferior de la pantalla.

2.  Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y finalmente en **Quick Create**.

    ![][28]

3.  Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Create a new Notification Hub**.

    ![][29]

4.  Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, a continuación, haga clic en la pestaña **Configure** en la parte superior.

    ![][30]

5.  Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

    ![][31]

6.  Seleccione la pestaña **Configure** en la parte superior y, a continuación, haga clic en **Upload** para la configuración de notificaciones de Apple. A continuación, seleccione el certificado **.p12** que exportó anteriormente y la contraseña para el certificado. Asegúrese de seleccionar si desea usar el servicio de inserción **Production** (si desea enviar notificaciones de inserción a usuarios que adquirieron la aplicación desde la tienda) o **Sandbox** (durante el desarrollo).

    ![][32]

7.  Haga clic en la pestaña **Panel** en la parte superior y, a continuación, haga clic en **Connection Information**. Anote las dos cadenas de conexión.

    ![][33]

Su centro de notificaciones está ahora configurado para funcionar con APN y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones.

## <a name="connecting-app"></a><span class="short-header">Conexión de su aplicación</span>Conexión de su aplicación al Centro de notificaciones

### Descargue la biblioteca WindowsAzure.Messaging.

Este ensamblado proporciona una forma sencilla de registrarse con Centros de notificaciones de Azure. Puede descargarse siguiendo las instrucciones de abajo y también se puede encontrar en la [descarga de ejemplo][aplicación NotificationHubs].

1.  Descargue el código fuente de [WindowsAzure.Messaging][WindowsAzure.Messaging] desde GitHub.

2.  Compile el proyecto y localice el ensamblado de salida **WindowsAzure.Messaging.dll**, ya que será necesario al configurar su aplicación de Xamarin.iOS más abajo.

### Crear un nuevo proyecto

1.  En Xamarin Studio, cree un nuevo proyecto iOS y seleccione la plantilla **Single View Application**.

    ![][34]

2.  En primer lugar, agregue una referencia al componente de Servicios móviles de Azure. En la vista Solution, haga clic con el botón secundario en la carpeta **Components** de su proyecto y elija **Get More Components**. Busque el componente **Servicios móviles de Azure** y agréguelo a su proyecto.

3.  Ahora, agregue una referencia a la biblioteca WindowsAzure.Messaging que descargó antes. Haga clic con el botón secundario en la carpeta **References** y seleccione **Edit References...**. En la pestaña **.Net Assembly**, localice el archivo **WindowsAzure.Messaging.dll** y

4.  En **AppDelegate.cs**, agregue las siguientes instrucciones using:

        using Microsoft.WindowsAzure.MobileServices;

    using WindowsAzure.Messaging;

5.  Declare una instancia de **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

6.  Cree una clase **Constants.cs** con las variables siguientes:

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";

7.  En **AppDelegate.cs**, actualice **FinishedLaunching()** para que coincida con lo siguiente:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

8.  Reemplace el método **RegisteredForRemoteNotifications()** de **AppDelegate.cs**:

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

9.  Reemplace el método **ReceivedRemoteNotification()** de **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

10. Cree el método **ProcessNotification()** siguiente en **AppDelegate.cs**:

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

    <div class="dev-callout"><b>Nota:</b>
<p>Puede elegir la invalidaci&oacute;n de **FailedToRegisterForRemoteNotifications()** para controlar situaciones, por ejemplo, cuando no haya conexi&oacute;n de red, entre otras.</p>
</div>

11. Ejecute la aplicación en el dispositivo.

## <a name="send"></a><span class="short-header">Envío de notificaciones</span>Envío de notificaciones desde su back-end

Puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end que use la [interfaz REST][interfaz REST]. En este tutorial enviará notificaciones con una aplicación de consola .NET y con un servicio móvil mediante un script de Node.

Para enviar notificaciones mediante una aplicación .NET:

1.  Cree una aplicación de consola nueva de Visual C#:

    ![][35]

2.  Agregue una referencia al SDK de Bus de servicio de Azure con el [paquete WindowsAzure.ServiceBus NuGet][paquete WindowsAzure.ServiceBus NuGet]. En el menú principal de Visual Studio, haga clic en **Tools**, luego en **Library Package Manager** y finalmente en **Package Manager Console**. Posteriormente, en la ventana de la consola, escriba:

        Install-Package WindowsAzure.ServiceBus and press Enter.

3.  Abra el archivo Program.cs y agregue la siguiente instrucción using:

        using Microsoft.ServiceBus.Notifications;

4.  En la clase `Program`, agregue el siguiente método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

5.  Posteriormente, agregue la siguiente línea al método `Main`:

         SendNotificationAsync();
         Console.ReadLine();

6.  Presione la tecla F5 para ejecutar la aplicación. Debe recibir una alerta en el dispositivo. Si usa Wi-Fi, asegúrese de que la conexión funciona.

Puede buscar todas las cargas posibles en la guía [Local and Push Notification Programming Guide][Local and Push Notification Programming Guide] de Apple.

Para enviar una notificación usando un servicio móvil, siga la [Introducción a los Servicios móviles][Introducción a los Servicios móviles] y, a continuación:

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure] y seleccione su servicio móvil.

2.  Seleccione la pestaña **Programador** en la parte superior.

    ![][36]

3.  Cree un nuevo trabajo programado, inserte un nombre y, a continuación, seleccione **On demand**.

    ![][37]

4.  Cuando se cree el trabajo, haga clic en el nombre del trabajo. Luego, haga clic en la pestaña **Script** de la barra superior.

5.  Inserte el siguiente script en su función de programador. Asegúrese de reemplazar los marcadores de posición por su nombre del centro de notificaciones y la cadena de conexión para *DefaultFullSharedAccessSignature* que obtuvo anteriormente. Haga clic en **Save**.

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

6.  Haga clic en **Run Once** en la barra inferior. Debe recibir una alerta en el dispositivo.

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, se difunden notificaciones a todos los dispositivos iOS. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios][Uso de Centros de notificaciones para insertar notificaciones en los usuarios], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora][Uso de los Centros de notificaciones para enviar noticias de última hora]. Para obtener más información sobre el uso de los Centros de notificaciones, consulte la [información general acerca de los centros de notificaciones de Azure][información general acerca de los centros de notificaciones de Azure] y los [procedimientos de los Centros de notificaciones para iOS][procedimientos de los Centros de notificaciones para iOS] (en inglés).

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Windows Universal]: /es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /es-es/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /es-es/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /es-es/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /es-es/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [aplicación NotificationHubs]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [Generación del archivo de solicitud de firma de certificado]: #certificates
  [Registro de la aplicación y habilitación para las notificaciones de inserción]: #register
  [Creación de un perfil de aprovisionamiento para la aplicación]: #profile
  [Configuración de su Centro de notificaciones]: #configure-hub
  [Conexión de su aplicación al Centro de notificaciones]: #connecting-app
  [Envío de notificaciones desde su back-end]: #send
  [XCode 5.0]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [1]: http://xamarin.com/download
  [Componente de Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F
  [Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  []: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
  [2]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
  [3]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png
  [portal de aprovisionamiento de iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [4]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
  [5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
  [6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
  [7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
  [8]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
  [9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
  [10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
  [11]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
  [12]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
  [13]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
  [14]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
  [15]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png
  [16]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
  [17]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png
  [18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
  [19]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
  [20]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
  [21]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
  [22]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
  [23]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
  [24]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
  [25]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
  [26]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png
  [27]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [30]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
  [34]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [interfaz REST]: http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx
  [35]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
  [paquete WindowsAzure.ServiceBus NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-xamarin-ios
  [36]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
  [37]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
  [Uso de Centros de notificaciones para insertar notificaciones en los usuarios]: /es-es/manage/services/notification-hubs/notify-users-aspnet
  [Uso de los Centros de notificaciones para enviar noticias de última hora]: /es-es/manage/services/notification-hubs/breaking-news-dotnet
  [información general acerca de los centros de notificaciones de Azure]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
  [procedimientos de los Centros de notificaciones para iOS]: http://msdn.microsoft.com/es-es/library/jj927168.aspx
