
El servicio de notificaciones de inserción de Apple (APNS) usa certificados para autenticar su servicio móvil. Siga estas instrucciones para crear los certificados necesarios y cargarlos en su servicio móvil. Para consultar la documentación oficial de la característica APNS, consulte [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

## Generación del archivo de solicitud de firma de certificado

Primero debe generar el archivo de solicitud de firma de certificado (CSR) que Apple usa para generar un certificado firmado.

1. En la carpeta Utilities, ejecute la herramienta Acceso a llaves.

2. Haga clic en **Acceso a llaves**, expanda **Asistente de certificados** y, a continuación, haga clic en **Solicitar un certificado de una entidad de certificación...**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png)

3. Seleccione una **Dirección de correo electrónico del usuario** y un **Nombre común**, asegúrese de que la opción **Guardado en disco** está seleccionada y, a continuación, haga clic en **Continuar**. Deje el campo **Dirección de correo electrónico de la entidad de certificación** en blanco, ya que no es obligatorio.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png)

4. Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Guardar como**, seleccione la ubicación en **Donde** y, a continuación, haga clic en **Guardar**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png)

  	De esta forma, se guarda el archivo CSR en la ubicación seleccionada; el Escritorio es la ubicación predeterminada. Recuerde la ubicación seleccionada para este archivo.

A continuación, se registrará la aplicación en Apple, se habilitarán las notificaciones de inserción y se cargará este archivo CSR exportado para crear un certificado de inserción.

## Registrar la aplicación para las notificaciones de inserción

Para poder enviar notificaciones de inserción a una aplicación iOS desde servicios móviles, debe registrar su aplicación con Apple y también registrar las notificaciones de inserción.  

1. Si aún no ha registrado su aplicación, diríjase al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portal de aprovisionamiento de iOS</a> del Centro para desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identificadores** y, a continuación, en **Identificadores de aplicaciones** y, por último, haga clic en el signo **+** para registrar una aplicación nueva.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-02.png)



> [WACOM.NOTE] Si elige proporcionar un valor <strong>Identificador de paquete</strong> distinto a <i>MobileServices.Quickstart</i>, también debe actualizar el identificador de agrupación de trabajos en el proyecto de Xcode. Recomendamos que utilice el valor de identificador de paquete exacto que ya se ha utilizado en el proyecto de inicio rápido.

2. Escriba un nombre para su aplicación en **Descripción**, escriba el valor _MobileServices.Quickstart_ in **Identificador de paquete**, marque la opción "Notificaciones de inserción" en la sección "Servicios de aplicaciones" y, a continuación, haga clic en **Continuar**. En este ejemplo se usa el identificador **MobileServices.Quickstart** pero es posible que no pueda volver a usar ese mismo identificador, ya que los identificadores de aplicaciones deben ser exclusivos entre todos los usuarios. Por ese motivo, es recomendable que agregue su nombre completo o sus iniciales después del nombre de la aplicación.


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-03.png)

   	De esta forma, se genera el identificador de la aplicación y se solicita que **envíe** la información. Haga clic en **Enviar**.


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-04.png)


   	Después de hacer clic en **Enviar**, verá la pantalla **Registro completado**, como se muestra a continuación. Haga clic en **Listo**.


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-05.png)


3. Busque el identificador de la aplicación que acaba de crear y haga clic en su fila.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-06.png)

   	Si hace clic en el identificador de la aplicación, se mostrará información sobre la aplicación y el identificador de la aplicación. Haga clic en el botón **Configuración**.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-07.png)

4. Desplácese a la parte inferior de la pantalla y haga clic en el botón **Crear certificado...** en la sección **Development Push SSL Certificate**.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-08.png)

   	Se mostrará el asistente "Add iOS Certificate".

    > [WACOM.NOTE] Este tutorial usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producción. Asegúrese de que establece el mismo tipo de certificado cuando carga el certificado en Servicios móviles.

5. Haga clic en **Elegir archivo**, diríjase a la ubicación en la que guardó el archivo CSR que creó en la primera tarea y, a continuación, haga clic en **Generar**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-10.png)

6. Una vez que el portal haya creado el certificado, haga clic en el botón **Descargar** y, a continuación, haga clic en **Listo**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-11.png)

   	De esta forma, se descarga el certificado de firma y se guarda en su equipo en la carpeta de descargas.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] De forma predeterminada, el certificado de desarrollo del archivo descargado tiene el nombre **aps_development.cer**.

7. Haga doble clic en el certificado de inserción descargado **aps_development.cer**.

   	De esta forma, se instala un nuevo certificado en las llaves, como se muestra a continuación:

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png)

    > [WACOM.NOTE] El nombre del certificado puede ser distinto, pero tendrá el prefijo de los **servicios de notificaciones de inserción de iOS de desarrollo de Apple:**.

A continuación, usará este certificado para generar un archivo .p12 y cargarlo en Servicios móviles para permitir la autenticación con APNS.

## Creación de un perfil de aprovisionamiento para la aplicación

1. Vuelva al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portal de aprovisionamiento de iOS</a>, seleccione **Perfiles de aprovisionamiento**, seleccione **Todos** y, a continuación, haga clic en el botón **+** para crear un perfil nuevo. De esta forma, se iniciará el asistente de **Agregar perfil de aprovisionamiento de iOS**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-12.png)

2. Seleccione **Desarrollo de aplicaciones de iOS** en **Desarrollo** como el tipo de perfil de aprovisionamiento y, a continuación, haga clic en **Continuar**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-13.png)

3. A continuación, seleccione el identificador de la aplicación para la aplicación Mobile Services Quickstart en la lista desplegable **Identificador de aplicación** y, a continuación, haga clic en **Continuar**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-14.png)

4. En la pantalla **Seleccionar certificados**, seleccione el certificado creado anteriormente y haga clic en **Continuar**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-15.png)

5. A continuación, seleccione los **Dispositivos** que usará para la prueba y haga clic en **Continuar**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-16.png)

6. Para terminar, seleccione un nombre para el perfil en **Nombre de perfil**, haga clic en **Generar** y en **Listo**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-17.png)

  	De esta forma, se creará un nuevo perfil de aprovisionamiento.

7. En Xcode, abra el organizador, seleccione la vista de dispositivos, seleccione **Perfiles de aprovisionamiento** en la sección **Biblioteca** en el panel izquierdo y, a continuación, haga clic en el botón **Actualizar** en la parte inferior del panel intermedio.

8. O bien, en el menú Xcode, seleccione **Preferencias** y, a continuación, **Cuentas**. En el panel izquierdo, seleccione el identificador de desarrollador de Apple. Haga clic en el botón **Ver detalles** de la derecha. En la ventana emergente, haga clic en el botón redondeado **Actualizar**. Esto actualiza la lista de perfiles de aprovisionamiento. Este proceso puede tardar unos minutos. Recomendamos hacer clic en **Actualizar** de dos a tres veces hasta que aparezca el nuevo perfil de aprovisionamiento. Asimismo, confirme que el identificador de paquete de este proyecto de Xcode es idéntico al identificador de paquete asociado con el identificador de la aplicación y el perfil de aprovisionamiento que ha creado hasta ahora.

    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-01.png)

9. En **Destinos**, haga clic en **Inicio rápido**, expanda **Identidad de firma de código** y, en **Depurar**, seleccione el nuevo perfil. De esta forma, se garantiza que el proyecto de Xcode usa el nuevo perfil para la firma de código. A continuación, debe cargar el certificado en Azure.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png)
