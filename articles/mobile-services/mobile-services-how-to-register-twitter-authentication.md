<properties 
	pageTitle="Registro para la autenticación de Twitter | Microsoft Azure" 
	description="Obtenga información acerca de cómo usar la autenticación de Twitter con su aplicación de Servicios móviles de Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="glenga"/>

#Registro de aplicaciones para el inicio de sesión en Twitter con Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Twitter para la autenticación con Servicios móviles de Azure.

>[AZURE.NOTE]En este tutorial se describen los [Servicios móviles de Azure](http://azure.microsoft.com/services/mobile-services/), una solución que le ayuda a crear aplicaciones móviles escalables para cualquier plataforma. La solución Servicios móviles facilita la sincronización de datos, la autenticación de usuarios y el envío de notificaciones de inserción. En esta página se incluye el tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Introducción a la autenticación</a> en el que se muestra cómo registrar usuarios en la aplicación. Si esta es la primera vez que usa Servicios móviles, complete el tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Introducción a los Servicios móviles</a>.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Twitter con una dirección de correo electrónico verificada. Para crear una cuenta de Twitter, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Desplácese hasta el sitio web para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">desarrolladores de Twitter</a>, inicie sesión con las credenciales de su cuenta de Twitter y haga clic en **Create a new application** (Crear una nueva aplicación).

   	![][1]

2. Escriba los valores de **Name**, **Description** y **Website** de su aplicación y especifique la dirección URL del servicio móvil anexada a la ruta de acceso _/login/twitter_ en **Callback URL**.

	>[AZURE.NOTE]Para un servicio móvil de back-end .NET publicado en Azure con Visual Studio, la URL de redireccionamiento es la URL del servicio móvil que lleva anexada la ruta de acceso _signin-twitter_. En este ejemplo, nuestro servicio móvil tendría la URL de devolución de llamada ```https://todolist.azure-mobile.net/signin-twitter```.

   	![][2]

3.  En la parte inferior de la página, lea y acepte los términos y, luego, haga clic en **Create your Twitter application** (Crear su aplicación de Twitter).

   	De esta forma, la aplicación se registra y se muestran los detalles correspondientes.

6. Haga clic en la pestaña **Keys and Access Tokens** (Claves y tokens de acceso) en el panel de la aplicación y anote los valores de **Consumer key** (Clave de consumidor) y **Consumer secret** (Secreto de consumidor).

    > [AZURE.NOTE]El secreto de consumidor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

7. Haga clic en la pestaña **Settings** (Configuración), desplácese hacia abajo y asegúrese de que la casilla **Allow this application to be used to sign in with Twitter** (Permitir que esta aplicación se use para iniciar sesión en Twitter) esté activada y, luego, haga clic en **Update Settings** (Actualizar configuración).

De este modo ya estará listo para usar un inicio de sesión de Twitter para autenticarse en su aplicación proporcionando los valores de clave de usuario y secreto de usuario a Servicios móviles.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO8-->