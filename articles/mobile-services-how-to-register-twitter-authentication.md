<properties 
	pageTitle="Registro para autenticación de Twitter - Servicios móviles" 
	description="Obtenga información acerca de cómo usar la autenticación de Twitter con su aplicación de Servicios móviles de Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

#Registro de las aplicaciones para el inicio de sesión en Twitter con Servicios móviles

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Twitter para la autenticación con Servicios móviles de Azure.

>[AZURE.NOTE] En este tutorial se describen los [Servicios móviles de Azure](http://azure.microsoft.com/services/mobile-services/), una solución que le ayuda a crear aplicaciones móviles escalables para cualquier plataforma. La solución Servicios móviles facilita la sincronización de datos, la autenticación de usuarios y el envío de notificaciones de inserción. Esta página admite el tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Introducción a la autenticación</a> que muestra cómo registrar usuarios en su aplicación. Si esta es la primera vez que usa Servicios móviles, complete el tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Introducción a los Servicios móviles</a>.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Twitter con una dirección de correo electrónico verificada. Para crear una cuenta de Twitter, vaya <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Navegue al sitio web de <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Desarrolladores de Twitter</a> inicie sesión con sus credenciales de cuenta de Twitter y haga clic en **Crear una nueva aplicación**.

   	![][1]

2. Escriba los valores de **Name**, **Description** y **Website** de su aplicación y especifique la dirección URL del servicio móvil anexada a la ruta de acceso _/login/twitter_ en **Callback URL**.

	>[AZURE.NOTE]Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil con la ruta de acceso _signin-twitter_ anexada como servicio .NET, por ejemplo <code>https://todolist.azure-mobile.net/signin-twitter</code>.

   	![][2]

3.  En la parte inferior de la página, lea y acepte los términos, escriba las palabras correctas del código CAPTCHA y, a continuación, haga clic en **Create your Twitter application** (Crear su aplicación de Twitter). 

   	![][3]

   	De esta forma, la aplicación se registra y se muestran los detalles correspondientes.

6. Tome nota de los valores indicados en **Consumer key** (Clave de consumidor) y **Consumer secret** (Secreto de consumidor). 

   	![][4]

    > [AZURE.NOTE] El secreto de consumidor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

7. Haga clic en la pestaña **Settings** (Configuración), desplácese hacia abajo y marque **Allow this application to be used to sign in with Twitter** (Permitir que esta aplicación se use para iniciar sesión en Twitter) y luego haga clic en **Update this Twitter application's settings** (Actualizar la configuración de esta aplicación de Twitter).

	![][5]

De este modo ya estará listo para usar un inicio de sesión de Twitter para autenticarse en su aplicación proporcionando los valores de clave de usuario y secreto de usuario a Servicios móviles.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Desarrolladores de Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Introducción a la autenticación]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Portal de administración de Azure]: https://manage.windowsazure.com/

<!--HONumber=47-->
