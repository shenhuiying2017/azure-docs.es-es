
El servicio de notificaciones de inserción de Apple (APNS) usa certificados para autenticar su servicio móvil. Siga estas instrucciones para crear los certificados necesarios y cargarlos en su servicio móvil. Para consultar la documentación oficial de la característica APNS, consulte [Servicio de notificaciones de inserción de Apple](http://go.microsoft.com/fwlink/p/?LinkId=272584).

## <a id="certificates"></a>Generación del archivo de solicitud de firma de certificado

Primero debe generar el archivo de solicitud de firma de certificado (CSR) que Apple usa para generar un certificado firmado.

1. En la carpeta Utilities, ejecute la herramienta Acceso a llaves.

2. Haga clic en **Keychain Access** (Acceso a llaves), expanda **Certificate Assistant** (Asistente para certificados) y, a continuación, haga clic en **, Request a Certificate from a Certificate Authority....** (Solicitar un certificado de una entidad de certificación...).

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png)

3. Seleccione una dirección de correo electrónico de usuario en **User Email Address** y un nombre común en **Common Name**, asegúrese de que **Saved to disk** (Guardado en disco) se encuentra seleccionado y, a continuación, haga clic en **Continue** (Continuar). Deje el campo **CA Email Address** (Dirección de correo de la entidad de certificación) en blanco, ya que no es obligatorio.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png)

4. Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Save As** (Guardar como), seleccione la ubicación en **Where** (Dónde) y, a continuación, haga clic en **Guardar**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png)

  	De esta forma, se guarda el archivo CSR en la ubicación seleccionada; el Escritorio es la ubicación predeterminada. Recuerde la ubicación seleccionada para este archivo.

A continuación, se registrará la aplicación en Apple, se habilitarán las notificaciones de inserción y se cargará este archivo CSR exportado para crear un certificado de inserción.

## <a id="register"></a>Registrar aplicaciones para las notificaciones de inserción

Para poder enviar notificaciones de inserción a una aplicación iOS desde servicios móviles, debe registrar su aplicación con Apple y también registrar las notificaciones de inserción.  

1. Si aún no ha registrado su aplicación, vaya al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de aprovisionamiento de iOS</a> en el Centro del desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identifiers** (Identificadores), a continuación, haga clic en **App IDs** (Id. de Apple) y, por último, haga clic en el signo **+** para registrar una nueva aplicación.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-02.png)



> [AZURE.NOTE] Si elige proporcionar un valor <strong>Bundle Identifier</strong> (Identificador de agrupación) distinto de <i>MobileServices.Quickstart</i>, también debe actualizar el identificador de agrupación de trabajos en el proyecto de Xcode. Recomendamos que utilice el valor de identificador de paquete exacto que ya se ha utilizado en el proyecto de inicio rápido.

2. Escriba un nombre para su aplicación en **Description** (Descripción), especifique el valor MobileServices.Quickstart en **Bundle Identifier** (Identificador de agrupación), haga clic en la opción de notificaciones de inserción "Push Notifications" de la sección de servicios de aplicaciones "App Services" y, a continuación, haga clic en **Continue** (Continuar). En este ejemplo se usa el identificador **MobileServices.Quickstart** pero es posible que no pueda volver a usar ese mismo identificador, ya que los identificadores de aplicaciones deben ser exclusivos entre todos los usuarios. Por ese motivo, es recomendable que agregue su nombre completo o sus iniciales después del nombre de la aplicación.


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-03.png)

   	De esta forma, se genera el identificador de la aplicación y se solicita que **envíe** la información. Haga clic en **Submit** (Enviar).


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-04.png)


   	Una vez que haga clic en **Submit** (Enviar), verá la pantalla **Registration complete** (Registro completado), como se muestra a continuación. Haga clic en **Done** (Listo).


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-05.png)


3. Busque el identificador de la aplicación que acaba de crear y haga clic en su fila.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-06.png)

   	Al hacer clic en el id. de la aplicación aparecerán los detalles de la misma y su id. Haga clic en el botón **Settings** (Configuración).

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-07.png)

4. Desplácese a la parte inferior de la pantalla y haga clic en el botón **Create Certificate...** (Crear certificado...) en la sección **Development Push SSL Certificate** (Certificado SSL de inserción de desarrollo).

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-08.png)

   	Se mostrará el asistente "Add iOS Certificate".

    > [AZURE.NOTE] Este tutorial usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producción. Asegúrese de que establece el mismo tipo de certificado cuando carga el certificado en Servicios móviles.

