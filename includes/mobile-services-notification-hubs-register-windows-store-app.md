1.  Si aún no ha registrado su aplicación, vaya a la [página Enviar una aplicación][página Enviar una aplicación] en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta de Microsoft y, a continuación, haga clic en **App name**.

    ![][0]

2.  Escriba el nombre de la aplicación en **App name**, haga clic en **Reserve app name** y, a continuación, haga clic en **Save**.

    ![][1]

    Se crea un nuevo registro de la Tienda Windows para su aplicación.

3.  En Visual Studio, abra el proyecto que ha creado al completar el tutorial **Introducción a los Servicios móviles**.

4.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Store** y, a continuación, haga clic en **Associate App with the Store...**.

    ![][2]

    Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

5.  En el asistente, haga clic en **Sign in** y, a continuación, inicie sesión con su cuenta de Microsoft.

6.  Seleccione la aplicación que ha registrado en el paso 2, haga clic en **Next** y, a continuación, en **Associate**.

    ![][3]

    Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.

7.  (Opcional) Repita los pasos 4 a 6 para también registrar el proyecto de la aplicación de la Tienda de Windows Phone de una aplicación universal de Windows.

8.  De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Services**.

    ![][4]

9.  En la página Services, en **Servicios móviles de Azure**, haga clic en el **sitio de Live Services**.

    ![][5]

10. Haga clic en **Authenticating your service** y anote los valores de **secreto de cliente** y **Package security identifier (SID)**.

    ![][6]

    <div class="dev-callout"><b>Nota de seguridad</b>
<p>El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No comparta esta informaci&oacute;n con nadie ni la distribuya con su aplicaci&oacute;n.</p>
</div>

11. Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

    ![][7]

12. Haga clic en la pestaña **Inserción**, escriba los valores de **Secreto del cliente** y **SID del paquete** obtenidos de WNS y luego haga clic en **Guardar**.

    > [WACOM.NOTE] Si completa este tutorial con un servicio móvil anterior, es posible que aparezca un vínculo al final de la pestaña **Insertar** que indique **Habilitar inserción mejorada**. Haga clic en este enlace para actualizar el servicio móvil, de modo que se integre con los Centros de notificaciones. Este cambio no se podrá deshacer. Para obtener detalles sobre cómo habilitar las notificaciones de inserción mejoradas en un servicio móvil de producción, consulte [esta guía][esta guía].

    ![][8]

    > [WACOM.NOTE]Al configurar las credenciales de WNS para las notificaciones de inserción mejoradas en la pestaña **Push** del portal, se comparten con los Centros de notificaciones para configurar el centro de notificaciones para la aplicación.



  [página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png
  [2]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [7]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png
  [esta guía]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [8]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png
