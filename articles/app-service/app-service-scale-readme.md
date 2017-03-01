---
title: 'Servicio de aplicaciones de Azure: escalado de aplicaciones de Servicio de aplicaciones'
description: "Aprenda los pormenores del escalado de una aplicación en Servicio de aplicaciones."
keywords: servicio de aplicaciones, servicio de aplicaciones de azure, escala, escalable, plan del servicio de aplicaciones, costo del servicio de aplicaciones
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: f403c971-4450-432b-8cea-3eeb426c0147
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ec043a9e01aca2e843e50cef8b90328ba1939ea5
ms.lasthandoff: 11/17/2016


---
# <a name="azure-app-service-scaling-app-service-applications"></a>Servicio de aplicaciones de Azure: escalado de aplicaciones de Servicio de aplicaciones
Las aplicaciones hospedadas en el Servicio de aplicaciones de Azure pueden [alcanzar una escala enorme](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/).
Sin embargo, el escalado de una aplicación es un problema complejo que no tiene una solución de "talla única". Para escalar correctamente su aplicación, tenga en cuenta tres áreas clave que contribuirán a su éxito:

1. Comprensión de la arquitectura de la aplicación y sus puntos débiles.
   * ¿Se trata de una aplicación con estado? ¿O sin estado?
   * ¿Cuáles son los componentes de la aplicación?
     * ¿Dónde se encuentran los cuellos de botella en la aplicación?
   * Cuando se aplica carga a la aplicación, ¿qué será lo primero en dejar de funcionar?
2. Comprensión de los requisitos de carga y rendimiento esperados.
   * ¿Se necesita que la aplicación sirva a mil usuarios? ¿O a un millón?
   * ¿Procederá el tráfico de una sola ubicación geográfica o será global?
   * ¿Hay variaciones estacionales? ¿Picos de tráfico?
   * ¿Con qué rapidez debe responder la aplicación? ¿Un segundo? ¿Un milisegundo?
3. Comprensión y aprovechamiento correcto de la plataforma que hospeda su aplicación.
   * ¿Qué características debo aprovechar para lograr mis objetivos de escala?

Esta sección lo ayudará a comprender todos los factores y a diseñar una estrategia que aproveche las características necesarias de App Service para lograr sus objetivos de escalabilidad.

[!INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]


