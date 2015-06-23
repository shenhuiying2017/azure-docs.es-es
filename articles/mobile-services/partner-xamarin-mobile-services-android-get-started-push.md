<properties 
	pageTitle="Incorporación de notificaciones de inserción a la aplicación de Servicios móviles - Servicios móviles" 
	description="Obtenga información acerca de cómo usar notificaciones de inserción en aplicaciones Xamarin.Android con Servicios móviles de Azure." 
	documentationCenter="xamarin" 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/12/2015" 
	ms.author="glenga"/>

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de Xamarin.Android. En este tutorial aprenderá a agregar notificaciones de inserción con el servicio de mensajería en la nube de Google (GCM) al proyecto [Introducción a Servicios móviles]. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones push:

1. [Habilitación del servicio de mensajería en la nube de Google](#register)
2. [Configuración de Servicios móviles](#configure)
4. [Configuración del proyecto para las notificaciones de inserción](#configure-app)
5. [Incorporación de código de notificaciones de inserción a la aplicación](#add-push)
6. [Inserción de datos para recibir notificaciones](#test)

Este tutorial requiere lo siguiente:

+ Una cuenta de Google activa.
+ [Componente cliente Servicio de mensajería en la nube de Google] Agregará este componente durante este tutorial.

Ya debe tener instalados los componentes [Xamarin.Android] y [Servicios móviles de Azure][Azure Mobile Services Component] en el proyecto desde el momento en que completó [Introducción a los Servicios móviles] o [Incorporación de Servicios móviles a una aplicación existente].

##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>Configuración del servicio móvil para enviar solicitudes de inserción

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-scripts"></a>Actualización del script de inserción registrado para enviar notificaciones

>[AZURE.NOTE]En los pasos siguientes se muestra cómo actualizar el script registrado para la operación de inserción en la tabla TodoItem del Portal de administración de Azure. También puede acceder a este script de servicios móviles y editarlo directamente en Visual Studio, en el nodo de Azure del Explorador de servidores.

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="configure-app"></a>Configuración del proyecto existente para notificaciones de inserción

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Incorporación de código de notificaciones de inserción a la aplicación

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a id="test"></a>Prueba de las notificaciones de inserción en su aplicación

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

Ha completado correctamente este tutorial.

## <a name="next-steps"></a>Pasos siguientes

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Incorporación de Servicios móviles a una aplicación existente] <br/>Obtenga más información sobre cómo almacenar y consultar datos con servicios móviles.

* [Introducción a la autenticación](mobile-services-android-get-started-users.md) <br/>Aprenda a autenticar a usuarios de su aplicación con distintos tipos de cuentas mediante los servicios móviles.

* [¿Qué son los Centros de notificaciones?](../notification-hubs-overview.md) <br/>Obtenga más información sobre el funcionamiento de los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas de cliente.

* [Depuración de aplicaciones de los Centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenga orientación sobre la solución de problemas y la depuración de soluciones de los Centros de notificaciones.

* [Uso de la biblioteca de cliente .NET para Servicios móviles](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Obtenga más información sobre cómo usar Servicios móviles con código C# para Xamarin.

* [Referencia de scripts de servidor de Servicios móviles](mobile-services-how-to-use-server-scripts.md) <br/>Obtenga más información sobre cómo implementar lógica empresarial al servicio móvil.

<!-- URLs. -->
[Introducción a Servicios móviles]: mobile-services-ios-get-started.md
[Introducción a los Servicios móviles]: mobile-services-ios-get-started.md
[Incorporación de Servicios móviles a una aplicación existente]: mobile-services-android-get-started-data.md

[Componente cliente Servicio de mensajería en la nube de Google]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
<!--HONumber=54--> 