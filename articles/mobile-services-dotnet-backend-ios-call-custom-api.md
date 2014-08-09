<properties pageTitle="Call a custom API from an iOS app | Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an iOS app that uses Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Call a custom API from the client" authors="" solutions="" writer="krisragh" manager="" editor="" />

Llamada a una API personalizada desde el cliente
================================================

[C\# para Tienda Windows](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api "JavaScript para Tienda Windows")[Windows Phone](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api "Windows Phone")[iOS](/es-es/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api "iOS")[Android](/es-es/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api "Android")

[Back-end de .NET](/es-es/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api "Back-end de .NET") | [Back-end de JavaScript](/es-es/documentation/articles/mobile-services-ios-call-custom-api "Back-end de JavaScript")

En este tema se muestra cómo realizar una llamada a una API personalizada desde una aplicación de iOS. Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una inserción, actualización, eliminación u operación de lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

La API personalizada que se creó en este tema le ofrece la posibilidad de enviar una sola consulta de POST que establece la marca completada en `true` para todos los elementos todo en la tabla. Sin esta API personalizada, el cliente tendría que enviar consultas individuales para actualizar la marca para cada elemento todo en la tabla.

Podrá agregar esta funcionalidad a la aplicación que creó cuando completó el tutorial de [Introducción a los Servicios móviles](/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started/) o de [Introducción a los datos](/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/). Para hacer esto, debe completar los siguientes pasos:

1.  [Definición de la API personalizada](#define-custom-api)
2.  [Actualización de la aplicación para llamar a la API personalizada](#update-app)
3.  [Prueba de la aplicación](#test-app)

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles](/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started/). Este tutorial requiere el [SDK de iOS para Servicios móviles](https://go.microsoft.com/fwLink/p/?LinkID=266533), [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) y iOS 5.0 o versiones posteriores.

Definición de la API personalizada
----------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

Pasos siguientes
----------------

Ahora que ha creado una API personalizada y la llamó desde su aplicación de iOS, considere la posibilidad de profundizar más sobre los siguientes temas de servicios móviles:

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
    Obtenga más información sobre la creación de API personalizadas.

-   [Almacenamiento de scripts de servidor en control de código fuente](/es-es/documentation/articles/mobile-services-store-scripts-source-control)
     Obtenga información sobre cómo usar la característica de control de código fuente para desarrollar y publicar de manera más fácil y segura código script de la API personalizada.


