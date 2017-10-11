---
title: "Comparación de Azure Mobile Engagement con otros servicios similares de Azure"
description: "Comparación de Azure Mobile Engagement con otros servicios similares de Azure: HockeyApp, AppInsights y Centros de notificaciones"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f114775-3a9a-4dd4-8d59-b10d1da9a68b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7df2eb9ecebe3313dad9c15171552a084787f6b8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Comparación de Azure Mobile Engagement con otros servicios similares de Azure
La lista de servicios ofrecidos por Microsoft Azure no deja de crecer y, en ocasiones, es posible que se pregunte en qué se diferencia Azure Mobile Engagement de este otro servicio sobre el que acaba de leer u oír. En este artículo se intentan aclarar algunas ideas y se le indica que elija Azure Mobile Engagement cuando su uso le resulte más adecuado. 

Azure Mobile Engagement es un servicio dirigido especialmente **a responsables de marketing o vendedores digitales**, pero podría usarlo cualquier **publicador o propietario de una aplicación móvil** que desee aumentar el uso, la retención y la monetización de sus aplicaciones móviles. 

*Es una plataforma de participación de usuarios de software como servicio (SaaS) que proporciona información detallada controlada por datos sobre el uso de las aplicaciones y segmentación de usuarios en tiempo real, y habilita las notificaciones push dependientes de contexto y la mensajería en la aplicación.* 

Al desglosar esta definición, tenemos las siguientes características clave que también destacan su propuesta de valor único:

1. **Notificaciones push dependientes del contexto y mensajería en la aplicación**
   
   Este es el objetivo principal del producto: realizar notificaciones push personalizadas y dirigidas. Y, para que esto ocurra, recopilamos datos de análisis de comportamiento enriquecidos. 
2. **Información detallada controlada por datos sobre el uso de la aplicación**
   
   Proporcionamos SDK multiplataforma para recopilar análisis de comportamiento acerca de los usuarios de la aplicación. Tenga en cuenta el término análisis del comportamiento (frente a análisis del rendimiento) porque nos centramos en cómo los usuarios usan la aplicación. Aunque recopilamos datos básicos de análisis del rendimiento sobre errores, bloqueos, etc., no es el enfoque principal del producto. 
3. **Segmentación de usuarios en tiempo real**
   
   Una vez recopilados los datos de análisis del comportamiento de los usuarios de la aplicación, le permitimos que segmente la audiencia en función de varios parámetros y que recopile datos para poder ejecutar campañas de inserción dirigidas. 
4. **Software como servicio (SaaS):**
   
   Tenemos un portal independiente del Portal de administración de Azure que está optimizado para interactuar y visualizar los análisis del comportamiento enriquecidos acerca de los usuarios de la aplicación, y para ejecutar campañas de inserción de marketing. El producto está preparado para que empiece a trabajar muy rápidamente.   

Con este conjunto de características diferenciadoras en la mano, esta es la comparación entre la nuestra y otras ofertas aparentemente similares de Azure; tenga en cuenta que el artículo no hace una comparación de las características, sino que usa un enfoque basado en escenarios para definir qué producto funciona mejor:

1. [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) es la solución móvil de DevOps de Microsoft. Con ella, podrá distribuir compilaciones a evaluadores de la versión beta, recopilar datos de bloqueo y obtener comentarios de los usuarios. También se integra en Visual Studio Team Services, lo que facilita las implementaciones de compilaciones y la integración de elementos de trabajo. 
   
   Aquí el foco está en DevOps y en la recopilación de datos de análisis de rendimiento acerca de las aplicaciones móviles. Podría terminar integrando tanto HockeyApps como Mobile Engagement en su aplicación y no sería inusual porque, aunque hay cierta superposición en los datos recopilados, el foco principal de los productos es diferente y ayudan a lograr objetivos diferentes.  
2. [Application Insights](../application-insights/app-insights-overview.md) Si la aplicación móvil tiene un servidor, usará Application Insights para supervisar el servidor web de la aplicación, pero para las aplicaciones móviles de cliente debe usar HockeyApp. 
3. [Centros de notificaciones](https://azure.microsoft.com/services/notification-hubs/) proporcionan un servicio ligero en el sentido de que no es necesario integrar un SDK en la aplicación móvil y puede tener un control total de la aplicación móvil porque permite enviar notificaciones push con funcionalidades básicas de etiquetado. Esto es fantástico para los propietarios de aplicaciones que se preocupan menos por la segmentación y más por enviar y comunicar al instante la información a los usuarios de su aplicación (difusión a un público grande). 
   
   Tenga en cuenta que, aquí, el foco está en el envío rapidísimo de notificaciones con funcionalidades de segmentación básicas. 

Observemos algunos escenarios:

1. Tim forma parte del equipo de marketing digital de la tienda de Adventure Works, que publica aplicaciones móviles para usuarios. El objetivo de Tim es asegurarse de que los usuarios que descargan sus aplicaciones móviles seguirán usándola y con frecuencia. Esto no solo aumenta la lealtad a la marca por parte de los usuarios de la aplicación, sino que también aumenta la monetización cuando los usuarios de la aplicación realizan compras mediante la aplicación móvil. Para ello, Tim tendrá que enviar notificaciones dirigidas a los usuarios de la aplicación, que les resulten útiles, para fomentar que abran la aplicación y vuelvan a consultarla con frecuencia. Aquí es cuando Mobile Engagement le resultará útil a Tim. 
2. Joann forma parte del equipo de desarrollo de aplicaciones móviles en Adventure Works y está buscando un producto que registre los detalles acerca de los bloqueos y las excepciones, y que obtenga telemetría sobre el rendimiento de una aplicación. HockeyApp puede ayudar en esto a Joann. Joann podría integrar en la misma aplicación HockeyApp para sus escenarios como desarrolladora y Azure Mobile Engagement para Tim para obtener lo mejor de ambas. 
3. Robin forma parte del equipo de desarrollo de aplicaciones móviles en la red Contoso News y lo que desea es enviar alertas de noticias a todos los usuarios sin demasiada segmentación en cuanto se desencadene la alerta de noticias. Para eso, a Robin le pueden resultar útiles los Centros de notificaciones. 
   No obstante, en este escenario es posible que, a medida que crezca la aplicación móvil, sea necesario realizar segmentaciones más complejas y obtener detalles acerca del comportamiento del usuario de la aplicación. En ese momento, Robin tendrá que evaluar Azure Mobile Engagement. 

Resumiendo, el propósito de Mobile Engagement no es simplemente recopilar los análisis, no es "otro producto de análisis de Microsoft". Se encarga de enviar notificaciones push dirigidas y, para ello, recopilamos datos de análisis del comportamiento, pero el foco sigue estando en el envío de notificaciones push con más sentido para los usuarios de la aplicación, para que no lo consideren como correo no deseado. 

Para obtener más detalles, eche un vistazo a este [corto vídeo](mobile-engagement-overview.md) donde se resume Mobile Engagement. 

