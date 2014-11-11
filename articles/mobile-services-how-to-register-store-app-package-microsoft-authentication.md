<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Shared Access Signature Part 1" pageTitle="Register your Windows Store app package for Microsoft authentication" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft

Servicios móviles de Azure es compatible con métodos de autenticación centrada en el cliente y centrada en el servidor. La autenticación centrada en el servidor usa proveedores de identidades, como la cuenta Microsoft. Cuando usa una cuenta Microsoft con la autenticación centrada en el servidor sin registrar la aplicación con Servicios móviles, se pide a los usuarios que suministren sus credenciales cada vez que se solicita la autenticación. Cuando registra la aplicación, las credenciales de inicio de sesión de la cuenta Microsoft se almacenan en caché y se pueden usar para autenticación sin que el usuario deba suministrarlas nuevamente. En este tema se explica cómo registrar el paquete de la aplicación de la Tienda Windows para tener una mejor experiencia de inicio de sesión en la cuenta Microsoft con Servicios móviles de Azure para la autenticación.

> [WACOM.NOTE] Visual Studio 2013 facilita el proceso de registro del paquete de la aplicación de la Tienda Windows con Servicios móviles. Para obtener más información, consulte [Quickstart: Incorporación de notificaciones de inserción para un servicio móvil][Quickstart: Incorporación de notificaciones de inserción para un servicio móvil] en el Centro de desarrollo de Windows.

La autenticación administrada por clientes se puede usar para brindar una experiencia de inicio de sesión único en un dispositivo de Windows a través de Live Connect. Si usa las API de Live Connect, no necesita completar los pasos que se describen en este tema. Si desea obtener más información, consulte [Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect][Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect].

> [WACOM.NOTE] La autenticación administrada por clientes con Live Connect todavía no se admite en un servicio móvil de back-end de .NET.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Una vez que haya registrado el paquete de la aplicación, recuerde suministrar un valor de **true** para *useSingleSignOn* cuando llama al método [LoginAsync][LoginAsync]. Con esto se ofrece a los usuarios una mejor experiencia de inicio de sesión cuando usan una cuenta Microsoft.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Quickstart: Incorporación de notificaciones de inserción para un servicio móvil]: http://go.microsoft.com/fwlink/p/?LinkId=309101
  [Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect]: /es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [LoginAsync]: http://go.microsoft.com/fwlink/p/?LinkId=311594
