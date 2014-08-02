

1.  Si aún no ha registrado su aplicación, vaya a la [página Enviar una aplicación](http://go.microsoft.com/fwlink/p/?LinkID=266582) en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta de Microsoft y, a continuación, haga clic en **App name**.

      ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-submit-win8-app.png)

2.  Escriba el nombre de la aplicación en **App name**, haga clic en **Reserve app name** y, a continuación, haga clic en **Save**.

      ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-name.png)

      Se crea un nuevo registro de la Tienda Windows para su aplicación.

3.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles](/en-us/documentation/articles/mobile-services-windows-store-get-started/).

4.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Store** y, a continuación, haga clic en **Associate App with the Store...**.

  ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-store-association.png) 

	Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

1.  En el asistente, haga clic en **Sign in** y, a continuación, inicie sesión con su cuenta de Microsoft.

2.  Seleccione la aplicación que ha registrado en el paso 2, haga clic en **Next** y, a continuación, en **Associate**.

      ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-select-app-name.png)

      Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.    

3.  De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Services**.

      ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit-app.png) 

4.  En la página Services, en **Servicios móviles de Azure**, haga clic en el **sitio de Live Services**.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

5.  Haga clic en **Authenticating your service** y anote los valores de **secreto de cliente** y **Package security identifier (SID)**.

      ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    **Nota de seguridad**

    El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No comparta esta información con nadie ni la distribuya con su aplicación.

6.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

  ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-selection.png)

1.  Haga clic en la pestaña **Push**, a continuación en **Habilitar inserción mejorada** y, por último, en **Yes** para aceptar el cambio de configuración.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

    De este modo, se actualiza la configuración del servicio móvil para utilizar la funcionalidad mejorada de notificaciones de inserción proporcionada por los Centros de notificaciones. El uso de algunos Centros de notificaciones es gratuito con servicios móviles de pago. Para obtener más información, consulte [Detalles de precios de Servicios móviles](http://go.microsoft.com/fwlink/p/?LinkID=311786).

    **Importante**

    Mediante esta operación se restablecen las credenciales de inserción y se cambia el funcionamiento de los métodos de inserción de los scripts. Estos cambios no pueden deshacerse. No utilice este método para agregar un centro de notificaciones a un servicio móvil de producción. Para obtener instrucciones acerca de cómo habilitar las notificaciones de inserción mejoradas en un servicio móvil de producción, consulte [esta guía](http://go.microsoft.com/fwlink/p/?LinkId=391951).

2.  Escriba los valores **secreto de cliente** y **SID del paquete** obtenidos de WNS en el paso 4 y, a continuación, haga clic en **Save**.

   ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-push-tab.png)

    >[WACOM.NOTE]Al establecer las credenciales de WNS para las notificaciones de inserción mejoradas en la pestaña **Push** del portal, estas se comparten con los Centros de notificaciones para configurar el centro de notificaciones de la aplicación.
