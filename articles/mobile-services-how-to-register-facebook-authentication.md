<properties linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Register for Facebook Authentication" pageTitle="Register for Facebook authentication - Mobile Services" metaKeywords="Azure Facebook, Azure Facebook, Azure authenticate Mobile Services" description="Learn how to use Facebook authentication in your Azure Mobile Services app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Facebook authentication with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registro de las aplicaciones para la autenticación de Facebook con Servicios móviles

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Facebook para la autenticación con Servicios móviles de Azure.

> [WACOM.NOTE] En este tutorial se describen los [Servicios móviles de Azure][], una solución que le ayuda a crear aplicaciones móviles escalables para cualquier plataforma. La solución Servicios móviles facilita la sincronización de datos, la autenticación de usuarios y el envío de notificaciones de inserción. En esta página se incluye el tutorial [Introducción a la autenticación][] en el que se muestra cómo registrar usuarios en la aplicación. Si esta es la primera vez que usa Servicios móviles, complete el tutorial [Introducción a los Servicios móviles][].

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Facebook asociada a una dirección de correo electrónico verificada y a un número de teléfono móvil. Para crear una cuenta de Facebook, vaya a [facebook.com][].

1.  Desplácese hasta el sitio web para [desarrolladores de Facebook][] e inicie sesión con las credenciales de su cuenta de Facebook.

2.  (Opcional) Si aún no está registrado, haga clic en **Aplicaciones** y, a continuación, en **Register as a Developer**; acepte la política y siga los pasos de registro.

    ![][]

3.  Haga clic en **Aplicaciones** y, a continuación, en **Crea una nueva aplicación**.

    ![][1]

4.  Elija un nombre exclusivo para la aplicación, seleccione **Aplicaciones para páginas**, haga clic en **Creación de aplicaciones** y conteste a la pregunta de seguridad.

    ![][2]

    De esta manera la aplicación se registra en Facebook.

5.  Haga clic en **Configuración** y escriba el dominio de su servicio móvil en **Dominios de aplicación**. Además, especifique un **Correo electrónico de contacto**, haga clic en **Agregar plataforma** y seleccione **Sitio web**.

    ![][3]

6.  Escriba la URL de su servicio móvil en **URL del sitio** y, a continuación, haga clic en **Guardar cambios**.

    ![][4]

7.  Haga clic en **Mostrar**, escriba su contraseña si se le solicita y, a continuación, tome nota de los valores de los campos **ID de la aplicación** y **App Secret**.

    ![][5]

    <div class="dev-callout"><b>Nota de seguridad</b>
<p>El secreto de aplicaci&oacute;n es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicaci&oacute;n.</p>
</div>

8.  Haga clic en la pestaña **Avanzado**, escriba la dirección URL del servicio móvil anexada a la ruta de acceso */login/facebook* en **URI de redireccionamiento de OAuth válidos** y haga clic en **Guardar cambios**.

    > [WACOM.NOTE] Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil anexada a la ruta de acceso *signin-facebook* para el servicio .NET, por ejemplo `https://todolist.azure-mobile.net/signin-facebook`.

    ![][6]

9.  La cuenta de Facebook para la que definió la nueva aplicación corresponde a un administrador de la aplicación, que tiene acceso a esta como administrador. Para autenticar otras cuentas de Facebook, estas necesitan acceso a la aplicación. Este paso concede acceso al público en general, de modo que la aplicación pueda autenticar otras cuentas de Facebook. Haga clic en **Estado y revisión**. A continuación, haga clic en **Sí** para habilitar el acceso del público en general.

    ![][7]

Ahora ya puede habilitar el uso del inicio de sesión de Facebook para autenticarse en la aplicación facilitando el identificador y el secreto de la aplicación a Servicios móviles.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Servicios móviles de Azure]: http://azure.microsoft.com/es-es/services/mobile-services/
  [Introducción a la autenticación]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-ios-get-started-users/
  [Introducción a los Servicios móviles]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-ios-get-started/
  [facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
  [desarrolladores de Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
  []: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
  [1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
  [2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
  [3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
  [4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
  [5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
  [6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png
  [7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
