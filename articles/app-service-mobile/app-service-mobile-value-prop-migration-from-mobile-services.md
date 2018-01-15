---
title: "Uso de Mobile Services: ¿cómo ayuda App Service?"
description: "Obtenga información sobre qué ventajas aporta Servicios de aplicaciones a los proyectos de Mobile Services existentes."
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: 
ms.assetid: 26b68a11-8352-4f78-acd2-e4e0ec177781
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: bdf49265b5ef88d11f4ed669aa05036839c574eb
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="getting-started"></a>Uso de Mobile Services: ¿cómo ayuda App Service?
## <a name="overview"></a>Información general
Servicios móviles existentes es seguro y seguirá siendo compatible. Sin embargo hay una serie de ventajas que proporciona la plataforma *Azure App Service* para su aplicación móvil que no están disponibles hoy en día con Mobile Services:

* Oferta más simple, más fácil y más rentable para las aplicaciones que incluyen clientes móviles y web
* Nuevas características de host como trabajos web, CNAME personalizado y una mejor supervisión
* Integración inmediata con Administrador de tráfico
* Conectividad a los recursos locales y VPN mediante una red virtual además de las conexiones híbridas
* Supervisión, alertas y solución de problemas para su aplicación mediante NewRelic o AppInsights
* Espectro más completo de los recursos de procesos subyacentes y precios
* Escalado automático integrado, equilibrio de carga y supervisión del rendimiento
* Capacidades de prueba en producción, reversión, copia de seguridad y almacenamiento provisional incorporadas

## <a name="new-hosting-features"></a>Nuevas características de hospedaje
En *Azure App Service*, el código de back-end de la *aplicación móvil* se ejecuta en el mismo contenedor que la aplicación web y la aplicación de API. De esta forma, puede beneficiarse de todas las características de este contenedor, incluidas la que no están actualmente en Mobile Services:

* Adición de lógica de back-end de ejecución continua a través de trabajos web
* Garantía de que el código de back-end siempre está ejecutándose
* Uso de CNames para proporcionar nombres descriptivos y estables en los extremos de back-end móvil
* Escala geográfica de la aplicación con Administrador de tráfico
* Se incluyen las bibliotecas y los paquetes que desee.
* (Para. NET) Aproveche todas las características de ASP.NET, incluido MVC.
* (Para Node.js) Aproveche cualquier biblioteca puramente de JavaScript del ecosistema de Node, incluidas las bibliotecas comunes de MVC.

## <a name="access-on-premises-data-using-vnet"></a>Acceso a datos locales con red virtual
Con Mobile Services ahora puede usar las conexiones híbridas para acceder a los recursos locales. Sin embargo, hay situaciones en las que es preferible una solución de VPN. Con *Azure App Service* puede usar la red virtual de Azure para el código de back-end de la aplicación móvil.

## <a name="use-your-favorite-backend-language"></a>Uso del lenguaje de back-end favorito
*Azure App Service* ofrece una compatibilidad más amplia y completa para las plataformas ASP.NET y Node.js, incluido el acceso a los tiempos de ejecución más recientes.

## <a name="set-up-automatic-scale"></a>Configuración de la escala automática
Con Mobile Services, todas las instancias de su código de back-end se ejecutaban en máquinas virtuales pequeñas. *Azure App Service* le permite seleccionar el tamaño de las máquinas virtuales desde un conjunto mucho más completo de opciones. También puede escalar de forma vertical u horizontal rápidamente para controlar cualquier carga de cliente entrante basándose en varias métricas de rendimiento.

## <a name="be-in-the-know"></a>Sepa lo que ocurre
Reaccione a los problemas en tiempo real gracias a la supervisión y a las alertas que permiten el aviso automático a usted o a su equipo. Integre análisis de aplicaciones avanzadas y la funcionalidad de supervisión de New Relic y AppInsights para obtener una perspectiva incluso más completa acerca de cómo funciona la aplicación móvil. Con *Azure App Service*, ahora puede configurar alertas basadas en varias métricas de rendimiento, ya sea mediante programación o a través de Azure Portal.

## <a name="keep-your-assets-safe"></a>Proteja los activos
Realice una copia de seguridad automática del back-end y la base de datos. El código y los datos están seguros frente a desastres y se restauran fácilmente, por lo que puede llevar a cabo su actividad comercial con confianza.

## <a name="ready-stage-go"></a>Preparado, listo, ¡ya!
Con *Azure App Service* ahora puede crear varios entornos de ensayo y prueba privados para las aplicaciones móviles. Úselos para realizar la prueba antes de la implementación. Cambie a una producción sin tiempo de inactividad. Las aplicaciones web se cargan previamente, por lo que se garantiza la mejor experiencia del cliente.

Para comenzar a aprovechar *App Service* en su servicio móvil existente, siga este [tutorial](app-service-mobile-migrating-from-mobile-services.md).
