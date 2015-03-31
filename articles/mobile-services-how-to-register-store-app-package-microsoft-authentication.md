<properties 
	pageTitle="Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft" 
	description="Obtenga información acerca de cómo registrar su aplicación de la Tienda Windows para la autenticación de Microsoft en la aplicación de Servicios móviles de Azure." 
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

# Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft

Servicios móviles de Azure es compatible con métodos de autenticación centrada en el cliente y centrada en el servidor. La autenticación centrada en el servidor usa proveedores de identidades, como la cuenta Microsoft. Cuando usa una cuenta Microsoft con la autenticación centrada en el servidor sin registrar la aplicación con Servicios móviles, se pide a los usuarios que suministren sus credenciales cada vez que se solicita la autenticación. Cuando registra la aplicación, las credenciales de inicio de sesión de la cuenta Microsoft se almacenan en caché y se pueden usar para autenticación sin que el usuario deba suministrarlas nuevamente. En este tema se explica cómo registrar el paquete de la aplicación de la Tienda Windows para tener una mejor experiencia de inicio de sesión en la cuenta Microsoft con Servicios móviles de Azure para la autenticación. 

>[AZURE.NOTE]Visual Studio 2013 facilita el proceso de registro del paquete de la aplicación de la Tienda Windows con Servicios móviles. Para obtener más información, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">Inicio rápido: Incorporación de notificaciones de inserción para un servicio móvil</a> en el Centro de desarrollo de Windows.

La autenticación administrada por clientes se puede usar para brindar una experiencia de inicio de sesión único en un dispositivo de Windows a través de Live Connect. Si usa las API de Live Connect, no necesita completar los pasos que se describen en este tema. Si desea obtener más información, consulte [Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect].   

[AZURE.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Después de haber registrado el paquete de la aplicación, recuerde proporcionar un valor de <strong>true</strong> para el parámetro <em>useSingleSignOn</em> cuando llame al método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> . Con esto se ofrece a los usuarios una mejor experiencia de inicio de sesión cuando usan una cuenta Microsoft.

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[Introducción a las notificaciones de inserción]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Introducción a usuarios de C#]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Introducción a usuarios de JavaScript]: /develop/mobile/tutorials/get-started-with-users-js/

<!--HONumber=47-->
