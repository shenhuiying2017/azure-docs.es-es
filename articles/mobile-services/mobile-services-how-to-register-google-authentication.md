<properties 
	pageTitle="Registro para la autenticación de Google | Microsoft Azure"
	description="Obtenga información acerca de cómo registrar las aplicaciones para usar Google para autenticarse con los Servicios móviles de Azure."
	services="mobile-services"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="glenga"/>

# Registro de las aplicaciones para el inicio de sesión de Google con Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Google para la autenticación con Servicios móviles de Azure.

>[AZURE.NOTE]En este tutorial se describen los [Servicios móviles de Azure](http://azure.microsoft.com/services/mobile-services/), una solución que le ayuda a crear aplicaciones móviles escalables para cualquier plataforma. La solución Servicios móviles facilita la sincronización de datos, la autenticación de usuarios y el envío de notificaciones de inserción. En esta página se incluye el tutorial [Introducción a la autenticación](mobile-services-ios-get-started-users.md) en el que se muestra cómo iniciar sesión a los usuarios en la aplicación. <br/>Si esta es la primera vez que usa Servicios móviles, complete el tutorial [Introducción a los Servicios móviles](mobile-services-ios-get-started.md).

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google asociada a una dirección de correo electrónico verificada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

3. Diríjase al sitio web [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), inicie sesión con las credenciales de su cuenta de Google, haga clic en **Crear proyecto**, proporcione un **Nombre de proyecto**, y haga clic en **Crear**.

4. En la barra de navegación de la izquierda, haga clic en **API y autenticación** y luego, en **API sociales**, haga clic en **API de Google+** > **Habilitar API**.

5. Haga clic en**API y autenticación** > **Credenciales** > **Pantalla de consentimiento de OAuth**, seleccione su **Dirección de correo electrónico**, escriba un **Nombre de producto** y haga clic en **Guardar**.

6. En la pestaña **Credenciales**, haga clic en **Agregar credenciales** > **Id. de cliente de OAuth 2.0** y luego seleccione **Aplicación web**.

7. Escriba la dirección URL de su servicio móvil en **Orígenes de JavaScript autorizados**, reemplace la dirección URL generada en **Autorizar URI de redirección** por la dirección URL de su servicio móvil anexada a la ruta de acceso `/login/google` y luego haga clic en **Crear identificador de cliente**.

	>[AZURE.NOTE]Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil con la ruta de acceso _signin-google_ anexada como servicio .NET, como `https://todolist.azure-mobile.net/signin-google`. &nbsp;
	
8. En la siguiente pantalla, tome nota de los valores de id. de cliente y el secreto del cliente.

    > [AZURE.IMPORTANT]El secreto de cliente es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.

Ahora está listo para configurar el servicio móvil para utilizar inicio de sesión de Google para autenticarse en la aplicación.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=September15_HO1-->