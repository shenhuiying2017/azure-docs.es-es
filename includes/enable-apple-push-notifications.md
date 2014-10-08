El servicio de notificaciones de inserción de Apple (APNS) usa certificados para autenticar su servicio móvil. Siga estas instrucciones para crear los certificados necesarios y cargarlos en su servicio móvil. Para consultar la documentación oficial de la característica APNS, consulte [Apple Push Notification Service][].

## Generación del archivo de solicitud de firma de certificado

Primero debe generar el archivo de solicitud de firma de certificado (CSR) que Apple usa para generar un certificado firmado.

1.  En la carpeta Utilities, ejecute la herramienta Acceso a llaves.

2.  Haga clic en **Acceso a llaves**, expanda **Certificate Assistant** y, a continuación, haga clic en **Request a Certificate from a Certificate Authority...**.

    ![][]

3.  Seleccione una dirección de correo electrónico de usuario en **User Email Address** y un nombre común en **Common Name**, asegúrese de que **Saved to disk** se encuentra seleccionado y, a continuación, haga clic en **Continue**. Deje el campo **CA Email Address** en blanco, ya que no es obligatorio.

    ![][1]

4.  Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Save As**, seleccione la ubicación en **Where** y, a continuación, haga clic en **Save**.

    ![][2]

    De esta forma, se guarda el archivo CSR en la ubicación seleccionada; el Escritorio es la ubicación predeterminada. Recuerde la ubicación seleccionada para este archivo.

A continuación, se registrará la aplicación en Apple, se habilitarán las notificaciones de inserción y se cargará este archivo CSR exportado para crear un certificado de inserción.

## Registro de la aplicación para las notificaciones de inserción

Para poder enviar notificaciones de inserción a una aplicación iOS desde servicios móviles, debe registrar su aplicación con Apple y también registrar las notificaciones de inserción.

1.  Si aún no ha registrado su aplicación, diríjase al [portal de aprovisionamiento de iOS][] (en inglés) del Centro para desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identifiers** y, a continuación, en **App IDs**. Para finalizar, haga clic en el signo **+** para registrar una nueva aplicación.

    ![][3]

2.  Escriba un nombre para su aplicación en **Description**, especifique el valor *MobileServices.Quickstart* en **Bundle Identifier**, haga clic en la opción de notificaciones de inserción "Push Notifications" de la sección de servicios de aplicaciones "App Services" y, a continuación, haga clic en **Continue**. En este ejemplo se usa el identificador **MobileServices.Quickstart** pero es posible que no pueda volver a usar ese mismo identificador, ya que los identificadores de aplicaciones deben ser exclusivos entre todos los usuarios. Por ese motivo, es recomendable que agregue su nombre completo o sus iniciales después del nombre de la aplicación.

    ![][4]

    De esta forma, se genera el identificador de la aplicación y se solicita que **envíe** la información. Haga clic en **Enviar**.

    ![][5]

    Una vez que haga clic en **Submit**, verá la pantalla **Registration complete**, como se muestra a continuación. Haga clic en **Done**.

    ![][6]

    > [WACOM.NOTE] Si elige proporcionar un valor **Bundle Identifier** distinto a *MobileServices.Quickstart*, también debe actualizar el identificador de agrupación de trabajos en el proyecto de Xcode.

3.  Busque el identificador de la aplicación que acaba de crear y haga clic en su fila.

    ![][7]

    Si hace clic en el identificador de la aplicación, se mostrará información sobre la aplicación y el identificador de la aplicación. Haga clic en el botón **Configuración**.

    ![][8]

4.  Desplácese a la parte inferior de la pantalla y haga clic en el botón **Create Certificate...** en la sección **Development Push SSL Certificate**.

    ![][9]

    Se mostrará el asistente "Add iOS Certificate".

    > [WACOM.NOTE] Este tutorial usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producción. Asegúrese de que establece el mismo tipo de certificado cuando carga el certificado en Servicios móviles.

5.  Haga clic en **Choose File**, diríjase a la ubicación en la que guardó el archivo CSR que creó en la primera tarea y, a continuación, haga clic en **Generate**.

    ![][10]

