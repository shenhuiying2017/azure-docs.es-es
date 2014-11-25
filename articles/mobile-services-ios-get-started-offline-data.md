<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc" />

# Introducción a la sincronización de datos sin conexión en Servicios móviles

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="C# para Tienda Windows">C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/es-es/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS" class="current">iOS</a>
<a href="/es-es/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/es-es/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

En este tutorial se trata la característica de sincronización sin conexión de Servicios móviles, que permite a los desarrolladores escribir aplicaciones que se pueden usar incluso cuando el usuario final no tiene acceso de red.

La sincronización sin conexión tiene varios usos posibles:

-   Mejorar la capacidad de respuesta de las aplicaciones almacenando en caché datos de servidor de forma local en el dispositivo.
-   Hacer que las aplicaciones sean resistentes ante una conectividad de red intermitente.
-   Permitir a los usuarios finales crear y modificar datos incluso cuando no hay acceso de red, proporcionando escenarios con muy poca o ninguna conectividad.
-   Sincronizar datos entre diferentes dispositivos y detectar conflictos cuando dos dispositivos modifican el mismo registro.

En este tutorial se muestra cómo actualizar la aplicación del tutorial [Introducción a Servicios móviles][Introducción a Servicios móviles] o [Introducción a los datos][Introducción a los datos] para permitir las características sin conexión de Servicios móviles de Azure. A continuación, agregará datos en un escenario sin conexión desconectado, sincronizará dichos elementos con la base de datos en línea e iniciará sesión en el Portal de administración de Azure para ver los cambios efectuados en los datos al ejecutar la aplicación.

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].

## Obtención de la aplicación de ejemplo

**Próximamente estará disponible un tutorial completo. Mientras tanto, vea el ejemplo de [lista de tareas pendientes sin conexión en iOS][lista de tareas pendientes sin conexión en iOS] y la [entrada de blog que lo acompaña][entrada de blog que lo acompaña].**

<!-- ## Next steps  * [How to use the Mobile Services client library for iOS]    --> 



  [Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-ios-get-started/
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-ios-get-started-data/
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28
  [lista de tareas pendientes sin conexión en iOS]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
  [entrada de blog que lo acompaña]: http://aka.ms/iosoffline
