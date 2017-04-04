
Para registrar la aplicación para notificaciones push mediante Apple Push Notification Service (APNS) es preciso crear un certificado push, un id. de la aplicación y un perfil de aprovisionamiento para el proyecto en el portal para desarrolladores de Apple.

El identificador de la aplicación contiene los valores de configuración que permiten a la aplicación enviar y recibir notificaciones push. Esta configuración incluye el certificado de notificaciones push necesario para realizar la autenticación APNS cuando la aplicación envía y recibe notificaciones push.

Para más información acerca de estos conceptos, consulte la documentación oficial de [Apple Push Notification Service](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

## <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Generación del archivo de solicitud de firma de certificados para el certificado push
Estos pasos le guían a través del proceso de creación de la solicitud de firma de certificados, que genera un certificado push que se utiliza con APNS.

1. En el Mac, ejecute la herramienta Acceso a llaves. Se puede activar desde la carpeta **Utilidades** o la carpeta **Otros** del panel de inicio.

2. Seleccione **Keychain Access** (Acceso con cadena de claves), expanda **Certificate Assistant** (Asistente para certificados) y, después, seleccione **Request a Certificate from a Certificate Authority...** (Solicitar un certificado a una entidad de certificación).

      ![Solicitar un certificado](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Seleccione **User Email Address** (Dirección de correo electrónico de usuario) y **Common Name** (Nombre común).

4. Asegúrese de que **Saved to disk** (Guardado en disco) está seleccionado y, después, seleccione **Continue** (Continuar). Deje el campo **CA Email Address** (Dirección de correo de la entidad de certificación) en blanco, ya que no es obligatorio.

      ![Información del certificado](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Escriba el nombre del archivo de solicitud de firma de certificados (CSR) en **Save As** (Guardar como).
5. Seleccione la ubicación en **Where** (Dónde) y, después, seleccione **Save** (Guardar).

      ![Guardar la solicitud de firma de certificados](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

      Esto guarda el archivo CSR en la ubicación seleccionada (la ubicación predeterminada es el escritorio). Recuerde la ubicación que eligió para este archivo.

## <a name="register-your-app-for-push-notifications"></a>Registro de la aplicación para notificaciones push
Cree un nuevo id. de aplicación explícito para la aplicación con Apple y configúrelo para las notificaciones push.  

1. Navegue hasta el [Portal de aprovisionamiento de iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) en el Centro para desarrolladores de Apple y, después, inicie sesión con su Id. de Apple.

2. Seleccione **Identifiers** (Identificadores) y, después, seleccione **App IDs** (Id. de aplicaciones).

3. Seleccione el signo **+** para registrar una aplicación nueva.

      ![Registrar una aplicación nueva](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

4. Actualice los tres campos siguientes de la nueva aplicación y, después, seleccione **Continue**(Continuar):

   * **Name** (Nombre): en la sección **App ID Description** (Descripción del identificador de la aplicación) y escriba un nombre descriptivo para la aplicación.

   * **Bundle Identifier** (Identificador del lote): en la sección **Explicit App ID** (Identificador de aplicación explícita), escriba un **identificador del lote** con el formato `<Organization Identifier>.<Product Name>`, tal como se describe en la [Guía de distribución de aplicaciones](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Debe coincidir con el que usó en los proyectos XCode, Xamarin o Cordova de la aplicación.

   * **Push Notifications** (Notificaciones push): select la opción **Push Notifications** (Notificaciones push) en la sección **App Services**.

     ![Registrar el identificador de aplicaciones](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

5. En la pantalla **Confirm your App ID** (Confirmar un id. de aplicación), revise la configuración. Una vez que la haya comprobado, seleccione **Register** (Registrar).

6. Después de enviar la nueva identificación de aplicación, verá la pantalla **Registro completado**. Seleccione **Listo**.

7. En el Centro para desarrolladores, en **App IDs** (Id. de aplicaciones), busque el identificador de la aplicación que ha creado y seleccione en su fila. Al hacerlo, se muestran los detalles de dicha aplicación. Después haga clic en el botón **Edit** (Editar) de la parte inferior.

8. Desplácese hasta la parte inferior de la pantalla y haga clic en el botón **Create Certificate...** (Crear certificado) en la sección **Development SSL Certificate** (Certificado SSL de desarrollo).

      ![Certificado SSL push de desarrollo](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

 Se mostrará el asistente "Add iOS Certificate".

   > [!NOTE]
   > Este tutorial usa un certificado de desarrollo. Cuando se registra un certificado de producción se usa el mismo proceso. Asegúrese usar el mismo tipo de certificado al enviar notificaciones.
   >

9. Haga clic en **Choose File**(Elegir archivo) y diríjase a la ubicación en que guardó el CSR del certificado push. A continuación, seleccione **Generate** (Generar).

      ![Generar certificado](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

10. Una vez que el portal haya creado el certificado, haga clic en el botón **Download** (Descargar).

      ![Certificado listo](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

       De esta forma, se descarga el certificado de firma y se guarda en su equipo en la carpeta de descargas.

      ![Carpeta de descarga](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

   > [!NOTE]
   > De manera predeterminada, el archivo descargado es un certificado de implementación denominado **aps_development.cer**.
   >
   >
11. Haga doble clic en el certificado de inserción **aps_development.cer** descargado. De esta forma se instala el certificado nuevo en la cadena de llaves, como se muestra en la siguiente captura de pantalla:

       ![Acceso a llaves](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

   > [!NOTE]
   > El nombre de su certificado puede ser diferente, pero el prefijo será **Apple Development iOS Push Services (Servicios push de Apple Development iOS)**.
   >
   >
12. En **Keychain Access** (Acceso a llaves), seleccione el nuevo certificado push que creó, en la categoría **Certificates** (Certificados). Haga clic en **Export** (Exportar), asigne un nombre al archivo, seleccione el formato **.p12** y, después, haga clic en **Save** (Guardar).

    Recuerde el nombre de archivo y la ubicación del certificado de inserción p12 exportado. Se usa para habilitar la autenticación con APNS mediante su carga en el Portal de Azure clásico. Si la opción de formato **. p12** no está disponible, es posible que necesite reiniciar el acceso a llaves.

## <a name="create-a-provisioning-profile-for-the-app"></a>Creación de un perfil de aprovisionamiento para la aplicación
1. Vuelva al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portal de aprovisionamiento de iOS</a>, seleccione **Provisioning Profiles** (Perfiles de aprovisionamiento), luego **All** (Todo) y, finalmente, haga clic en el botón **+** para crear un perfil. Así se inicia la herramienta de perfiles de **Agregar aprovisionamiento de iOS**.

      ![Perfiles de aprovisionamiento](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. En **Development** (Desarrollo), seleccione **iOS App Development** (Desarrollo de aplicaciones de iOS) como tipo de perfil de aprovisionamiento y, después, haga clic en **Continue** (Continuar).

3. En la lista desplegable **App ID** (Id. de la aplicación), seleccione el identificador de la aplicación que ha creado y, después, haga clic en **Continue** (Continuar).

      ![Seleccionar identificador de la aplicación](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. En la pantalla **Select certificates** (Seleccionar certificados), seleccione el certificado de desarrollo que se usa para la firma del código y, después, haga clic en **Continue** (Continuar). Este es un certificado de firma, no el certificado push que creó.

       ![Signing certificate](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. A continuación, seleccione en **Devices** (Dispositivos) los dispositivos que usará para la prueba y haga clic en **Continue** (Continuar).

     ![Agregar perfil de aprovisionamiento](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Por último, en **Profile Name** (Nombre de perfil) seleccione el nombre del perfil y haga clic en **Generate** (Generar).

      ![Asignación de nombre al perfil](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
