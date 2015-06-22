<properties 
	pageTitle="Introducción a la inserción (Android) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación .Net Android." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="ricksal"/>

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

En este tema se muestra cómo usar los Servicios móviles de Azure para enviar notificaciones de inserción a la aplicación Android. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles] o [Introducción a los datos] para conectar su proyecto al servicio móvil. Por este motivo, este tutorial requiere también Visual Studio 2013.

>[AZURE.NOTE]Para la versión de Eclipse de este tutorial, consulte [Introducción a las notificaciones de inserción (Eclipse)].
 
##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>Configuración del servicio móvil para enviar solicitudes de inserción

[AZURE.INCLUDE [mobile-services-android-configure-push](../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>Actualización del servicio móvil para enviar notificaciones de inserción

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a name="update-app"></a>Incorporación de notificaciones de inserción a la aplicación

###Comprobación de la versión del SDK de Android

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]


El siguiente paso es instalar los servicios de Google Play. El servicio de mensajería en la nube de Google tiene algunos requisitos mínimos en el nivel de API para desarrollo y prueba, que debe cumplir la propiedad **minSdkVersion** del manifiesto.

Si va a realizar pruebas con un dispositivo antiguo, consulte [Configuración del SDK de Google Play Services] para determinar el valor mínimo que puede configurar, y cómo configurarlo de forma adecuada.

###Incorporación de Google Play Services al proyecto

[AZURE.INCLUDE [Incorporación de Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Incorporación de código

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

##<a name="test-app"></a>Prueba de la aplicación con el servicio móvil publicado

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

###<a id="local-testing"></a> Habilitación de notificaciones de inserción para pruebas locales

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../includes/mobile-services-android-push-notifications-test.md)]

Ha completado correctamente este tutorial.

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial se muestran los conceptos básicos de la habilitación de una aplicación de Android para que use Servicios móviles y Centros de notificaciones para enviar notificaciones de inserción. Le recomendamos que después realice el siguiente tutorial, [Envío de notificaciones de inserción a usuarios autenticados], que muestra cómo usar etiquetas para enviar estas notificaciones con etiquetas desde un servicio móvil a solo un usuario autenticado.

+ [Enviar notificaciones de inserción a los usuarios autenticados] <br/>Aprenda a usar las etiquetas para enviar notificaciones de inserción desde un servicio móvil solo a un usuario autenticado.

+ [Enviar notificaciones de difusión a los suscriptores] <br/>Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

+ [Envío de notificaciones basadas en plantillas a los suscriptores] <br/>Obtenga información sobre cómo usar las plantillas para enviar notificaciones de inserción desde un servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Adición de autenticación a la aplicación][Get started with authentication] <br/>Obtenga información sobre cómo autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

* [¿Qué son los Centros de notificaciones?] <br/>Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

* [Depuración de aplicaciones de los Centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenga orientación sobre la solución de problemas y la depuración de soluciones de los Centros de notificaciones.

* [Uso de la biblioteca de cliente Android para Servicios móviles ] <br/>Obtenga más información sobre cómo usar Servicios móviles con Android.
  
<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Introducción a las notificaciones de inserción (Eclipse)]: mobile-services-dotnet-backend-android-get-started-push-EC.md
[Introducción a los Servicios móviles]: mobile-services-dotnet-backend-android-get-started.md
[Introducción a los datos]: mobile-services-dotnet-backend-android-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-android-get-started-users.md
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545

[Uso de la biblioteca de cliente Android para Servicios móviles ]: mobile-services-android-how-to-use-client-library.md

[Enviar notificaciones de inserción a los usuarios autenticados]: mobile-services-dotnet-backend-android-push-notifications-app-users.md
[Envío de notificaciones de inserción a usuarios autenticados]: mobile-services-dotnet-backend-android-push-notifications-app-users.md

[¿Qué son los Centros de notificaciones?]: notification-hubs/notification-hubs-overview.md
[Enviar notificaciones de difusión a los suscriptores]: notification-hubs/notification-hubs-windows-store-dotnet-send-breaking-news.md
[Envío de notificaciones basadas en plantillas a los suscriptores]: notification-hubs/notification-hubs-windows-store-dotnet-send-localized-breaking-news.md
[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54-->