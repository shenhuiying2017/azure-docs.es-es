1.  Si aún no ha registrado su aplicación, vaya a la [página Enviar una aplicación][] en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta de Microsoft y, a continuación, haga clic en **App name**.

    ![][]

2.  Escriba el nombre de la aplicación en **App name**, haga clic en **Reserve app name** y, a continuación, haga clic en **Save**.

    ![][1]

    Se crea un nuevo registro de la Tienda Windows para su aplicación.

3.  En Visual Studio, abra el proyecto que ha creado al completar el tutorial [Introducción a los Servicios móviles][].

4.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Store** y, a continuación, haga clic en **Associate App with the Store...**.

    ![][2]

    Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

5.  En el asistente, haga clic en **Sign in** y, a continuación, inicie sesión con su cuenta de Microsoft.

6.  Seleccione la aplicación que ha registrado en el paso 2, haga clic en **Next** y, a continuación, en **Associate**.

    ![][3]

    Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.

7.  De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Services**.

    ![][4]

8.  En la página Services, en **Servicios móviles de Azure**, haga clic en el **sitio de Live Services**.

    ![][5]

9.  En **Configuración de aplicación**, tome nota de los valores de **Identificador de cliente**, **Secreto del cliente** e **Identificador de seguridad (SID)**.

    ![][6]

    > [WACOM.NOTE]El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No comparta esta información con nadie ni la distribuya con su aplicación.

10. (opcional) Haga clic en **Configuración de API**, habilite **Seguridad de redireccionamiento mejorada**, proporcione un valor de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` en **Dirección URL de redireccionamiento** y haga clic en **Guardar**.

    ![][7]

    Esto habilita la autenticación de la cuenta Microsoft de la aplicación.

11. Inicie sesión en el [Portal de administración de Azure][], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

    ![][8]

12. Haga clic en la pestaña **Inserción**, escriba los valores de **Secreto del cliente** y **SID del paquete** obtenidos de WNS en el paso 4 y luego haga clic en **Guardar**.

    ![][9]

13. Haga clic en la pestaña **Identidad**. Observe que los valores de **Secreto del cliente** y **SID del paquete** ya se establecieron en el paso anterior. Especifique el **Identificador de cliente** que anotó anteriormente y haga clic en **Guardar**.

    ![][10]

De este modo ya estará listo para usar una cuenta Microsoft para autenticarse en su aplicación.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  []: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
  [2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [7]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [8]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
  [9]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png
  [10]: ./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png
