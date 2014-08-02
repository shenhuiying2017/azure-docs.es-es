<properties pageTitle="Service-side authorization (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Autorización en el servicio de usuarios de Servicios móviles
============================================================

[C\# para Tienda Windows](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts "JavaScript para Tienda Windows")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts "iOS")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/documentation/articles/mobile-services-ios-authorize-users-in-scripts/ "Back-end de JavaScript")

En este tema se muestra cómo autorizar a los usuarios autenticados el acceso a los datos de Servicios móviles de Azure desde una aplicación iOS. En este tutorial, agregará código a los métodos de acceso a datos de su controlador para filtrar las consultas según el identificador de usuario de los usuarios autenticados, lo que garantiza que cada usuario solo pueda ver sus propios datos.

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a la autenticación](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users). Antes de comenzar este tutorial, primero debe completar [Introducción a la autenticación](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users).

Modificación de los métodos de acceso a datos
---------------------------------------------

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

Prueba de la aplicación
-----------------------

1.  En Xcode, abra el proyecto que ha modificado al completar el tutorial [Introducción a la autenticación](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users).

2.  Presione el botón **Run** para compilar el proyecto, inicie la aplicación en el emulador de iPhone e inicie sesión con su proveedor de identidades elegido.

	Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

3.  En la aplicación, escriba un texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

	![][3]

	De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userID correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

4.  Vuelva a la tabla **todoitem** en el [Portal de administración de Azure], haga clic en **Browse** y compruebe que los elementos recién agregados dispongan ahora de un valor userId asociado.

5.  (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior.


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
