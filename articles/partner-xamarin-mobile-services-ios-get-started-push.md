<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-ios" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications (Xamarin.iOS) - Mobile Services" metaKeywords="" description="Learn how to use push notifications in Xamarin.iOS apps with Azure Mobile Services." metaCanonical="" disqusComments="0" umbracoNaviHide="1" editor="mollybos" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="" />

# Introducción a las notificaciones de inserción en Servicios móviles
<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/develop/mobile/tutorials/get-started-with-push-dotnet" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-js" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<p>TEste tema muestra cómo puede usar Servicios móviles Azure para enviar notificaciones de inserción a una aplicación Xamarin.iOS. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Apple (APNS). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.</p>

   <div class="dev-callout"><b>Nota</b>
   <p>En este tutorial se presenta una manera simplificada de enviar notificaciones de inserción que consiste en adjuntar un token de dispositivo de notificaciones de inserción al registro insertado. Asegúrese de realizar también el tutorial siguiente para obtener más información acerca de cómo incorporar notificaciones de inserción a aplicaciones reales.</p>
   </div>

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Generación del archivo de solicitud de firma de certificado](#certificates)
2. [Registro de la aplicación y habilitación para las notificaciones de inserción](#register)
3. [Creación de un perfil de aprovisionamiento para la aplicación](#profile)
4. [Configuración de Servicios móviles](#configure)
5. [Incorporación de notificaciones de inserción a la aplicación](#add-push)
6. [Actualización de scripts para enviar notificaciones de inserción](#update-scripts)
7. [Inserción de datos para recibir notificaciones](#test)

Este tutorial requiere lo siguiente:

+ [XCode 5.0](https://go.microsoft.com/fwLink/p/?LinkID=266532)
+ Dispositivo compatible con iOS 5.0 (o una versión posterior)
+ Pertenencia al programa para desarrolladores de iOS
+ [Xamarin.iOS]
+ [Componente de Servicios móviles Azure](http://components.xamarin.com/view/azure-mobile-services/)

   <div class="dev-callout"><b>Nota</b>
   <p>Debido a los requisitos de configuración de las notificaciones de inserción, debe implementar y probar las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.</p>
   </div>

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started-xamarin-ios).

El servicio de notificaciones de inserción de Apple (APNS) usa certificados para autenticar su servicio móvil. Siga estas instrucciones para crear los certificados necesarios y cargarlos en su servicio móvil. Para consultar la documentación oficial de la característica APNS, consulte [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

<h2><a name="certificates"></a><span class="short-header">Generación de un archivo CSR</span>Generación del archivo de solicitud de firma de certificado</h2>

Primero debe generar el archivo de solicitud de firma de certificado (CSR) que Apple usa para generar un certificado firmado.

1. En la carpeta Utilities, ejecute la herramienta Acceso a llaves.

2. Haga clic en **Acceso a llaves**, expanda **Certificate Assistant** y, a continuación, haga clic en **Request a Certificate from a Certificate Authority...**.

  	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png)

3. Seleccione una dirección de correo electrónico de usuario en **User Email Address**, escriba un nombre común en **Common Name** y una dirección de correo electrónico en **CA Email Address**, asegúrese de que **Saved to disk** se encuentra seleccionado, y, a continuación, haga clic en **Continue**.

  	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png)

4. Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Save As**, seleccione la ubicación en **Where** y, a continuación, haga clic en **Save**.

  	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png)
  
  	De esta forma, se guarda el archivo CSR en la ubicación seleccionada; el Escritorio es la ubicación predeterminada. Recuerde la ubicación seleccionada para este archivo.

A continuación, se registrará la aplicación en Apple, se habilitarán las notificaciones de inserción y se cargará este archivo CSR exportado para crear un certificado de inserción.

<h2><a name="register"></a><span class="short-header">Registro de la aplicación</span>Registro de la aplicación para las notificaciones de inserción</h2>

Para poder enviar notificaciones de inserción a una aplicación iOS desde servicios móviles, debe registrar su aplicación con Apple y también registrar las notificaciones de inserción.

1. Si aún no ha registrado su aplicación, diríjase al [portal de aprovisionamiento de iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) (en inglés) del Centro para desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identifiers** y, a continuación, en **App IDs**. Para finalizar, haga clic en el signo **+** para registrar una nueva aplicación.

  	![][102] 

2. Escriba un nombre para su aplicación en **Description**, especifique el valor *MobileServices.Quickstart* en **Bundle Identifier**, haga clic en la opción de notificaciones de inserción "Push Notifications" de la sección de servicios de aplicaciones "App Services" y, a continuación, haga clic en **Continue**. En este ejemplo se usa el identificador **MobileServices.Quickstart** pero es posible que no pueda volver a usar ese mismo identificador, ya que los identificadores de aplicaciones deben ser exclusivos entre todos los usuarios. Por ese motivo, es recomendable que agregue su nombre completo o sus iniciales después del nombre de la aplicación.

   	![][103]
   
   	De esta forma, se genera el identificador de la aplicación y se solicita que envíe (**Submit**) la información. Haga clic en **Submit**.
   
   	![][104] 
   
   	Una vez que haga clic en **Submit**, verá la pantalla **Registration complete** como se muestra a continuación. Haga clic en **Done**.
   
   	![][105]

	Nota: si elige proporcionar un valor **Bundle Identifier** distinto a *MobileServices.Quickstart*, también debe actualizar el identificador de agrupación de trabajos en el proyecto de Xcode.
    

3. Busque el identificador de la aplicación que acaba de crear y haga clic en su fila.

   	![][106]
   
   	Si hace clic en el identificador de la aplicación, se mostrará información sobre la aplicación y el identificador de la aplicación. Haga clic en el botón **Settings**.
   
   	![][107] 

4. Desplácese a la parte inferior de la pantalla y haga clic en el botón **Create Certificate...** en la sección **Development Push SSL Certificate**.

   	![][108] 

   	Se mostrará el asistente "Add iOS Certificate".
   
   	![][108] 

    Nota: este tutorial usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producción. Asegúrese de que establece el mismo tipo de certificado cuando carga el certificado en Servicios móviles.

5. Haga clic en **Choose File**, diríjase a la ubicación en la que guardó el archivo CSR que creó en la primera tarea y, a continuación, haga clic en **Generate**.

  	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png)

6. Una vez que el portal haya creado el certificado, haga clic en el botón **Download** y haga clic en **Done**.

  	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png)

  	De esta forma, se descarga el certificado de firma y se guarda en su equipo en la carpeta de descargas.

  	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png)

    Nota: de manera predeterminada, el certificado de desarrollo del archivo descargado se llama <strong>aps_development.cer</strong>.

7. Haga doble clic en el certificado de inserción descargado **aps\_development.cer**.

   	De esta forma, se instala un nuevo certificado en las llaves como se muestra a continuación:

   	![][10]

    Nota: el nombre del certificado puede ser distinto, pero tendrá el prefijo de los **servicios de notificaciones de inserción de iOS de desarrollo de Apple:**

A continuación, usará este certificado para generar un archivo .p12 y cargarlo en Servicios móviles para permitir la autenticación con APNS.

<h2><a name="profile"></a><span class="short-header">Aprovisionamiento de la aplicación</span>Creación de un perfil de aprovisionamiento para la aplicación</h2>

1. Vuelva al [portal de aprovisionamiento de iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) (en inglés), seleccione **Provisioning Profiles**, seleccione **All** y, a continuación, haga clic en el botón **+** para crear un nuevo perfil. De esta forma, se iniciará el asistente de **Add iOS Provisiong Profile**.

   	![][112]

2. Seleccione **iOS App Development** en **Development** como tipo de perfil de aprovisionamiento y haga clic en **Continue**.

   	![][113]

3. A continuación, seleccione el identificador de la aplicación para la aplicación Mobile Services Quickstart en la lista desplegable **App ID** y haga clic en **Continue**.

   	![][114]

4. En la pantalla **Select certificates**, seleccione el certificado creado anteriormente y haga clic en **Continue**.

   	![][115]

5. A continuación, seleccione los **dispositivos** que usará para la prueba y haga clic en **Continue**.

   	![][116]

6. Para terminar, seleccione un nombre para el perfil en **Profile Name** y haga clic en **Generate** y en **Done**.

   	![][117]

   	De esta forma, se creará un nuevo perfil de aprovisionamiento.

7. En Xcode, abra el organizador, seleccione la vista de dispositivos, seleccione **Provisioning Profiles** en la sección **Library** del panel izquierdo y, a continuación, haga clic en el botón **Refresh** en la parte inferior del panel intermedio.

   	![][101]

8. En **Targets**, haga clic en **Quickstart**, expanda **Code Signing Identity** y, a continuación, seleccione el nuevo perfil en **Debug**.

   	![][17]

De esta forma, se garantiza que el proyecto de Xcode usa el nuevo perfil para la firma de código. A continuación, debe cargar el certificado en Servicios móviles.

<a name="configure"></a><h2><span class="short-header">Configuración del servicio</span>Configuración de Servicios móviles para enviar solicitudes de inserción</h2>

Una vez que haya registrado su aplicación con APNS y haya configurado su proyecto, debe configurar su servicio móvil para la integración con APNS.

1. En Acceso a llaves, haga clic con el botón secundario en el nuevo certificado, haga clic en **Export**, utilice un nombre para el archivo QuickstartPusher, seleccione el formato **.p12** y, a continuación, haga clic en **Save**.

   	![][28]

  	Anote el nombre de archivo y la ubicación del certificado exportado.

    <div class="dev-callout"><b>Nota:</b>
	<p>En este tutorial se crea un archivo QuickstartPusher.p12. El nombre y la ubicación del archivo pueden ser diferentes.</p>
    </div>

2. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

   	![][18]

3. Haga clic en la pestaña **Push** y, a continuación, en **Upload**.

   	![][19]

   	Se mostrará el cuadro de diálogo Upload Certificate.

4. Haga clic en **File**, seleccione el archivo QuickstartPusher.p12 del certificado exportado y especifique la contraseña en **Password**. Asegúrese de que selecciona el modo correcto en **Mode**, haga clic en el icono de marca de verificación y, a continuación, haga clic en **Save**.

   	![][20] 

    <div class="dev-callout"><b>Nota</b>
	<p>Este tutorial usa certificados de desarrollo.</p>
    </div>

El servicio móvil está configurado ahora para que funcione con APNS.

<a name="add-push"></a><h2><span class="short-header">Corporación de notificaciones de inserción</span>Incorporación de notificaciones de inserción a la aplicación</h2>

1. En Xamarin.Studio, abra el archivo AppDelegate.cs y agregue la siguiente propiedad:

        public string DeviceToken { get; set; }

2. Abra la clase **TodoItem** y agregue la siguiente propiedad:

        [DataMember(Name = "deviceToken")]
        public string DeviceToken { get; set; }

    <div class="dev-callout"><b>Nota</b>
	<p> Si su servicio móvil tiene habilitado el esquema dinámico, se agregará automáticamente una nueva columna “deviceToken” a la tabla <strong>TodoItem</strong> t cuando se inserte un elemento nuevo que contenga esta propiedad.</p>
    </div> 

3. En **AppDelegate**, sobrescriba el evento **FinishedLaunching**:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

4. En **AppDelegate**, sobrescriba el evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            string trimmedDeviceToken = deviceToken.Description;
            if (!string.IsNullOrWhiteSpace(trimmedDeviceToken))
            {
                trimmedDeviceToken = trimmedDeviceToken.Trim('<');
                trimmedDeviceToken = trimmedDeviceToken.Trim('>');
            }
            DeviceToken = trimmedDeviceToken;
        }

5. En **AppDelegate**, sobrescriba el evento **ReceivedRemoteNotification**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            Debug.WriteLine(userInfo.ToString());
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

6. En **TodoListViewController**, modifique la acción **OnAdd** para que el token del dispositivo se almacene en **AppDelegeate**, y almacénelo en la clase **TodoItem** que se está agregando.

      string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

			var newItem = new TodoItem() 
			{
				Text = itemText.Text, 
				Complete = false,
                DeviceToken = deviceToken
			};

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción.

<h2><a name="update-scripts"></a><span class="short-header">Actualización del script de inserción</span>Actualización del script de inserción registrado en el Portal de administración</h2>

1. En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

   	![][21]

2. En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insert**.

   	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png)

   	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

        function insert(item, user, request) {
            request.execute();
            // Establecer el tiempo de espera para retrasar la notificación y proporcionar tiempo para que la 
            // aplicación se cierre en el dispositivo y se muestren las notificaciones del sistema
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

   	De esta forma, se registra un nuevo script de inserción, que usa [apns object] para enviar una notificación de inserción (el texto insertado) al dispositivo proporcionado en la solicitud de inserción. 


   	<div class="dev-callout"><b>Note</b>
   <p>Este script retrasa el envío de la notificación para proporcionarle tiempo para cerrar la aplicación y recibir una notificación del sistema.</p>
   </div>

<h2><a name="test"></a><span class="short-header">Prueba de la aplicación</span>Pruebas de notificaciones de inserción en su aplicación</h2>

1. Presione el botón **Run** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **OK** para aceptar las notificaciones de inserción.

   	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png)

    <div class="dev-callout"><b>Nota:</b>
    <p>Debe aceptar las notificaciones de inserción de la aplicación de forma explícita. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.</p>
    </div>

2. En la aplicación, escriba un texto significativo, como *Una nueva tarea de Servicios móviles* y, a continuación, haga clic en el icono del signo más (**+**).

   	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png)

3. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **OK** para descartarla.

   	![][25]

4. Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente notificación.

  	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png)

Ha completado correctamente este tutorial.

## Obtención del ejemplo completo
Descargue el [proyecto de ejemplo completo](http://go.microsoft.com/fwlink/p/?LinkId=331303). Asegúrese de actualizar las variables **applicationURL** y **applicationKey** con su propia configuración de Azure.

## <a name="next-steps"> </a>Pasos siguientes

En este ejemplo simple, un usuario recibe una notificación de inserción con los datos que se acaban de insertar. El cliente en la solicitud suministra al servicio móvil el token de dispositivo que APNS utiliza. En el siguiente tutorial, [Notificaciones de inserción para usuarios de la aplicación](/es-es/develop/mobile/tutorials/push-notifications-to-users-ios), creará una tabla de dispositivos independiente en la que puede almacenar tokens de dispositivos y enviar una notificación de inserción a todos los canales almacenados cuando se produce una inserción.

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->




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
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
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

[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: /es-es/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /es-es/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push notifications to app users]: /es-es/develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios


[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
