<properties title="Introducci&oacute;n a Servicios m&oacute;viles" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Tareas iniciales][Tareas iniciales]
> -   [¿Qué ha ocurrido?][¿Qué ha ocurrido?]

### <span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

##### Se han agregado referencias

Se ha habilitado el complemento de cliente de servicio móvil de Azure con todas las aplicaciones híbridas multidispositivo.

##### Conexión de valores de cadena para Servicios móviles

En `services\mobileServices\settings`, se ha generado un nuevo archivo JavaScript (.js) con **MobileServiceClient** que contiene la dirección URL y la clave de aplicación del servicio móvil seleccionado. El archivo contiene la inicialización de un objeto de cliente de servicio móvil, similar al código siguiente.

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

[Más información acerca de servicios móviles][Más información acerca de servicios móviles]

  [Tareas iniciales]: /documentation/articles/vs-mobile-services-cordova-getting-started/
  [¿Qué ha ocurrido?]: /documentation/articles/vs-mobile-services-cordova-what-happened/
  [Más información acerca de servicios móviles]: http://azure.microsoft.com/documentation/services/mobile-services/
