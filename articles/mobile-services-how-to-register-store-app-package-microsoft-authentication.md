<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Shared Access Signature Part 1" pageTitle="Register your Windows Store app package for Microsoft authentication" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="" editor="" />

Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft
=============================================================================================

Servicios móviles de Azure es compatible con métodos de autenticación centrada en el cliente y centrada en el servidor. La autenticación centrada en el servidor usa proveedores de identidades, como la cuenta Microsoft. Cuando usa una cuenta Microsoft con la autenticación centrada en el servidor sin registrar la aplicación con Servicios móviles, se pide a los usuarios que suministren sus credenciales cada vez que se solicita la autenticación. Cuando registra la aplicación, las credenciales de inicio de sesión de la cuenta Microsoft se almacenan en caché y se pueden usar para autenticación sin que el usuario deba suministrarlas nuevamente. En este tema se explica cómo registrar el paquete de la aplicación de la Tienda Windows para tener una mejor experiencia de inicio de sesión en la cuenta Microsoft con Servicios móviles de Azure para la autenticación.

La autenticación centrada en el cliente se puede usar para brindar una experiencia de inicio de sesión único en un dispositivo de Windows a través de Live Connect. Si usa las API de Live Connect, no necesita completar los pasos que se describen en este tema. Si desea obtener más información, consulte [Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet).

Registrar la aplicación también le permite enviar notificaciones de inserción a la aplicación. Si ya completó el tutorial [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/) para la aplicación, ya completó los pasos descritos en este tema.

**Nota:**

*Visual Studio 2013 Preview* incluye nuevas características que facilitan el proceso de registro del paquete de la aplicación de la Tienda Windows con Servicios móviles. Para obtener más información, consulte [Guía de inicio rápido: Incorporación de notificaciones de inserción para un servicio móvil](http://go.microsoft.com/fwlink/p/?LinkId=309101) en el Centro de desarrollo de Windows.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Una vez que haya registrado el paquete de la aplicación, recuerde suministrar un valor de **true** para *useSingleSignOn* cuando llama al método [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594). Con esto se ofrece a los usuarios una mejor experiencia de inicio de sesión cuando usan una cuenta Microsoft.

