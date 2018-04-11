---
title: Detalles del plan de precios Enterprise heredado para Azure Application Insights | Microsoft Docs
description: Administre los volúmenes de telemetría y supervise los costos en Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>Detalles del plan Enterprise

Application Insights tiene dos planes de precios. El plan predeterminado se denomina [Básico](app-insights-pricing.md) e incluye las mismas características que el plan Enterprise sin costo adicional, facturándose principalmente en función del volumen de ingestión de datos. Si usa Operations Management Suite, debería elegir el plan Enterprise, en el que se aplica un cargo por nodo junto con las concesiones de datos diarias y, posteriormente, se aplica otro cargo por los datos ingeridos por encima de la concesión incluida.

Consulte en la [página Precios de Application Insights](http://azure.microsoft.com/pricing/details/application-insights/) los precios actuales en su moneda y región.

## <a name="heres-how-the-enterprise-plan-works"></a>Funcionamiento del plan Enterprise

* En dicho plan, se paga por nodo que envíe telemetría desde cualquier aplicación.
 * Un *nodo* es un servidor físico o virtual o una instancia de rol de plataforma como servicio que hospeda su aplicación.
 * Los equipos de desarrollo, los exploradores cliente y los dispositivos móviles no se cuentan como nodos.
 * Si su aplicación presenta varios componentes que envían telemetría, como un servicio web y un trabajo de back-end, se cuentan por separado.
 * Los datos de [Live Metrics Stream](app-insights-live-stream.md) no cuentan para calcular los precios.* En una suscripción, sus cargos son por nodo, no por aplicación. Si tiene cinco nodos que envían telemetría de 12 aplicaciones, se le cobrará por 5 nodos.
* Aunque los cargos se presupuestan mensualmente, solo se le cobrarán las horas en las que un nodo envíe telemetría de una aplicación. El cargo por hora es el cargo mensual presupuestado partido por 744 (el número de horas en un mes de 31 días).
* Se proporciona una asignación de volumen de datos de 200 MB por día para cada nodo detectado (con una granularidad de horas). Las asignaciones de datos no utilizadas no se guardarán de un día para otro.
 * Si elige el plan Enterprise, cada suscripción recibe una asignación de datos diaria en función del número de nodos que envíen telemetría a los recursos de Application Insights de esa suscripción. De modo que, si tiene 5 nodos enviando datos todos los días, tendrá una asignación global de 1 GB para todos los recursos de Application Insights de esa suscripción. No importa si algunos nodos envían más datos que otros, porque los datos incluidos se comparten entre todos los nodos. Si, un día determinado, los recursos de Application Insights reciben más datos de los que se incluyen en la asignación de datos diaria para esta suscripción, se aplicarán los cargos de datos por encima del límite por cada GB. 
 * La asignación de datos diaria se calcula como el número de horas del día (de acuerdo con UTC) que cada nodo envía telemetría dividido entre 24 por 200 MB. Es decir, si tiene 4 nodos enviando telemetría durante 15 de las 24 horas del día, los datos incluidos para ese día serán ((4 x 15) / 24) x 200 MB = 500 MB. A un precio de 2,30 USD por GB en el caso de uso por encima del límite de datos, el cargo sería de 1,15 USB si los nodos envían 1 GB de datos ese día.
 * La asignación diaria del plan Enterprise no se comparte con las aplicaciones para las que haya elegido la opción Básica y que las asignaciones no utilizadas no se guardan de un día a otro. 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>Estos son algunos de los ejemplos de cómo determinar el número concreto de nodos

| Escenario                               | Número de nodos total diario |
|:---------------------------------------|:----------------:|
| 1 aplicación está usando 3 instancias de Azure App Service y 1 servidor virtual. | 4 |
| 3 aplicaciones que se ejecutan en 2 máquinas virtuales, y los recursos de Application Insights de estas aplicaciones se encuentran en la misma suscripción y en el plan Enterprise. | 2 | 
| 4 aplicaciones cuyos recursos de Application Insights se encuentran en la misma suscripción. Cada aplicación ejecuta 2 instancias durante 16 horas de poca actividad y 4 durante 8 horas de máxima actividad. | 13,33 | 
| Servicios en la nube con 1 rol de trabajo y 1 rol web; cada uno ejecuta 2 instancias. | 4 | 
| Clúster de Service Fabric de 5 nodos que ejecuta 50 microservicios; cada uno de estos últimos ejecuta 3 instancias. | 5|

* El comportamiento concreto del recuento de nodos depende del SDK de Application Insights que esté usando su aplicación. 
  * En la versión 2.2 del SDK y posteriores, tanto el [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) como el [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) de Application Insights notificará a cada aplicación host como un nodo. Por ejemplo, el nombre del equipo del servidor físico y los hosts de las máquinas virtuales o el nombre de la instancia, en el caso de servicios en la nube.  Las aplicaciones que usan [.NET Core](https://dotnet.github.io/) y el Core SDK de Application Insights constituyen la única excepción. En este caso, solo se indicará un nodo para todos los hosts porque el nombre de host no se encuentra disponible. 
  * Para las versiones anteriores del SDK, el [SDK Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comportará del mismo modo que las versiones más nuevas. Sin embargo, [SDK Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) solo notificará un nodo, con independencia del número real de hosts de la aplicación. 
  * Si su aplicación está usando el SDK para establecer roleInstance en un valor personalizado, ese mismo valor se usará para determinar el recuento de nodos de forma predeterminada. 
  * Si utiliza una nueva versión del SDK con una aplicación que se ejecuta desde equipos cliente o dispositivos móviles, es posible que el recuento de nodos devuelva un número muy grande (del gran número de equipos cliente o dispositivos móviles). 
