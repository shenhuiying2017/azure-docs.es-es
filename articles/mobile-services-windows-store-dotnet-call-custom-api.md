<properties urlDisplayName="Call a custom API from the client" pageTitle="Llamada a una API personalizada desde un cliente de la Tienda Windows - Servicios móviles" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Llamada a una API personalizada desde el cliente

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

En este tema se muestra cómo realizar una llamada a una API personalizada desde una aplicación de Tienda Windows. Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una inserción, actualización, eliminación u operación de lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

La API personalizada que se creó en este tema le ofrece la posibilidad de enviar una sola consulta de POST que establece la marca completada en "true" para todos los elementos todo en la tabla. Sin esta API personalizada, el cliente tendría que enviar consultas individuales para actualizar la marca para cada elemento todo en la tabla.

Podrá agregar esta funcionalidad a la aplicación que creó cuando completó el tutorial [Introducción a Servicios móviles] o [Introducción a los datos]. Para ello, debe completar los siguientes pasos:

1. [Definición de la API personalizada]
2. [Actualización de la aplicación para llamar a la API personalizada]
3. [Prueba de la aplicación] 

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a Servicios móviles] o [Introducción a los datos]. Este tutorial usa Visual Studio 2012 Express para Windows 8.

## <a name="define-custom-api"></a>Definición de la API personalizada

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-call-custom-api](../includes/mobile-services-windows-store-dotnet-call-custom-api.md)]


## Pasos siguientes

Ahora que ha creado una API personalizada y la llamó desde su aplicación de la Tienda Windows, considere la posibilidad de profundizar más sobre los siguientes temas de servicios móviles:

* [Definición de una API personalizada que admita notificaciones periódicas]
	<br/>Aprenda cómo usar una API personalizada para admitir notificaciones periódicas en una aplicación de Tienda Windows. Con las notificaciones periódicas habilitadas, Windows tendrá acceso de manera periódica a su extremo de API personalizada y usará el XML devuelto, en un formato específico de icono, para actualizar el icono de la aplicación en el menú Inicio.

* [Referencia del script del servidor de Servicios móviles]
  <br/>Obtenga más información acerca de la creación de API personalizadas.

* [Almacenamiento de scripts de servidor en control de código fuente]
  <br/> Aprenda a usar la característica de control de código fuente para desarrollar y publicar de manera más fácil y segura código script de la API personalizada.

<!-- Anchors. -->
[Definición de la API personalizada]: #define-custom-api
[Actualización de la aplicación para llamar a la API personalizada]: #update-app
[Prueba de la aplicación]: #test-app
[Pasos siguientes]: #next-steps

<!-- URLs. -->
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/

[Definición de una API personalizada que admita notificaciones periódicas]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications
[Almacenamiento de scripts de servidor en control de código fuente]: /es-es/documentation/articles/mobile-services-store-scripts-source-control

