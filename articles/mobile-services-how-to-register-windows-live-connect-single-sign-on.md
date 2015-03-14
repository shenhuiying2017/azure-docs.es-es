<properties 
	pageTitle="Registro para inicio de sesión único - Servicios móviles de Azure" 
	description="Obtenga información acerca de cómo registrarse para la autenticación de inicio de sesión único en la aplicación de Servicios móviles de Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Registro de las aplicaciones de la Tienda Windows para usar el inicio de sesión único de Live Connect de Windows

En este tema se explica cómo registrar la aplicación con la Tienda Windows para poder usar Live Connect para el inicio de sesión único como el proveedor de identidades de Servicios móviles de Azure. Este paso también es necesario para usar notificaciones de inserción.

> [AZURE.IMPORTANT] No es necesario que registre la aplicación con la Tienda Windows para poder usar la cuenta Microsoft para la autenticación antes de publicar la aplicación. Cuando la aplicación de la Tienda Windows no requiere inicio de sesión único o notificaciones de inserción, puede simplemente registrar la aplicación con Live Connect para usar un inicio de sesión de la cuenta Microsoft.  Para obtener más información, consulte [Registro de sus aplicaciones de la Tienda Windows para usar un inicio de sesión de la cuenta Microsoft](/es-es/develop/mobile/how-to-guides/register-for-microsoft-authentication).

1. Si aún no ha registrado la aplicación, vaya a la página [Enviar una aplicación] en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta Microsoft y, a continuación, haga clic en **Nombre de la aplicación**.

   	![][0]

2. Escriba el nombre de la aplicación en **Nombre de la aplicación**, haga clic en **Reservar nombre de aplicación** y luego en **Guardar**.

   	![][1]

   	Se crea un nuevo registro de la Tienda Windows para su aplicación.

3. En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles].

4. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto, haga clic en **Almacén** y, a continuación, haga clic en **Asociar aplicación con la Tienda...**. 

  	![][2]

   	Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

5. En el asistente, haga clic en **Iniciar sesión** y, a continuación, inicie sesión con su cuenta Microsoft.

6. Seleccione la aplicación que ha registrado en el paso 2, haga clic en **Siguiente** y luego en **Asociar**.

   	![][3]

   	Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.    

9. Desplácese hasta la página [Mis aplicaciones] del Centro para desarrolladores de Live Connect y haga clic en la aplicación en la lista **Mis aplicaciones**.

   	![][6] 

10. Haga clic en **Editar configuración**, luego en **Configuración de API** y anote el valor del **Secreto de cliente**. 

   	![][7]

    > [AZURE.NOTE] El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

11. En **Dominio de redirección**, escriba la dirección URL del servicio móvil del paso 8 y luego haga clic en **Guardar**.

Ahora está preparado para integrar la autenticación a su aplicación mediante el uso de Live Connect. Servicios móviles proporciona los dos siguientes métodos para autenticar usuarios mediante Live Connect:

   - Inicio de sesión único para aplicaciones de la Tienda Windows. En este método, los usuarios solo deben autorizar la autenticación en la aplicación una vez con Live Connect; a continuación, Windows administra las credenciales, según las preferencias del usuario. Para obtener más información, consulte [Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect].

   - Autenticación básica. Este método, que es compatible con diversos proveedores de autenticación, requiere que los usuarios inicien sesión cada vez que se inicia la aplicación. Para obtener más información, vea [Introducción a la autenticación].

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png


[6]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png



<!-- URLs. -->
[Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect]: /es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript y HTML]: /es-es/develop/mobile/tutorials/get-started-with-users-js/
[Portal de administración de Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
