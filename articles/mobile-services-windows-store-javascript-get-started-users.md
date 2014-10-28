<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"></tags>

# Introducción a la autenticación en Servicios móviles

> [AZURE.SELECTOR-LIST (Plataforma | Backend)]
-   [(iOS | .NET)](/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/)
-   [(iOS | JavaScript)](/es-es/documentation/articles/mobile-services-ios-get-started-users/)
-   [(Windows C# | .NET)](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/)
-   [(Windows C# | Javascript)](/es-es/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users/)
-   [(Windows JavaScript | .NET)](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/)
-   [(Windows JavaScript | Javascript)](/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
-   [(Windows Phone | .NET)](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/)
-   [(Windows Phone | Javascript)](/es-es/documentation/articles/mobile-services-windows-phone-get-started-users/)
-   [(Android | .NET)](/es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/)
-   [(Android | Javascript)](/es-es/documentation/articles/mobile-services-android-get-started-users/)
-   [(Xamarin iOS | .NET)](/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/)
-   [(Xamarin iOS | Javascript)](/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/)
-   [(HTML | Javascript)](/es-es/documentation/articles/mobile-services-html-get-started-users/)
-   [(Xamarin Android | .NET)](/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/)
-   [(Xamarin Android | Javascript)](/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/)
-   [(Appcelerator | Javascript)](/es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users/)

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2.  [Restricción de los permisos de tabla a usuarios autenticados]
3.  [Incorporación de autenticación a la aplicación]
4.  [Almacenamiento de tokens de autenticación en el cliente]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles].

> [WACOM.NOTE] En este tutorial se muestra el flujo de autenticación que administra Servicios móviles mediante una variedad de proveedores de identidades. Este método es fácil de configurar y es compatible con varios proveedores. Para usar Live Connect con una autenticación administrada por el cliente y proporcionar una experiencia de inicio de sesión único en la aplicación de Windows Phone, consulte el tema [Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect]. Al usar la autenticación administrada por el cliente, su aplicación tiene acceso a los datos de usuario que mantiene el proveedor de identidades. Puede obtener los mismos datos de usuario en el servicio móvil al llamar a la función **user.getIdentities()** en los scripts de servidor. Para obtener más información, consulte [este artículo](http://go.microsoft.com/fwlink/p/?LinkId=506605).

## <a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Opcional) Complete los pasos en <a href="/es-es/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft</a>.

    <div class="dev-callout"><b>Nota:</b>
    <p>Este paso es opcional porque solo se aplica al proveedor de inicio de sesi&oacute;n de la cuenta de Microsoft. Cuando registre la informaci&oacute;n del paquete de la aplicaci&oacute;n de la Tienda Windows con Servicios m&oacute;viles, el cliente podr&aacute; volver a usar las credenciales de inicio de sesi&oacute;n de la cuenta de Microsoft para conseguir una experiencia de inicio de sesi&oacute;n &uacute;nico. Si no realiza este procedimiento, los usuarios de inicio de sesi&oacute;n de la cuenta de Microsoft visualizar&aacute;n una solicitud de inicio de sesi&oacute;n cada vez que se llame al m&eacute;todo de inicio de sesi&oacute;n. Complete este paso cuando tenga previsto utilizar el proveedor de identidades de la cuenta de Microsoft.</p>
    </div>

El servicio móvil y la aplicación están ahora configurados para que funcionen con el proveedor de autenticación seleccionado.

## <a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial <a href="/es-es/develop/mobile/tutorials/get-started/">Introducción a los Servicios móviles</a>.

2.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

## <a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)]

## <a name="tokens"></a>Almacenamiento de tokens de autorización en el cliente

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Pasos siguientes

En el tutorial siguiente, [Autorización en el servicio de usuarios de Servicios móviles][Autorización en el servicio de usuarios de Servicios móviles], usará el valor del identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado y lo usará para filtrar los datos devueltos por Servicios móviles.

<!-- Anchors. --> 
<!-- URLs. -->

  [(iOS | .NET)]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
  [(iOS | JavaScript)]: /es-es/documentation/articles/mobile-services-ios-get-started-users/
  [(Windows C# | .NET)]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
  [(Windows C# | Javascript)]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users/
  [(Windows JavaScript | .NET)]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [(Windows JavaScript | Javascript)]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [(Windows Phone | .NET)]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
  [(Windows Phone | Javascript)]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-users/
  [(Android | .NET)]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
  [(Android | Javascript)]: /es-es/documentation/articles/mobile-services-android-get-started-users/
  [(Xamarin iOS | .NET)]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/
  [(Xamarin iOS | Javascript)]: /es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/
  [(HTML | Javascript)]: /es-es/documentation/articles/mobile-services-html-get-started-users/
  [(Xamarin Android | .NET)]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/
  [(Xamarin Android | Javascript)]: /es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/
  [(Appcelerator | Javascript)]: /es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users/
  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
  [Restricción de los permisos de tabla a usuarios autenticados]: #permissions
  [Incorporación de autenticación a la aplicación]: #add-authentication
  [Almacenamiento de tokens de autenticación en el cliente]: #tokens
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-get-started/
  [Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect]: /es-es/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
  [este artículo]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft]: /es-es/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /es-es/develop/mobile/tutorials/get-started/
  [mobile-services-windows-store-javascript-authenticate-app]: ../includes/mobile-services-windows-store-javascript-authenticate-app.md
  [mobile-services-windows-store-javascript-authenticate-app-with-token]: ../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md
  [Autorización en el servicio de usuarios de Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts
