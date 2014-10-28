<properties linkid="mobile-services-call-custom-api-js" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from a Windows Store JS client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Llamada a una API personalizada desde el cliente

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="JavaScript para Tienda Windows" class="current">JavaScript para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone" class="current">Windows Phone</a><a href="/es-es/documentation/articles/mobile-services-ios-call-custom-api" title="iOS">iOS</a><a href="/es-es/documentation/articles/mobile-services-android-call-custom-api" title="Android" class="current">Android</a><a href="/es-es/documentation/articles/mobile-services-html-call-custom-api" title="HTML">HTML</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api" title="Back-end de .NET">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-call-custom-api"  title="Back-end de JavaScript" class="current">Back-end de JavaScript</a></div>

En este tema se muestra cómo realizar una llamada a una API personalizada desde una aplicación de Tienda Windows. Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una inserción, actualización, eliminación u operación de lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

La API personalizada que se creó en este tema le ofrece la posibilidad de enviar una sola consulta de POST que establece la marca completada en `true` para todos los elementos todo en la tabla. Sin esta API personalizada, el cliente tendría que enviar consultas individuales para actualizar la marca para cada elemento todo en la tabla.

Podrá agregar esta funcionalidad a la aplicación que creó cuando completó el tutorial de [Introducción a los Servicios móviles][] o de [Introducción a los datos][]. Para hacer esto, debe completar los siguientes pasos:

1.  [Definición de la API personalizada][]
2.  [Actualización de la aplicación para llamar a la API personalizada][]
3.  [Prueba de la aplicación][]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles][] o [Introducción a los datos][]. Este tutorial usa Visual Studio 2012 Express para Windows 8.

## <a name="define-custom-api"></a>Definición de la API personalizada

[WACOM.INCLUDE [mobile-services-create-custom-api][]]

[WACOM.INCLUDE [mobile-services-windows-store-javascript-call-custom-api][]]

## Pasos siguientes

Ahora que ha creado una API personalizada y la llamó desde su aplicación de la Tienda Windows, considere la posibilidad de profundizar más sobre los siguientes temas de servicios móviles:

-   [Definición de una API personalizada que admita notificaciones periódicas][]
    Aprenda cómo usar una API personalizada para admitir notificaciones periódicas en una aplicación de Tienda Windows. Con las notificaciones periódicas habilitadas, Windows tendrá acceso de manera periódica a su extremo de API personalizada y usará el XML devuelto, en un formato específico de icono, para actualizar el icono de la aplicación en el menú Inicio.

-   [Referencia del script del servidor de Servicios móviles][]
    Obtenga más información sobre la creación de API personalizadas.

-   [Almacenamiento de scripts de servidor en control de código fuente][]
     Obtenga información sobre cómo usar la característica de control de código fuente para desarrollar y publicar de manera más fácil y segura código script de la API personalizada.

<!-- Anchors. --> <!-- URLs. -->

  [C# para Tienda Windows]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone"
  [iOS]: /es-es/documentation/articles/mobile-services-ios-call-custom-api "iOS"
  [Android]: /es-es/documentation/articles/mobile-services-android-call-custom-api "Android"
  [HTML]: /es-es/documentation/articles/mobile-services-html-call-custom-api "HTML"
  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Back-end de JavaScript"
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-get-started/
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
  [Definición de la API personalizada]: #define-custom-api
  [Actualización de la aplicación para llamar a la API personalizada]: #update-app
  [Prueba de la aplicación]: #test-app
  [mobile-services-create-custom-api]: ../includes/mobile-services-create-custom-api.md
  [mobile-services-windows-store-javascript-call-custom-api]: ../includes/mobile-services-windows-store-javascript-call-custom-api.md
  [Definición de una API personalizada que admita notificaciones periódicas]: /es-es/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Almacenamiento de scripts de servidor en control de código fuente]: /es-es/documentation/articles/mobile-services-store-scripts-source-control
