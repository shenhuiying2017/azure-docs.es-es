<properties 
	pageTitle="Llamada a una API personalizada desde una aplicación de iOS | Servicios móviles" 
	description="Obtenga información acerca de cómo definir una API personalizada y, a continuación, llamarla desde una aplicación de iOS que utiliza Servicios móviles de Azure." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	Writer="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Llamada a una API personalizada desde el cliente

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

En este tema se muestra cómo realizar una llamada a una API personalizada desde una aplicación de iOS. Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

La API personalizada que se creó en este tema le ofrece la posibilidad de enviar una sola consulta de POST que establece la marca completada en  `true` para todos los elementos todo en la tabla. Sin esta API personalizada, el cliente tendría que enviar consultas individuales para actualizar la marca para cada elemento todo en la tabla.

Podrá agregar esta funcionalidad a la aplicación que creó cuando completó el tutorial de [Introducción a los Servicios móviles] o de [Introducción a los datos]. Para hacer esto, debe completar los siguientes pasos:

1. [Definición de la API personalizada]
2. [Actualización de la aplicación para llamar a la API personalizada]
3. [Prueba de la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles]. Este tutorial requiere el [SDK de iOS para Servicios móviles](https://go.microsoft.com/fwLink/p/?LinkID=266533), [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) y iOS 5.0 o versiones posteriores.

## <a name="define-custom-api"></a>Definición de la API personalizada

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](mobile-services-ios-call-custom-api.md)]

## Pasos siguientes

Ahora que ha creado una API personalizada y la llamó desde su aplicación de iOS, considere la posibilidad de profundizar más sobre los siguientes temas de servicios móviles:

* [Referencia del script de servidor de Servicios móviles]
  <br/>Más información acerca de la creación de API personalizadas.

* [Almacenamiento de scripts de servidor en control de fuente]
  <br/> Aprenda a usar la característica de control de código fuente para desarrollar y publicar código script de API personalizadas de manera más fácil y segura.

<!-- Anchors. -->
[Definición de la API personalizada]: #define-custom-api
[Actualización de la aplicación para llamar a la API personalizada]: #update-app
[Prueba de la aplicación]: #test-app
[Pasos siguientes]: #next-steps

<!-- URLs. -->
[Notificaciones de inserción de Windows y de Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Panel Mis aplicaciones]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-ios-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-ios-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-ios-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-ios-get-started-push/
[Almacenamiento de scripts de servidor en control de fuente]: /es-es/documentation/articles/mobile-services-store-scripts-source-control



<!--HONumber=42-->
 