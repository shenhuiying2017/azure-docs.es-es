---
title: "¿Qué son las aplicaciones móviles?"
description: "Obtenga información acerca de qué ventajas aporta Servicios de aplicaciones a las aplicaciones móviles de la empresa."
services: app-service\mobile
documentationcenter: 
author: adrianhall
manager: yochayk
editor: 
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 94c61f4b204b30b16b424fa0716e370a4f4eb51b


---
# <a name="getting-started"></a>¿Qué es Mobile Apps?
El Servicio de aplicaciones de Azure es una [plataforma como servicio](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) totalmente administrada para desarrolladores profesionales que ofrece un amplio conjunto de funcionalidades para escenarios de integración, web y móviles. *Mobile Apps* de *El Servicio de aplicaciones de Azure* cuenta con una plataforma de desarrollo de aplicaciones móviles altamente escalable y disponible globalmente para los desarrolladores empresariales e integradores de sistemas, que proporciona un amplio conjunto de funcionalidades a los desarrolladores móviles.

![Aplicaciones móviles](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>¿Por qué aplicaciones móviles?
*Mobile Apps* de *El Servicio de aplicaciones de Azure* cuenta con una plataforma de desarrollo de aplicaciones móviles altamente escalable y disponible globalmente para los desarrolladores empresariales e integradores de sistemas, que proporciona un amplio conjunto de funcionalidades a los desarrolladores móviles. Con las aplicaciones móviles, puede:

* **Crear aplicaciones nativas y multiplataforma** : si va a crear aplicaciones iOS, Android y Windows nativas o aplicaciones Xamarin o Cordova (Phonegap) multiplataforma, puede aprovechar el Servicio de aplicaciones usando SDK nativos.
* **Conectarse a sus sistemas empresariales** : con aplicaciones móviles, puede agregar el inicio de sesión corporativo en cuestión de minutos y conectarse a sus recursos locales o en la nube empresariales.
* **Crear aplicaciones de uso sin conexión con sincronización de datos**: consiga que sus recursos móviles sean productivos creando aplicaciones que funcionen sin conexión y utilice Mobile Apps para sincronizar los datos en segundo plano cuando exista conectividad con cualquiera de los orígenes de datos empresariales o las API de SaaS.
* **Notificaciones push a millones de personas en cuestión de segundos** : atraer a los clientes con notificaciones push instantáneas en cualquier dispositivo, que se personalizan según sus necesidades y que se envían en el momento oportuno.

## <a name="mobile-app-features"></a>Características de las aplicaciones móviles
Las siguientes características son importantes para el desarrollo móvil habilitado para la nube:

* **Autenticación y autorización**: seleccione de entre una lista creciente de proveedores de identidades, como Azure Active Directory para la autenticación empresarial, y proveedores de redes sociales, como Facebook, Google, Twitter y la cuenta de Microsoft.  Azure Mobile Apps dispone de un servicio OAuth 2.0 para cada proveedor.  También puede integrar el SDK para el proveedor de identidades para la funcionalidad específica del proveedor.
  
  Descubra más acerca de nuestras [características de autenticación].
* **Acceso a datos**: Azure Mobile Apps cuenta con un origen de datos OData v3 adecuado para dispositivos móviles que está vinculado a SQL Azure o a un servidor SQL Server local.  Este servicio puede basarse en Entity Framework, lo que permite integrarlo fácilmente con otros proveedores de datos NoSQL y SQL, incluidos los proveedores de [Azure Table Storage], MongoDB, [DocumentDB] y las API de SaaS API, como Office 365 y Salesforce.com.
* **Sincronización sin conexión**: nuestros SDK de cliente facilitan la tarea de compilar aplicaciones móviles sólidas y dinámicas que funcionan con un conjunto de datos sin conexión, el cual puede sincronizarse automáticamente con los datos de back-end, incluida la resolución de conflictos.
  
  Descubra más acerca de nuestras [características de datos].
* **Notificaciones push**: nuestros SDK de cliente se integran perfectamente con las funcionalidades de registro de Azure Notification Hubs, lo que le permite enviar notificaciones push a millones de usuarios simultáneamente.
  
  Más información acerca de las [características de notificaciones push].
* **SDK de cliente**: proporcionamos un completo conjunto de SDK de cliente que cubren el desarrollo nativo ([iOS], [Android] y [Windows]), el desarrollo multiplataforma ([Xamarin para iOS y Android], [Xamarin Forms]) y el desarrollo de aplicaciones híbridas ([Apache Cordova]).  Cada SDK de cliente está disponible con una licencia MIT y es de código abierto.

## <a name="azure-app-service-features"></a>Características del Servicio de aplicaciones de Azure
Las siguientes características de plataforma suelen ser útiles para los sitios de producción móvil.

* **Ajuste de escala automático** : Servicio de aplicaciones le permite escalar vertical u horizontalmente de forma rápida para administrar cualquier carga entrante del cliente. Seleccione manualmente el número y tamaño de las máquinas virtuales o configure la escala automática para escalar el back-end de aplicación móvil basado en la carga o en la programación.
  
  Descubra más acerca del [Ajuste de escala automático].
* **Entornos de ensayo**: App Service puede ejecutar varias versiones del sitio, lo que le permite realizar pruebas A/B, pruebas en producción como parte de un plan DevOps más amplio y ensayos in situ de un nuevo back-end.
  
  Descubra más acerca de los [Entornos de ensayo].
* **Implementación continua**: App Service puede integrarse con sistemas SCM comunes, lo que permite implementar automáticamente una nueva versión del back-end mediante la inserción de una rama del sistema SCM.
  
  Descubra más acerca de las [opciones de implementación].
* **Red virtual**: App Service puede conectarse a recursos locales mediante redes virtuales, ExpressRoute o conexiones híbridas.
  
  Obtenga más información sobre las [conexiones híbridas], las [redes virtuales] y [ExpressRoute].
* **Entornos aislados y dedicados**: App Service se puede ejecutar en un entorno plenamente aislado y dedicado para ejecutar aplicaciones deAzure App Service de forma segura a gran escala.  Esto es ideal para cargas de trabajo de aplicaciones que requieren acceso de red a gran escala, con aislamiento o seguro.
  
  Descubra más acerca de los [entornos del Servicio de aplicaciones].

## <a name="getting-started"></a>Introducción
Para empezar a trabajar con aplicaciones móviles, siga el tutorial [Introducción] .  Esto abarca los conceptos básicos de generar un back-end móvil y el cliente de su elección y a continuación, integrar la autenticación, la sincronización sin conexión y las notificaciones push.  Puede realizar el tutorial [Introducción] varias veces: una vez para cada aplicación cliente.

Para más información sobre Aplicaciones móviles de Azure, revise nuestro [mapa de aprendizaje].
Para más información sobre la plataforma Servicio de aplicaciones de Azure, consulte [El Servicio de aplicaciones de Azure].

> [!NOTE]
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](https://tryappservice.azure.com/?appServiceName=mobile), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

<!-- URLs. -->
[Migración del servicio móvil a App Service]: app-service-mobile-migrating-from-mobile-services.md
[El Servicio de aplicaciones de Azure]: ../app-service/app-service-value-prop-what-is.md
[Introducción]: app-service-mobile-ios-get-started.md
[Azure Table Storage]: ../storage/storage-getting-started-guide.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[características de autenticación]: ./app-service-mobile-auth.md
[características de datos]: ./app-service-mobile-offline-data-sync.md
[características de notificaciones push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin para iOS y Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[Ajuste de escala automático]: ../app-service-web/web-sites-scale.md
[Entornos de ensayo]: ../app-service-web/web-sites-staged-publishing.md
[opciones de implementación]: ../app-service-web/web-sites-deploy.md
[conexiones híbridas]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[redes virtuales]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[entornos del Servicio de aplicaciones]: ../app-service-web/app-service-app-service-environment-intro.md
[mapa de aprendizaje]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/



<!--HONumber=Nov16_HO2-->


