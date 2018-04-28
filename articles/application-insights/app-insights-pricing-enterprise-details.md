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
ms.openlocfilehash: 6df013506e4541fee7850850776d26e5c69a799d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="enterprise-plan-details"></a>Detalles del plan Enterprise

Azure Application Insights dispone de dos planes de precios: Básico y Enterprise. El plan de precios [Básico](app-insights-pricing.md) es el predeterminado. Incluye todas las características del plan Enterprise sin ningún costo adicional. En el plan Básico, la facturación se realiza principalmente en función del volumen de datos ingerido. 

En el plan Enterprise, se cobra por nodo y cada nodo recibe una asignación de datos diaria. En el plan de precios Enterprise, se cobran los datos ingeridos que sobrepasan la asignación incluida. Si usa Operations Management Suite, debería elegir el plan Enterprise. 

Para ver los precios vigentes actualmente en su moneda y región, consulte [Precios de Application Insights](http://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-the-enterprise-plan-works"></a>Funcionamiento del plan Enterprise

* En el plan Enterprise, se paga por cada nodo que envíe datos de telemetría desde cualquier aplicación.
 * Un *nodo* es un servidor físico o virtual o una instancia del rol de plataforma como servicio que hospeda la aplicación.
 * Los equipos de desarrollo, los exploradores cliente y los dispositivos móviles no se cuentan como nodos.
 * Si la aplicación contiene varios componentes que envían datos telemetría, como un servicio web y un trabajo de back-end, estos componentes se cuentan por separado.
 * Los datos de la [secuencia de métricas en directo](app-insights-live-stream.md) no cuentan para calcular los precios. En una suscripción, se cobra por nodo, no por aplicación. Si tiene cinco nodos que envían telemetría desde 12 aplicaciones, se le cobrarán 5 nodos.
* Aunque los cargos se presupuestan mensualmente, solo se le cobrarán las horas en las que un nodo envíe telemetría de una aplicación. El cargo por hora es el cargo mensual presupuestado dividido entre 744 (el número de horas que tiene un mes de 31 días).
* Cada día, se asigna un volumen de datos de 200 MB por cada nodo detectado (con una granularidad por hora). Las asignaciones de datos que no se utilizan no se guardan de un día para otro.
 * Si elige el plan de precios Enterprise, cada suscripción recibirá una asignación de datos diaria en función del número de nodos que envíen datos de telemetría a los recursos de Application Insights de esa suscripción. Por tanto, si tiene cinco nodos que envían datos todo el día, tendrá una asignación global de 1 GB para todos los recursos de Application Insights de esa suscripción. No importa si algunos nodos envían más datos que otros, ya que los datos incluidos se comparten entre todos los nodos. Si, un día determinado, los recursos de Application Insights reciben más datos de los que se incluyen en la asignación de datos diaria para esta suscripción, se aplicarán los cargos de datos por encima del límite por cada GB. 
 * La asignación de datos diaria se calcula como el número de horas del día (de acuerdo con UTC) que cada nodo envía datos de telemetría dividido entre 24 y multiplicado por 200 MB. Es decir, si tiene cuatro nodos que envían datos de telemetría durante 15 de las 24 horas del día, los datos incluidos para ese día serán ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. A un precio de 2,30 USD por GB en el caso de uso por encima del límite de datos, el cargo sería de 1,15 USB si los nodos envían 1 GB de datos ese día.
 * La asignación diaria del plan Enterprise no se comparte con las aplicaciones que tienen el plan Básico. La asignación que no se ha utilizado no se guarda de un día para otro. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>Ejemplos acerca de cómo determinar el número de nodos

| Escenario                               | Número de nodos total diario |
|:---------------------------------------|:----------------:|
| 1 aplicación utiliza 3 instancias de Azure App Service y 1 servidor virtual | 4 |
| 3 aplicaciones se ejecutan en 2 máquinas virtuales; los recursos de Application Insights de estas aplicaciones están en la misma suscripción y en el plan Enterprise | 2 | 
| 4 aplicaciones cuyos recursos de Applications Insights están en la misma suscripción; cada aplicación ejecuta 2 instancias durante 16 horas de poca actividad y 4 instancias durante 8 horas punta | 13,33 | 
| Servicios en la nube con 1 rol de trabajo y 1 rol web; cada uno ejecuta 2 instancias. | 4 | 
| Un clúster de Azure Service Fabric con 5 nodos ejecuta 50 microservicios; cada microservicio ejecuta 3 instancias | 5|

* El número preciso de nodos depende del SDK de Application Insights que utilice la aplicación. 
  * En las versiones 2.2 y posteriores del SDK, tanto el [SDK básico](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) como el [SDK web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) de Application Insights cuentan cada host de aplicación como un nodo. Algunos ejemplos son el nombre de equipo del servidor físico y los hosts de máquina virtual, o el nombre de instancia de los servicios en la nube.  La única excepción es una aplicación que solamente usa [.NET Core](https://dotnet.github.io/) y el SDK básico de Application Insights. En ese caso, solo se cuenta un nodo para todos los hosts porque el nombre de host no está disponible. 
  * En las versiones anteriores del SDK, el [SDK web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comporta del mismo modo que las versiones más nuevas. Sin embargo, el [SDK básico](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) solo cuenta un nodo, independientemente del número real de hosts de la aplicación. 
  * Si la aplicación utiliza el SDK para establecer **roleInstance** en un valor personalizado, ese mismo valor se usará de manera predeterminada para calcular el número de nodos. 
  * Si utiliza una nueva versión del SDK con una aplicación que se ejecuta desde equipos cliente o dispositivos móviles, es posible que el recuento de nodos devuelva un número muy grande (debido a la gran cantidad de equipos cliente o de dispositivos móviles). 
