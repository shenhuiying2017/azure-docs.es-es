<properties 
	writer="ricksal" 
	pageTitle="Llamada a una API personalizada desde un cliente Android | Centro de desarrollado móvil" 
	description="Obtenga información acerca de cómo definir una API personalizada y, a continuación, llamarla desde una aplicación Android que utiliza Servicios móviles de Azure." 
	services="mobile-services" 
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
	ms.date="02/03/2015" 
	ms.author="ricksal,glenga"/>

# Llamada a una API personalizada desde el cliente

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

En este tema se muestra cómo realizar una llamada a una API personalizada desde una aplicación Android. Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

La API personalizada que se creó en este tema le ofrece la posibilidad de enviar una sola solicitud POST que establece la marca  *completed* en  `true` para todos los elementos de la tabla del servicio móvil. Sin esta API personalizada, el cliente tendría que enviar consultas individuales para actualizar la marca para cada elemento todo en la tabla.

Podrá agregar esta funcionalidad a la aplicación que creó cuando completó el tutorial de [Introducción a los Servicios móviles] o de [Introducción a los datos]. 


>[AZURE.NOTE] Si desea ver el código fuente de la aplicación final, vaya <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">aquí</a>.

## Requisitos previos

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## <a name="define-custom-api"></a>Definición de la API personalizada

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]


[AZURE.INCLUDE [mobile-services-android-call-custom-api](mobile-services-android-call-custom-api.md)]


## Pasos siguientes

Ahora que ha creado una API personalizada y la llamó desde su aplicación de Android, considere la posibilidad de profundizar más sobre los siguientes temas de servicios móviles:



* [Referencia del script del servidor de servicios móviles]
  <br/>Obtenga más información sobre cómo crear API personalizadas.

* [Almacenamiento de scripts de servidor en control de fuente]
  <br/> Aprenda a usar la característica de control de código fuente para desarrollar y publicar código script de API personalizadas de manera más fácil y segura.

<!-- Anchors. -->
[Definición de la API personalizada]: #define-custom-api
[Actualización de la aplicación para llamar a la API personalizada]: #update-app
[Prueba de la aplicación]: #test-app
[Pasos siguientes]: #next-steps

<!-- URLs. -->
[SDK de Android para Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Referencia del script del servidor de servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Panel Mis aplicaciones]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introducción a los Servicios móviles]: /documentation/articles/mobile-services-android-get-started/
[Introducción a los datos]: /documentation/articles/mobile-services-android-get-started-data/
[Introducción a la autenticación]: /documentation/articles/mobile-services-android-get-started-users/
[Introducción a las notificaciones de inserción]: /documentation/articles/mobile-services-android-get-started-push/

[Almacenamiento de scripts de servidor en control de fuente]: /documentation/articles/mobile-services-store-scripts-source-control

<!--HONumber=47-->
 