<properties writer="ricksal" pageTitle="Llamada a una API personalizada desde un cliente Android | Centro de desarrollo móvil" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="ricksal,glenga" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal,glenga" />

# Llamada a una API personalizada desde el cliente

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

En este tema se muestra cómo realizar una llamada a una API personalizada desde una aplicación Android. Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una inserción, actualización, eliminación u operación de lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

La API personalizada que se creó en este tema le ofrece la posibilidad de enviar una sola solicitud POST que establece la marca *completado* en `verdadero` para todos los elementos de la tabla del servicio móvil. Sin esta API personalizada, el cliente tendría que enviar consultas individuales para actualizar la marca para cada elemento todo en la tabla.

Podrá agregar esta funcionalidad a la aplicación que creó cuando completó el tutorial de [Introducción a Servicios móviles] o [Introducción a los datos]. Para ello, debe completar los siguientes pasos:

1. [Definición de la API personalizada]
2. [Actualización de la aplicación para llamar a la API personalizada]
3. [Prueba de la aplicación] 

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a Servicios móviles] o [Introducción a los datos]. 

>[AZURE.NOTE] Si desea ver el código fuente de la aplicación final, haga clic <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">aquí</a>.
>
## <a name="define-custom-api"></a>Definición de la API personalizada

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]


[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]


## Pasos siguientes

Ahora que ha creado una API personalizada y la llamó desde su aplicación de Android, considere la posibilidad de profundizar más sobre los siguientes temas de servicios móviles:



* [Referencia del script del servidor de Servicios móviles]
  <br/>Obtenga más información sobre la creación de API personalizadas.

* [Almacenamiento de scripts de servidor en control de código fuente]
  <br/> Obtenga información sobre la característica de control de código fuente para desarrollar y publicar de manera más sencilla y segura el código de script de API personalizadas.

<!-- Anchors. -->
[Definición de la API personalizada]: #define-custom-api
[Actualización de la aplicación para llamar a la API personalizada]: #update-app
[Prueba de la aplicación]: #test-app
[Pasos siguientes]: #next-steps

<!-- URLs. -->
[SDK de Android para Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Panel Mis aplicaciones]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-android-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-android-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-android-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-android-get-started-push/

[Almacenamiento de scripts de servidor en control de código fuente]: /es-es/documentation/articles/mobile-services-store-scripts-source-control
