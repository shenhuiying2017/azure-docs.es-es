<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="" solutions="" manager="" editor="" />

Introducción a las notificaciones de inserción en Servicios móviles
===================================================================

[C\# de Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[JavaScript de Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/es-es/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/es-es/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/es-es/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de iOS. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Apple (APNS). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Puede ver la versión en vídeo de este tutorial haciendo clic en el clip de la derecha.

[Ver el tutorial](http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services) [Reproducir vídeo](http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services) 10:37

> [WACOM.NOTE] Este tema muestra cómo habilitar las notificaciones de inserción mediante la compatibilidad heredada que brinda Servicios móviles. Centros de notificaciones de Azure se integra con Servicios móviles para permitirle enviar notificaciones de inserción basadas en plantillas entre plataformas a millones de dispositivos. Las notificaciones de inserción mediante Centros de notificaciones no están habilitadas de forma predeterminada y actualmente no se ofrece compatibilidad con Centro de notificaciones para iOS en las bibliotecas de Servicios móviles. Sin embargo, puede enviar notificaciones de inserción desde su servicio móvil si usa las bibliotecas del Centro de notificaciones. Para obtener más información, consulte [Introducción a los Centros de notificaciones](/es-es/documentation/articles/notification-hubs-ios-get-started/).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Generación del archivo de solicitud de firma de certificado](#certificates)
2.  [Registro de la aplicación para las notificaciones de inserción](#register)
3.  [Creación de un perfil de aprovisionamiento para la aplicación](#profile)
4.  [Configuración de Servicios móviles](#configure)
5.  [Incorporación de notificaciones de inserción a la aplicación](#add-push)
6.  [Actualización de scripts para enviar notificaciones de inserción](#update-scripts)
7.  [Inserción de datos para recibir notificaciones](#test)

Este tutorial requiere lo siguiente:

-   [SDK de iOS para Servicios móviles](https://go.microsoft.com/fwLink/p/?LinkID=266533)
-   [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   Dispositivo compatible con iOS 5.0 (o una versión posterior)
-   Pertenencia al programa para desarrolladores de iOS

    > [WACOM.NOTE] Debido a los requisitos de la configuración de las notificaciones de inserción, debe implementar y realizar una prueba de las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started-ios).

El servicio de notificaciones de inserción de Apple (APNS) usa certificados para autenticar su servicio móvil. Siga estas instrucciones para crear los certificados necesarios y cargarlos en su servicio móvil. Para consultar la documentación oficial de la característica APNS, consulte [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Generación del archivo de solicitud de firma de certificado
-----------------------------------------------------------

Primero debe generar el archivo de solicitud de firma de certificado (CSR) que Apple usa para generar un certificado firmado.

1.  En la carpeta Utilities, ejecute la herramienta Acceso a llaves.

2.  Haga clic en **Acceso a llaves**, expanda **Certificate Assistant** y, a continuación, haga clic en **Request a Certificate from a Certificate Authority...**

	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png)

3.  Seleccione una dirección de correo electrónico de usuario en **User Email Address** y un nombre común en **Common Name**, asegúrese de que **Saved to disk** se encuentra seleccionado y, a continuación, haga clic en **Continue**. Deje el campo **CA Email Address** en blanco, ya que no es obligatorio.

	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png)

4.  Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Save As**, seleccione la ubicación en **Where** y, a continuación, haga clic en **Save**.

	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png)

	De esta forma, se guarda el archivo CSR en la ubicación seleccionada; el Escritorio es la ubicación predeterminada. Recuerde la ubicación seleccionada para este archivo.

A continuación, se registrará la aplicación en Apple, se habilitarán las notificaciones de inserción y se cargará este archivo CSR exportado para crear un certificado de inserción.

Registro de la aplicación para las notificaciones de inserción
--------------------------------------------------------------

Para poder enviar notificaciones de inserción a una aplicación iOS desde servicios móviles, debe registrar su aplicación con Apple y también registrar las notificaciones de inserción.

1.  Si aún no ha registrado su aplicación, diríjase al [portal de aprovisionamiento de iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) (en inglés) del Centro para desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identifiers** y, a continuación, en **App IDs**. Para finalizar, haga clic en el signo **+** para registrar una nueva aplicación.

	![][102] 

2.  Escriba un nombre para su aplicación en **Description**, especifique el valor *MobileServices.Quickstart* en **Bundle Identifier**, haga clic en la opción de notificaciones de inserción "Push Notifications" de la sección de servicios de aplicaciones "App Services" y, a continuación, haga clic en **Continue**. En este ejemplo se usa el identificador **MobileServices.Quickstart** pero es posible que no pueda volver a usar ese mismo identificador, ya que los identificadores de aplicaciones deben ser exclusivos entre todos los usuarios. Por ese motivo, es recomendable que agregue su nombre completo o sus iniciales después del nombre de la aplicación.

	![][103]
           
	De esta forma, se genera el identificador de la aplicación y se solicita que envíe (**Submit**) la información. Haga clic en **Submit**.
           
	![][104] 
           
	Una vez que haga clic en **Submit**, verá la pantalla **Registration complete** como se muestra a continuación. Haga clic en **Done**.
           
	![][105]

    > [WACOM.NOTE] Si elige proporcionar un valor **Bundle Identifier** distinto a *MobileServices.Quickstart*, también debe actualizar el identificador de agrupación de trabajos en el proyecto de Xcode.

3.  Busque el identificador de la aplicación que acaba de crear y haga clic en su fila.

	![][106]
           
	Si hace clic en el identificador de la aplicación, se mostrará información sobre la aplicación y el identificador de la aplicación. Haga clic en el botón **Settings**.
           
	![][107] 

4.  Desplácese a la parte inferior de la pantalla y haga clic en el botón **Create Certificate...** en la sección **Development Push SSL Certificate**.

	![][108] 

	Se mostrará el asistente "Add iOS Certificate".
           
	![][108] 

    > [WACOM.NOTE] Este tutorial usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producción. Asegúrese de que establece el mismo tipo de certificado cuando carga el certificado en Servicios móviles.

5.  Haga clic en **Choose File**, diríjase a la ubicación en la que guardó el archivo CSR que creó en la primera tarea y, a continuación, haga clic en **Generate**.

	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png)

1.  Una vez que el portal haya creado el certificado, haga clic en el botón **Download** y haga clic en **Done**.

	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png)

 De esta forma, se descarga el certificado de firma y se guarda en su equipo en la carpeta de descargas.
 
	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] De forma predeterminada, el certificado de desarrollo del archivo descargado tiene el nombre **aps_development.cer**.

1.  Haga doble clic en el certificado de inserción descargado **aps\_development.cer**.

	De esta forma, se instala un nuevo certificado en las llaves como se muestra a continuación:

	![][10]

    > [WACOM.NOTE] El nombre del certificado puede ser distinto, pero tendrá el prefijo de los **servicios de notificaciones de inserción de iOS de desarrollo de Apple:**

A continuación, usará este certificado para generar un archivo .p12 y cargarlo en Servicios móviles para permitir la autenticación con APNS.

Creación de un perfil de aprovisionamiento para la aplicación
-------------------------------------------------------------

1.  Vuelva al [portal de aprovisionamiento de iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) (en inglés), seleccione **Provisioning Profiles**, seleccione **All** y, a continuación, haga clic en el botón **+** para crear un nuevo perfil. De esta forma, se iniciará el asistente de **Add iOS Provisiong Profile**.

	![][112]

2.  Seleccione **iOS App Development** en **Development** como tipo de perfil de aprovisionamiento y haga clic en **Continue**.

	![][113]

3.  A continuación, seleccione el identificador de la aplicación para la aplicación Mobile Services Quickstart en la lista desplegable **App ID** y haga clic en **Continue**.

	![][114]

4.  En la pantalla **Select certificates**, seleccione el certificado creado anteriormente y haga clic en **Continue**.

	![][115]

5.  A continuación, seleccione los **dispositivos** que usará para la prueba y haga clic en **Continue**.

	![][116]

6.  Para terminar, seleccione un nombre para el perfil en **Profile Name** y haga clic en **Generate** y en **Done**.

	![][117]

	De esta forma, se creará un nuevo perfil de aprovisionamiento.

1.  En Xcode, abra el organizador, seleccione la vista de dispositivos, seleccione **Provisioning Profiles** en la sección **Library** del panel izquierdo y, a continuación, haga clic en el botón **Refresh** en la parte inferior del panel intermedio.

	![][101]

2.  En **Targets**, haga clic en **Quickstart**, expanda **Code Signing Identity** y, a continuación, seleccione el nuevo perfil en **Debug**.

	![][17]

	De esta forma, se garantiza que el proyecto de Xcode usa el nuevo perfil para la firma de código. A continuación, debe cargar el certificado en Servicios móviles.

	Configuración de Servicios móviles para enviar solicitudes de inserción
-----------------------------------------------------------------------

Una vez que haya registrado su aplicación con APNS y haya configurado su proyecto, debe configurar su servicio móvil para la integración con APNS.

1.  En Acceso a llaves, haga clic con el botón secundario en el nuevo certificado, haga clic en **Export**, utilice un nombre para el archivo QuickstartPusher, seleccione el formato **.p12** y, a continuación, haga clic en **Save**.

	![][28]

	Anote el nombre de archivo y la ubicación del certificado exportado.

    > [WACOM.NOTE] Con este tutorial se crea un archivo QuickstartPusher.p12. El nombre del archivo y la ubicación pueden ser diferentes.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

	![][18]

2.  Haga clic en la pestaña **Push** y, a continuación, en **Upload**.

	![][19]

    Se mostrará el cuadro de diálogo Upload Certificate.

3.  Haga clic en **File**, seleccione el archivo QuickstartPusher.p12 del certificado exportado y especifique la contraseña en **Password**. Asegúrese de que selecciona el modo correcto en **Mode**, haga clic en el icono de marca de verificación y, a continuación, haga clic en **Save**.

	![][20] 

    > [WACOM.NOTE] Este tutorial usa certificados de desarrollo.

El servicio móvil está configurado ahora para que funcione con APNS.

Incorporación de notificaciones de inserción a la aplicación
------------------------------------------------------------

1.  En Xcode, abra el archivo QSAppDelegate.h y agregue la siguiente propiedad debajo de la propiedad **\*window**:

         @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] Cuando su servicio móvil tenga habilitado el esquema dinámico, se agregará automáticamente una nueva columna 'deviceToken' a la tabla **TodoItem** cuando se inserte un elemento nuevo que contenga esta propiedad.

2.  En QSAppDelegate.m, reemplace el siguiente método de controlador dentro de la implementación:

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
         (NSDictionary *)launchOptions
         {
             // Registrar las notificaciones remotas
             [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
             UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
             return YES;
         }

3.  En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

         // Ya se ha realizado el registro. Ahora registre el token del dispositivo (como una cadena) en la instancia AppDelegate
         // teniendo en cuenta que debe eliminar primero los corchetes angulares.
         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
         (NSData *)deviceToken {
             NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
             self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
         }

4.  En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

         // Gestione los errores en el registro. En este caso, establecemos deviceToken en una cadena vacía
         // para evitar que se produzcan errores en la inserción.
         - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
         (NSError *)error {
             NSLog(@"Failed to register for remote notifications: %@", error);
             self.deviceToken = @"";
         }

5.  En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

         // Puesto que las alertas del sistema no funcionan cuando la aplicación se está ejecutando, la aplicación las gestiona.
         // De esta forma, se usa userInfo en la carga para mostrar la UIAlertView.
         - (void)application:(UIApplication *)application didReceiveRemoteNotification:
         (NSDictionary *)userInfo {
             NSLog(@"%@", userInfo);
             UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
             @"OK" otherButtonTitles:nil, nil];
             [alert show];
         }

6.  En QSTodoListViewController.m, importe el archivo QSAppDelegate.h para poder usar la delegación y obtener el token del dispositivo:

         #import "QSAppDelegate.h"

7.  En QSTodoListViewController.m, modifique la acción **(IBAction)onAdd** buscando la siguiente línea:

         NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

    Reemplace esto por el siguiente código:

         // Obtener una referencia a AppDelegate para recuperar fácilmente deviceToken
         QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

         NSDictionary *item = @{
             @"text" : itemText.text,
             @"complete" : @(NO),
             // agregar la propiedad del token del dispositivo a la carga de elementos todo
             @"deviceToken" : delegate.deviceToken
         };

    De esta forma, se agrega una referencia a **QSAppDelegate** para obtener el token del dispositivo y, a continuación, se modifica la carga de la solicitud para incluir el token del dispositivo.

       > [WACOM.NOTE] Debe agregar este código antes de llamar al método <strong>addItem</strong>.

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción.

Actualización del script de inserción registrado en el Portal de administración
-------------------------------------------------------------------------------

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

	![][21]

2.  En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insert**.

	![](./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png) 

	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

1.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

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


       > [WACOM.NOTE] Este script retrasa el envío de la notificación para proporcionarle tiempo para cerrar la aplicación y recibir una notificación del sistema.

Pruebas de notificaciones de inserción en su aplicación
-------------------------------------------------------

1.  Presione el botón **Run** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **OK** para aceptar las notificaciones de inserción.

	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png)

    > [WACOM.NOTE] Debe aceptar de forma explícita las notificaciones de inserción desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

1.  En la aplicación, escriba un texto significativo, como *Una nueva tarea de Servicios móviles* y, a continuación, haga clic en el icono del signo más (**+**).

	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png)

1.  Compruebe que se ha recibido la notificación y, a continuación, haga clic en **OK** para descartarla.

	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png)

1.  Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente notificación.

	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png)

Ha completado correctamente este tutorial.

Pasos siguientes
----------------

En este ejemplo simple, un usuario recibe una notificación de inserción con los datos que se acaban de insertar. El cliente en la solicitud suministra al servicio móvil el token de dispositivo que APNS utiliza. En el siguiente tutorial, [Notificaciones de inserción para usuarios de la aplicación](/es-es/develop/mobile/tutorials/push-notifications-to-users-ios), creará una tabla de dispositivos independiente en la que puede almacenar tokens de dispositivos y enviar una notificación de inserción a todos los canales almacenados cuando se produce una inserción.

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png



<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: /es-es/develop/mobile/tutorials/get-started-ios
[Get started with data]: /es-es/develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /es-es/develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /es-es/develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /es-es/develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333