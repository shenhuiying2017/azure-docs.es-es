<properties 
	pageTitle="Registro para autenticación de Google - Servicios móviles" 
	description="Obtenga información acerca de cómo registrar las aplicaciones para usar Google para autenticarse con los Servicios móviles de Azure." 
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

# Registro de las aplicaciones para el inicio de sesión de Google con Servicios móviles

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Google para la autenticación con Servicios móviles de Azure.

>[AZURE.NOTE] En este tutorial se describen los [Servicios móviles de Azure](http://azure.microsoft.com/services/mobile-services/), una solución que le ayuda a crear aplicaciones móviles escalables para cualquier plataforma. La solución Servicios móviles facilita la sincronización de datos, la autenticación de usuarios y el envío de notificaciones de inserción. Esta página admite el tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Introducción a la autenticación</a> que muestra cómo registrar usuarios en su aplicación. Si esta es la primera vez que usa Servicios móviles, complete el tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Introducción a los Servicios móviles</a>.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google asociada a una dirección de correo electrónico verificada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Navegue al sitio web de <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">API de Google</a> , inicie sesión con sus credenciales de cuenta de Google, haga clic en **Crear proyecto**, proporcione un **nombre de proyecto** y, a continuación, haga clic en **Crear**.

   	![][1]

2. Haga clic en la **pantalla de consentimiento**, seleccione su **dirección de correo electrónico**, escriba un **nombre de producto** y luego haga clic en **Save** (Guardar). 

3. Haga clic en **API & Auth** (API y autorización), en **Credentials**(Credenciales) y luego en **Create new Client ID** (Crear nuevo identificador de cliente).

   	![][2]

4. Seleccione **Aplicación web**, escriba la dirección URL del servicio móvil en **Orígenes de JavaScript autorizados**, reemplace la dirección URL generada en **URI de redireccionamiento autorizados** por la dirección URL del servicio móvil con la ruta de acceso _/login/google_ anexada y, a continuación, haga clic en **Crear identificador de cliente**.

	>[AZURE.NOTE] Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil con la ruta de acceso _signin-google_ anexada como servicio .NET, por ejemplo <code>https://todolist.azure-mobile.net/signin-google</code>. 

   	![][3]

5. En **Client ID for web applications** (Identificador de cliente para aplicaciones web), anote los valores de **Client ID** (Identificador de cliente) y **Client secret** (Secreto de cliente). 

   	![][4]

    > [AZURE.IMPORTANT] El secreto de cliente es una credencial de seguridad importante. por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

De este modo ya estará listo para usar un inicio de sesión de Google para autenticarse en su aplicación proporcionando los valores de identificador de cliente y secreto de cliente a Servicios móviles.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[API de Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Introducción a la autenticación]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Portal de administración de Azure]: https://manage.windowsazure.com/

<!--HONumber=47-->
