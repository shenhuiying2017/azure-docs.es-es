
1. Si aún no ha registrado su aplicación, vaya a la [página Enviar una aplicación][1] en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta de Microsoft y, a continuación, haga clic en **App name**.
    
	![][0]

2. Escriba el nombre de la aplicación en **App name**, haga clic en **Reserve app name** y, a continuación, haga clic en **Save**.  

	![][1]
       
	Se crea un nuevo registro de la Tienda Windows para su aplicación.

3.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que ha creado cuando realizó el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started/#create-new-service).

4.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Store** y, a continuación, haga clic en **Associate App with the Store...**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png) 
	
	Se muestra el asistente para **asociar la aplicación con la Tienda Windows**. 
1.  En el asistente, haga clic en **Sign in** y, a continuación, inicie sesión con su cuenta de Microsoft.

2.  Seleccione la aplicación que ha registrado en el paso 2, haga clic en **Next** y, a continuación, en **Associate**.
    
	![][3]
        
	Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.    

3.  De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Services**.
    
	![][4] 

4.  En la página Services, en **Servicios móviles de Azure**, haga clic en el **sitio de Live Services**.
    
    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)

5.  Haga clic en **Authenticating your service** y anote los valores de **Secreto de cliente** y **Package security identifier (SID)**. 
	
	![][6]

	> [WACOM.NOTE]El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No comparta esta información con nadie ni la distribuya con su aplicación.

6.  Inicie sesión en el [Portal de administración de Azure][2], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png) 
1.  Haga clic en la pestaña **Push**, escriba los valores de **Secreto
    de cliente** y **Package SID** obtenidos de WNS en el paso 4 y, a continuación, haga clic en **Save**.

 	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png) 
<!-- Anchors. -->



<!-- Images. -->
[0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
[3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
[4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
[5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
[6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
[7]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
[8]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure Management Portal]: https://manage.windowsazure.com/