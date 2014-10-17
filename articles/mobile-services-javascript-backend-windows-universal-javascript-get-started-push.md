<properties pageTitle="Get started with push notification using a JavaScript backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile-services,notification-hubs" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga"></tags>

# Introducción a las notificaciones de inserción en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-push][mobile-services-selector-get-started-push]]

En este tema se muestra cómo usar Servicios móviles de Azure con un back-end de JavaScript para enviar notificaciones de inserción a una aplicación universal de Windows. Aprenderá a habilitar las notificaciones de inserción con los Centros de notificaciones de Azure en un proyecto de aplicación universal para Windows. Cuando termine, cada vez que se inserte un registro en la tabla TodoList, el servicio móvil enviará una notificación de inserción desde el back-end de JavaScript a todas las aplicaciones registradas en la Tienda Windows y la Tienda de Windows Phone. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

> [WACOM.NOTE]En este tema se muestra cómo usar las herramientas de Visual Studio 2013 Update 3 para agregar compatibilidad con las notificaciones de inserción de Servicios móviles para una aplicación universal para Windows. Se pueden seguir estos mismos pasos para agregar notificaciones de inserción de Servicios móviles a una aplicación de la Tienda Windows o de la Tienda de Windows Phone 8.1. Si no puede actualizar a Visual Studio 2013 Update 3 o prefiere agregar de forma manual el proyecto de servicio móvil a la solución de aplicación de la Tienda Windows, consulte [esta versión][esta versión] del tema.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registrar la aplicación para las notificaciones de inserción][Registrar la aplicación para las notificaciones de inserción]
2.  [Actualizar el servicio para enviar notificaciones de inserción][Actualizar el servicio para enviar notificaciones de inserción]
3.  [Probar las notificaciones de inserción en su aplicación][Probar las notificaciones de inserción en su aplicación]

Para completar este tutorial, necesitará lo siguiente:

-   Una [cuenta de Microsoft Store][cuenta de Microsoft Store] activa.
-   [Visual Studio 2013 Express para Windows][Visual Studio 2013 Express para Windows] (Update 3 o una versión posterior).

## <span id="register"></span></a>Registrar la aplicación para las notificaciones de inserción

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013][mobile-services-create-new-push-vs2013]]

1.  Vaya a la carpeta de proyecto `\services\mobileServices\scripts`, copie el archivo de script \<*nombre\_del\_servicio*\>.push.register.js generado en la carpeta compartida `\js` y elimínelo de los proyectos de aplicación individuales de Windows y Windows Phone.

2.  Abra el archivo de script en la carpeta de proyecto compartida `\js`, busque el código en la escucha de eventos *activated* que registra la URL de canal del dispositivo con el Centro de notificaciones y elimine la función de la promesa **done**.

    En este tutorial se envían notificaciones cuando se inserta un elemento nuevo y no cuando se llama a una API personalizada.

3.  En el proyecto de aplicación de Windows, abra el archivo default.html y cambie la ruta de acceso de la referencia del archivo de script por la carpeta de proyecto compartida `\js`, con un resultado similar al siguiente:

        <script src="/js/your_service_name.push.register.js"></script>

4.  Repita este paso para el proyecto de aplicación de Windows Phone.

    Ahora, los dos proyectos usan una versión compartida del script de registro de inserción.

Tras habilitar las notificaciones de inserción en la aplicación, actualice el servicio móvil para enviarlas.

## <span id="update-service"></span></a>Actualizar el servicio para enviar notificaciones de inserción

Los pasos siguientes sirven para actualizar el script de inserción registrado para la tabla TodoItem. Puede implementar código similar en cualquier script del servidor o ubicación de los servicios back-end.

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs][mobile-services-javascript-update-script-notification-hubs]]

## <span id="test"></span></a>Probar las notificaciones de inserción en su aplicación

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push][mobile-services-javascript-backend-windows-universal-test-push]]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial se han mostrado los aspectos básicos en la habilitación de aplicaciones de la Tienda Windows para que envíen notificaciones de inserción mediante Servicios móviles y Centros de notificaciones. Le recomendamos que después realice el siguiente tutorial sobre cómo [enviar notificaciones de inserción a usuarios autenticados][enviar notificaciones de inserción a usuarios autenticados], que muestra cómo enviar estas notificaciones con etiquetas desde un servicio móvil a un único usuario autenticado.

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

-   [Introducción a los datos][Introducción a los datos]
    Obtenga más información sobre cómo almacenar y consultar datos con los servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]
    Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

-   [¿Qué son los Centros de notificaciones?][¿Qué son los Centros de notificaciones?]
    Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

-   [Uso de un cliente HTML/JavaScript para Servicios móviles de Azure][Uso de un cliente HTML/JavaScript para Servicios móviles de Azure]
    Obtenga más información sobre cómo usar Servicios móviles desde aplicaciones JavaScript y HTML.



  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [esta versión]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Registrar la aplicación para las notificaciones de inserción]: #register
  [Actualizar el servicio para enviar notificaciones de inserción]: #update-service
  [Probar las notificaciones de inserción en su aplicación]: #test
  [cuenta de Microsoft Store]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-javascript-backend-windows-universal-test-push]: ../includes/mobile-services-javascript-backend-windows-universal-test-push.md
  [enviar notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users
  [¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/
  [Uso de un cliente HTML/JavaScript para Servicios móviles de Azure]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library
