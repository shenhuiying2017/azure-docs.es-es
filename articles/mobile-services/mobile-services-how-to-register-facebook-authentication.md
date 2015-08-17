<properties 
	pageTitle="Registro para autenticación de Facebook - Servicios móviles" 
	description="Aprenda a usar la autenticación de Facebook en la aplicación de Servicios móviles de Azure." 
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
	ms.date="06/27/2015" 
	ms.author="glenga"/>

# Registro de las aplicaciones para la autenticación de Facebook con Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Facebook para la autenticación con Servicios móviles de Azure.

>[AZURE.NOTE]En este tutorial se describen los [Servicios móviles de Azure], una solución que le ayuda a crear aplicaciones móviles escalables para cualquier plataforma. La solución Servicios móviles facilita la sincronización de datos, la autenticación de usuarios y el envío de notificaciones de inserción. En esta página se incluye el tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Introducción a la autenticación</a> en el que se muestra cómo registrar usuarios en la aplicación. Si esta es la primera vez que usa Servicios móviles, complete el tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Introducción a los Servicios móviles</a>.
	
Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Facebook asociada a una dirección de correo electrónico verificada y a un número de teléfono móvil. Para crear una cuenta de Facebook, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>.

1. Desplácese hasta el sitio web para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">desarrolladores de Facebook</a> e inicie sesión con las credenciales de su cuenta de Facebook.

2. (Opcional) Si aún no está registrado, haga clic en **Mis aplicaciones**, luego en **Registrarse como desarrollador**, acepte la política y siga los pasos de registro.

3. Haga clic en **Mis aplicaciones** > **Agregar una nueva aplicación** > **Configuración avanzada**.

4. Escriba un nombre único **Nombre para mostrar** para la aplicación, elija **Aplicaciones para páginas** en **Categoría**, a continuación, haga clic en **Crear Id. de aplicación** y complete el ejercicio de seguridad.

	Esto crea un nuevo identificador de aplicación de Facebook.

5. Haga clic en **Configuración**, escriba el dominio de su servicio móvil en **App Domains**, escriba un **correo electrónico de contacto** opcional y, a continuación, haga clic en **Añadir plataforma** y seleccione **Página web**.

   	![][3]

6. Escriba la URL de su servicio móvil en **Site URL** (URL del sitio) y haga clic en **Save Changes** (Guardar cambios).

7. Haga clic en **Show** (Mostrar), proporcione la contraseña si se le solicita y anote los valores de **App ID** (Identificador de aplicación) y **App Secret** (Secreto de aplicación).

   	![][5] &nbsp;
	
    >[AZURE.IMPORTANT]El secreto de aplicación es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación. &nbsp;

8. Haga clic en la pestaña **Opciones avanzadas**, escriba la dirección URL del servicio móvil anexada a la ruta de acceso _/login/facebook_ en **URI de redireccionamiento de OAuth válidos** y haga clic en **Guardar cambios**. &nbsp;

     >[AZURE.NOTE]Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil anexada a la ruta de acceso _signin-facebook_ para el servicio .NET, como `https://todolist.azure-mobile.net/signin-facebook`.
       

9. Haga clic en **Estado y revisión** > **Sí** para activar el acceso publico general a su aplicación.

	La cuenta de Facebook que utilizó para la nueva aplicación corresponde a un administrador de la aplicación, que tiene acceso a esta como administrador. Este paso concede acceso al público en general, de modo que la aplicación pueda autenticar a través del uso de otras cuentas de Facebook.


Ahora ya puede habilitar el uso del inicio de sesión de Facebook para autenticarse en la aplicación facilitando el identificador y el secreto de la aplicación a Servicios móviles.

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/
[Servicios móviles de Azure]: http://azure.microsoft.com/services/mobile-services/
 

<!---HONumber=August15_HO6-->