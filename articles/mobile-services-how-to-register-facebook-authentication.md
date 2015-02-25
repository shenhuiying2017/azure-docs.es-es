<properties pageTitle="Registro para autenticación de Facebook - Servicios móviles" description="Aprenda a usar la autenticación de Facebook en la aplicación de Servicios móviles de Azure." services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

# Registro de las aplicaciones para la autenticación de Facebook con Servicios móviles

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Facebook para la autenticación con Servicios móviles de Azure. 

> [AZURE.NOTE] En este tutorial se describen los [Servicios móviles de Azure], una solución que le ayuda a crear aplicaciones móviles escalables para cualquier plataforma. La solución Servicios móviles facilita la sincronización de datos, la autenticación de usuarios y el envío de notificaciones de inserción. En esta página se incluye el tutorial <a href="http://azure.microsoft.com/es-es/documentation/articles/mobile-services-ios-get-started-users/">Introducción a la autenticación</a> en el que se muestra cómo registrar usuarios en la aplicación. Si esta es la primera vez que usa Servicios móviles, complete el tutorial <a href="http://azure.microsoft.com/es-es/documentation/articles/mobile-services-ios-get-started/">Introducción a los Servicios móviles</a>.
	
Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Facebook asociada a una dirección de correo electrónico verificada y a un número de teléfono móvil. Para crear una nueva cuenta de Facebook, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>.

1. Desplácese hasta el sitio web para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">desarrolladores de Facebook</a> e inicie sesión con las credenciales de su cuenta de Facebook.

2. (Opcional) Si aún no está registrado, haga clic en **Apps** (Aplicaciones), luego en **Register as a Developer** (Registrarse como desarrollador), acepte la política y siga los pasos de registro. 

   	![][0]

3. Haga clic en **Apps** (Aplicaciones) y luego en **Create a New App** (Crear una nueva aplicación).

   	![][1]

4. Elija un nombre exclusivo para la aplicación, seleccione **Apps for Pages** (Aplicaciones para páginas), haga clic en **Create App** (Crear aplicación) y conteste a la pregunta de seguridad.

   	![][2]

	De esta manera la aplicación se registra en Facebook. 

5. Haga clic en **Settings** (Configuración) y escriba el dominio de su servicio móvil en **App Domains** (Dominios de aplicaciones). También indique un **correo electrónico de contacto**, haga clic en **Add Platform** (Agregar plataforma) y seleccione **Website** (Sitio web).

   	![][3]

6. Escriba la URL de su servicio móvil en **Site URL** (URL del sitio) y haga clic en **Save Changes** (Guardar cambios).

	![][4]

7. Haga clic en **Show** (Mostrar), proporcione la contraseña si se le solicita y anote los valores de **App ID** (Identificador de aplicación) y **App Secret** (Secreto de aplicación). 

   	![][5]

	> [AZUTE.NOTE] **Nota de seguridad**
	El secreto de aplicación es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.


8. Haga clic en la pestaña **Advanced** (Opciones avanzadas), escriba la URL de su servicio móvil anexada a la ruta de acceso _/login/facebook_ en **Valid OAuth redirect URIs** (URI de redirección OAuth válidos) y luego haga clic en **Save Changes** (Guardar cambios). 

	> [AZURE.NOTE] Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil con la ruta de acceso _signin-facebook_ anexada como servicio .NET, por ejemplo <code>https://todolist.azure-mobile.net/signin-facebook</code>.  
	
	![][7]

9. La cuenta de Facebook para la que definió la nueva aplicación corresponde a un administrador de la aplicación, que tiene acceso a esta como administrador. Para autenticar otras cuentas de Facebook, estas necesitan acceso a la aplicación. Este paso concede acceso al público en general, de modo que la aplicación pueda autenticar otras cuentas de Facebook. Haga clic en **Status & Review** (Estado y revisión). A continuación, haga clic en **Yes** (Sí) para habilitar el acceso del público en general.

    ![][6]



Ahora ya puede habilitar el uso del inicio de sesión de Facebook para autenticarse en la aplicación facilitando el identificador y el secreto de la aplicación a Servicios móviles.  

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
[6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
[7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png

<!-- URLs. -->
[Desarrolladores de Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Servicios móviles de Azure]: http://azure.microsoft.com/es-es/services/mobile-services/


<!--HONumber=42-->
