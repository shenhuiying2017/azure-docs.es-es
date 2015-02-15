
1. Si aún no ha registrado su aplicación, vaya a la [página Enviar una aplicación] en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta de Microsoft y, a continuación, haga clic en **Nombre de aplicación**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Escriba el nombre de la aplicación en **Nombre de la aplicación**, haga clic en **Reservar nombre de aplicación** y, a continuación, haga clic en **Guardar**..

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	Se crea un nuevo registro de la Tienda Windows para su aplicación.

3. En Visual Studio, abra el proyecto que ha creado al completar el tutorial [Introducción a Servicios móviles].

4. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Almacén** y, a continuación, haga clic en **Asociar aplicación con la Tienda...**. 

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

5. En el asistente, haga clic en **Iniciar sesión** y, a continuación, inicie sesión con su cuenta de Microsoft.

6. Seleccione la aplicación que ha registrado en el paso 2, haga clic en **Siguiente** y, a continuación, en **Asociar**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png)

   	Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.    

7. De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Services** (Servicios). 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. En la página Servicios, haga clic en el **sitio Servicios Live** en **Servicios móviles de Azure**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

9. En **Configuración de la aplicación**, tome nota de los valores del **Identificador de cliente**, **Secreto del cliente** e **Identificador de seguridad del paquete (SID)**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE] El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No comparta esta información con nadie ni la distribuya con su aplicación.

10. (opcional) Haga clic en **Configuración de API**, habilite **Seguridad de redireccionamiento mejorada**, proporcione un valor de  `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` en **Dirección URL de redireccionamiento** y haga clic en **Guardar**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

	Esto habilita la autenticación de la cuenta Microsoft de la aplicación.

11. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)

12. Haga clic en la pestaña **Insertar**, escriba los valores **Secreto del cliente** e **Identificador de seguridad de paquete (SID)** obtenidos de WNS en el paso 4 y, a continuación, haga clic en **Guardar**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Haga clic en la pestaña **Identidad**. Observe que los valores de **Secreto del cliente** y **SID del paquete** ya se establecieron en el paso anterior. Especifique el **Identificador de cliente** que anotó anteriormente y haga clic en **Guardar**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
De este modo ya estará listo para usar una cuenta Microsoft para autenticarse en su aplicación.  

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Portal de administración de Azure]: https://manage.windowsazure.com/

<!--HONumber=42-->