6.  Una vez que el portal haya creado el certificado, haga clic en el botón **Download** y haga clic en **Done**.

    ![][11]

    De esta forma, se descarga el certificado de firma y se guarda en su equipo en la carpeta de descargas.

    ![][12]

    > [WACOM.NOTE] De forma predeterminada, el certificado de desarrollo del archivo descargado tiene el nombre **aps\_development.cer**.

7.  Haga doble clic en el certificado de inserción descargado **aps\_development.cer**.

    De esta forma, se instala un nuevo certificado en las llaves, como se muestra a continuación:

    ![][13]

    > [WACOM.NOTE] El nombre del certificado puede ser distinto, pero tendrá el prefijo de los **servicios de notificaciones de inserción de iOS de desarrollo de Apple:**

A continuación, usará este certificado para generar un archivo .p12 y cargarlo en Servicios móviles para permitir la autenticación con APNS.

## Creación de un perfil de aprovisionamiento para la aplicación

1.  Vuelva al [portal de aprovisionamiento de iOS][] (en inglés), seleccione **Provisioning Profiles**, seleccione **All** y, a continuación, haga clic en el botón **+** para crear un nuevo perfil. De esta forma, se iniciará el asistente de **Add iOS Provisiong Profile**.

    ![][14]

2.  Seleccione **iOS App Development** en **Development** como tipo de perfil de aprovisionamiento y haga clic en **Continue**.

    ![][15]

3.  A continuación, seleccione el identificador de la aplicación para la aplicación Mobile Services Quickstart en la lista desplegable **App ID** y haga clic en **Continue**.

    ![][16]

4.  En la pantalla **Select certificates**, seleccione el certificado creado anteriormente y haga clic en **Continue**.

    ![][17]

5.  A continuación, seleccione los **dispositivos** que usará para la prueba y haga clic en **Continue**.

    ![][18]

6.  Para terminar, seleccione un nombre para el perfil en **Profile Name** y haga clic en **Generate** y en **Done**.

    ![][19]

    De esta forma, se creará un nuevo perfil de aprovisionamiento.

7.  En Xcode, abra el organizador, seleccione la vista de dispositivos, seleccione **Provisioning Profiles** en la sección **Library** del panel izquierdo y, a continuación, haga clic en el botón **Refresh** en la parte inferior del panel intermedio.

    ![][20]

8.  En **Targets**, haga clic en **Quickstart**, expanda **Code Signing Identity** y, a continuación, seleccione el nuevo perfil en **Debug**.

    ![][21]

De esta forma, se garantiza que el proyecto de Xcode usa el nuevo perfil para la firma de código. A continuación, debe cargar el certificado en Azure.

  [Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  []: ./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png
  [1]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png
  [2]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png
  [portal de aprovisionamiento de iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [3]: ./media/enable-apple-push-notifications/mobile-services-ios-push-02.png
  [4]: ./media/enable-apple-push-notifications/mobile-services-ios-push-03.png
  [5]: ./media/enable-apple-push-notifications/mobile-services-ios-push-04.png
  [6]: ./media/enable-apple-push-notifications/mobile-services-ios-push-05.png
  [7]: ./media/enable-apple-push-notifications/mobile-services-ios-push-06.png
  [8]: ./media/enable-apple-push-notifications/mobile-services-ios-push-07.png
  [9]: ./media/enable-apple-push-notifications/mobile-services-ios-push-08.png
  [10]: ./media/enable-apple-push-notifications/mobile-services-ios-push-10.png
  [11]: ./media/enable-apple-push-notifications/mobile-services-ios-push-11.png
  [12]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png
  [13]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png
  [14]: ./media/enable-apple-push-notifications/mobile-services-ios-push-12.png
  [15]: ./media/enable-apple-push-notifications/mobile-services-ios-push-13.png
  [16]: ./media/enable-apple-push-notifications/mobile-services-ios-push-14.png
  [17]: ./media/enable-apple-push-notifications/mobile-services-ios-push-15.png
  [18]: ./media/enable-apple-push-notifications/mobile-services-ios-push-16.png
  [19]: ./media/enable-apple-push-notifications/mobile-services-ios-push-17.png
  [20]: ./media/enable-apple-push-notifications/mobile-services-ios-push-01.png
  [21]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png
