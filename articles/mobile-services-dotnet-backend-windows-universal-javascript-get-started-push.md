<properties pageTitle="Get started with push notification using a .NET backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga"></tags>

# Introducción a las notificaciones de inserción en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Este tema muestra cómo usar Servicios móviles de Azure con un back-end .NET para enviar notificaciones de inserción a una aplicación universal para Windows. Aprenderá a habilitar las notificaciones de inserción con los Centros de notificaciones de Azure en un proyecto de aplicación universal para Windows. Cuando termine, el servicio móvil enviará una notificación de inserción desde el back-end .NET a todas las aplicaciones de la Tienda Windows y de la Tienda de Windows Phone, siempre que se inserte un registro en la tabla TodoList. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

> [WACOM.NOTE]Este tema muestra cómo usar las herramientas de Visual Studio Professional 2013 Update 3 para agregar la compatibilidad con las notificaciones de inserción de Servicios móviles para una aplicación universal para Windows. Se pueden seguir estos mismos pasos para agregar notificaciones de inserción de Servicios móviles a una aplicación de la Tienda Windows o de la Tienda de Windows Phone 8.1. Si no puede actualizar a Visual Studio Professional 2013 Update 3 o prefiere agregar de forma manual el proyecto de servicio móvil a la solución de aplicación de la Tienda Windows, consulte [esta versión][esta versión] del tema.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registrar la aplicación para las notificaciones de inserción][Registrar la aplicación para las notificaciones de inserción]
2.  [Actualizar el servicio para enviar notificaciones de inserción][Actualizar el servicio para enviar notificaciones de inserción]
3.  [Habilitar notificaciones de inserción para pruebas locales][Habilitar notificaciones de inserción para pruebas locales]
4.  [Probar las notificaciones de inserción en su aplicación][Probar las notificaciones de inserción en su aplicación]

Para completar este tutorial, necesitará lo siguiente:

-   Una [cuenta de Microsoft Store][cuenta de Microsoft Store] activa.
-   [Visual Studio Professional 2013][Visual Studio Professional 2013] (Update 3 o una versión posterior).
    <br/ >Hay disponible una versión de prueba gratuita.

## <span id="register"></span></a>Registrar la aplicación para las notificaciones de inserción

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Vaya a la carpeta de proyecto `\services\mobileServices\scripts`, copie el archivo de script \<*nombre\_del\_servicio*\>.push.register.js generado en la carpeta compartida `\js` y elimínelo de los proyectos de aplicación individuales de Windows y Windows Phone.

2.  Abra el archivo de script en la carpeta de proyecto compartida `\js`, busque el código en la escucha de eventos *activated* que registra la URL de canal del dispositivo con el Centro de notificaciones y elimine la función de la promesa **done**.

    En este tutorial se envían notificaciones cuando se inserta un elemento nuevo y no cuando se llama a una API personalizada.

3.  En el proyecto de aplicación de Windows, abra el archivo default.html y cambie la ruta de acceso de la referencia del archivo de script por la carpeta de proyecto compartida `\js`, con un resultado similar al siguiente:

        <script src="/js/your_service_name.push.register.js"></script>

4.  Repita este paso para el proyecto de aplicación de Windows Phone.

    Ahora, los dos proyectos usan una versión compartida del script de registro de inserción.

Tras habilitar las notificaciones de inserción en la aplicación, actualice el servicio móvil para enviarlas.

## <span id="update-service"></span></a>Actualizar el servicio para enviar notificaciones de inserción

Los pasos siguientes actualizan la clase TodoItemController existente para enviar una notificación de inserción a todos los dispositivos registrados cuando se inserta un elemento nuevo. Puede implementar código similar en cualquier elemento [ApiController] o [TableController] personalizado, o en cualquier otra ubicación de los servicios back-end.

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a>Habilitar notificaciones de inserción para pruebas locales

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Los pasos pendientes de esta sección son opcionales. Permiten probar la aplicación con un servicio móvil que se ejecuta en el equipo local. Si quiere probar las notificaciones de inserción con el servicio móvil que se ejecuta en Azure, vaya directamente a la última sección. Esto se debe a que el Asistente para agregar notificaciones de inserción ya ha configurado la aplicación para conectar con el servicio que se ejecuta en Azure.

> [WACOM.NOTE]Nunca use un servicio móvil de producción para probar y desarrollar el trabajo. Para probarlo, publique siempre el proyecto de servicio móvil en un servicio de almacenamiento provisional independiente.

1.  Vaya a la carpeta de proyecto `\services\mobileServices\settings`, copie el archivo de script \<*nombre\_del\_servicio*\>.js generado en la carpeta de proyecto compartida `\js` y elimínelo de los proyectos de aplicación individuales de Windows y Windows Phone. Elimínelo también de la carpeta `\services\mobileServices\scripts` en los distintos proyectos de aplicación (si contienen esta carpeta).

2.  Abra el archivo de script en la carpeta de proyecto compartida `\js` y convierta en comentario el código existente que define el [objeto MobileServiceClient][objeto MobileServiceClient] con el que se accede al servicio móvil que se ejecuta en Azure.

3.  Agregue una nueva definición de objeto **MobileServiceClient** con el mismo nombre, pero con la URL del host local en el constructor. Algo similar a lo siguiente:

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        var todolistClient = new WindowsAzure.MobileServiceClient(
            "http://localhost:4584");

    Mediante este objeto **MobileServiceClient**, la aplicación conectará con el servicio local en lugar de con la versión que se hospeda en Azure. Para volver y ejecutar la aplicación con el servicio móvil que se hospeda en Azure, cambie las definiciones de objeto **MobileServiceClient** por las originales.

## <span id="test"></span></a>Probar las notificaciones de inserción en su aplicación

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

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
  [Habilitar notificaciones de inserción para pruebas locales]: #local-testing
  [Probar las notificaciones de inserción en su aplicación]: #test
  [cuenta de Microsoft Store]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push-vs2013]: ../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md
  [objeto MobileServiceClient]: http://msdn.microsoft.com/es-es/library/azure/jj554219.aspx
  [mobile-services-dotnet-backend-windows-universal-test-push]: ../includes/mobile-services-dotnet-backend-windows-universal-test-push.md
  [enviar notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users
  [¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/
  [Uso de un cliente HTML/JavaScript para Servicios móviles de Azure]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library