5. Haga clic en **Choose File** (Elegir archivo), diríjase a la ubicación en la que guardó el archivo CSR que creó en la primera tarea y, a continuación, haga clic en **Generate** (Generar).

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-10.png)

6. Una vez que el portal haya creado el certificado, haga clic en el botón **Download** (Descargar) y en **Done** (Listo).

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-11.png)

   	De esta forma, se descarga el certificado de firma y se guarda en su equipo en la carpeta de descargas.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png)

    > [AZURE.NOTE] De forma predeterminada, el certificado de desarrollo del archivo descargado tiene el nombre **aps_development.cer**.

7. Haga doble clic en el certificado de inserción descargado **aps_development.cer**.

   	De esta forma, se instala un nuevo certificado en las llaves, como se muestra a continuación:

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png)

    > [AZURE.NOTE] El nombre del certificado puede ser diferente, pero tendrá el prefijo de **Servicios inserción de desarrollo de Apple iOS:**.

A continuación, usará este certificado para generar un archivo .p12 y cargarlo en Servicios móviles para permitir la autenticación con APNS.

## <a id="profile"></a>Creación de un perfil de aprovisionamiento para la aplicación

1. Nuevamente en el <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de aprovisionamiento de iOS</a>, selecciones **Provisioning Profiles** (Perfiles de aprovisionamiento), seleccione **All** (Todos) y haga clic en el botón **+** para crear un nuevo perfil. De esta forma, se iniciará el asistente para **Add iOS Provisiong Profile** (Agregar perfil de aprovisionamiento de iOS).

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-12.png)

2. Seleccione **iOS App Development** (Desarrollo de aplicaciones de iOS) en **Development** (Desarrollo) como tipo de perfil de aprovisionamiento y haga clic en **Continue** (Continuar).

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-13.png)

3. A continuación, seleccione el identificador de la aplicación para la aplicación Mobile Services Quickstart en la lista desplegable **App ID** (Id. de aplicaciones) y haga clic en **Continue** (Continuar).

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-14.png)

4. En la pantalla **Select certificates** (Seleccionar certificados), seleccione el certificado creado anteriormente y haga clic en **Continue** (Continuar).

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-15.png)

5. A continuación, seleccione los **Devices** (Dispositivos) que usará para la prueba y haga clic en **Continue** (Continuar).

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-16.png)

6. Para terminar, seleccione un nombre para el perfil en **Profile Name** (Nombre de perfil), haga clic en **Generate** (Generar) y haga clic en **Done** (Listo)

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-17.png)

  	De esta forma, se creará un nuevo perfil de aprovisionamiento.

7. En Xcode, abra el organizador, seleccione la vista de dispositivos, seleccione **Provisioning Profiles** (Perfiles de aprovisionamiento) en la sección **Library** (Biblioteca) del panel izquierdo y, a continuación, haga clic en el botón **Refresh** (Actualizar) en la parte inferior del panel intermedio.

8. O bien, en el menú Xcode, seleccione **Preferences** (Preferencias) y **Accounts** (Cuentas). En el panel izquierdo, seleccione el identificador de desarrollador de Apple. Haga clic en el botón **View Details** (Ver detalles) de la derecha. En la ventana emergente, haga clic en el botón **Refresh** (Actualizar). Esto actualiza la lista de perfiles de aprovisionamiento. Este proceso puede tardar unos minutos. Recomendamos hacer clic en **Refresh** (Actualizar) de dos a tres veces hasta que aparezca el nuevo perfil de aprovisionamiento. Asimismo, confirme que el identificador de paquete de este proyecto de Xcode es idéntico al identificador de la agrupación asociado con el identificador de la aplicación y el perfil de aprovisionamiento que ha creado hasta ahora.

    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-01.png)

9. En **Targets** (Destinos), haga clic en **Quickstart** (Inicio rápido), expanda **Code Signing Identity** (Identidad de firma de código) y, a continuación, en **Debug** (Depurar) seleccione el nuevo perfil. De esta forma, se garantiza que el proyecto de Xcode usa el nuevo perfil para la firma de código. A continuación, debe cargar el certificado en Azure.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png)

<!--HONumber=42-->
