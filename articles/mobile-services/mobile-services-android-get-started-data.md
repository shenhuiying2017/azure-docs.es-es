---
title: Introducción a los datos en Android (back-end JavaScript) | Microsoft Docs
description: Obtenga información sobre cómo comenzar a usar Servicios móviles para aprovechar los datos de su aplicación Android (back-end JavaScript).
services: mobile-services
documentationcenter: android
author: RickSaling
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 07/21/2016
ms.author: ricksal

---
# Incorporación de servicios móviles a una aplicación Android existente (back-end JavaScript)
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Para información sobre la versión de Aplicaciones móviles equivalente de este tema, consulte [Uso de la biblioteca de cliente Android para Aplicaciones móviles](../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md).
> 
> 

## Resumen
Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Android. En este tutorial descargará una aplicación que almacena datos en memoria, creará un servicio móvil nuevo, integrará la aplicación con el servicio móvil para que almacene y actualice datos en los Servicios móviles de Azure en lugar de hacerlo de manera local y usará el Portal de Azure clásico. para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

Este tutorial le ayuda a comprender con más detalle cómo los Servicios móviles de Azure pueden almacenar y recuperar datos desde una aplicación Android. De ese modo, este tutorial recorre mucho de los pasos ya completados para usted en el tutorial de inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles](mobile-services-android-get-started.md).

## Requisitos previos
Para completar este tutorial, necesitará lo siguiente:

* una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357).
* el [SDK de Android para Servicios móviles]
* el [entorno de desarrollo integrado de Android Studio](https://developer.android.com/sdk/index.html), que incluye el SDK de Android y Android 4.2 o una versión más reciente. El proyecto GetStartedWithData descargado requiere Android 4.2 o una versión más reciente. No obstante, el SDK para Servicios móviles solo requiere Android 2.2 o una versión más reciente.

## Código de ejemplo
Para ver el código fuente completo, vaya [aquí](https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/AndroidStudio).

## Descarga del proyecto GetStartedWithData
### Obtención del código de ejemplo
[!INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### Inspección y ejecución del código de ejemplo
[!INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## Crear un nuevo servicio móvil en el Portal de Azure clásico
[!INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## Agregar una tabla nueva al servicio móvil
[!INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## Actualización de la aplicación para usar el servicio móvil para el acceso a datos
[!INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]

## Prueba de la aplicación con su servicio móvil nuevo
Ahora que la aplicación se ha actualizado para usar los Servicios móviles para almacenamiento back-end, puede probarla con los Servicios móviles usando el emulador de Android o un teléfono Android.

1. En el menú **Ejecutar**, haga clic en **Ejecutar aplicación** para iniciar el proyecto.
   
    De este modo se ejecuta su aplicación, que se ha creado con el SDK de Android, y se usa la biblioteca del cliente para enviar una consulta que devuelve los elementos desde su servicio móvil.
2. Como antes, escriba texto descriptivo y, a continuación, haga clic en **Agregar**.
   
       Esto envía un elemento nuevo como inserción al servicio móvil.
3. Inicie sesión en el [Portal de Azure clásico], haga clic en **Servicios móviles **y en su servicio móvil.
4. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.
   
       ![][9]
   
       Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

Así concluye el tutorial **Introducción a los datos** para Android.

## Solución de problemas
### Comprobación de la versión del SDK de Android
[!INCLUDE [Comprobar el SDK](../../includes/mobile-services-verify-android-sdk-version.md)]

## Pasos siguientes
Este tutorial muestra los aspectos básicos de la habilitación de una aplicación Android para que funcione con los datos de los Servicios móviles. Pruebe estos otros tutoriales de Android:

* [Introducción a la autenticación](mobile-services-android-get-started-users.md) <br/>Aprenda a autenticar a los usuarios de su aplicación.
* [Introducción a las notificaciones de inserción](mobile-services-javascript-backend-android-get-started-push.md) <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->

[Portal de Azure clásico]: https://manage.windowsazure.com/
[SDK de Android para Servicios móviles]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!---HONumber=AcomDC_0727_2016-->