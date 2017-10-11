---
title: "Introducción al escalado automático mediante métricas personalizadas en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo escalar recursos mediante métricas personalizadas en Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: ancav
ms.openlocfilehash: de8f7acadc282e4b81c657b1723f00fd3e5fd4f2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Introducción al escalado automático mediante métricas personalizadas en Azure
En este artículo se describe cómo escalar el recurso mediante una métrica personalizada en Azure Portal.

El escalado automático de Azure Monitor solo se aplica a Conjuntos de escalado de máquinas virtuales, Cloud Services, planes de App Service y App Service Environment. 

# <a name="lets-get-started"></a>Introducción
En este artículo se presupone que tiene una aplicación web con Application Insights configurado. Si aún no la tiene, puede [configurar Application Insights para el sitio web ASP.NET][1].

- Abra [Azure Portal][2].
- Haga clic en el icono de Azure Monitor en el panel de navegación izquierdo.
  ![Inicio de Azure Monitor][3]
- Haga clic en la opción Escalado automático para ver todos los recursos a los que se aplica el escalado automático, junto con su estado de escalado automático actual ![Detección de escalado automático en Azure Monitor][4]
- Abra la hoja "Escalado automático" en Azure Monitor y seleccione un recurso que desee escalar.
> Nota: En los pasos siguientes se usa un plan de App Service asociado con una aplicación web con Application Insights configurado.
- En la hoja de configuración de escalado del recurso, tenga en cuenta que el recuento de la instancia actual es 1. Haga clic en "Enable autoscale" (Habilitar escalado automático).
  ![Configuración de escalado para la nueva aplicación web][5]
- Proporcione un nombre para la configuración de escalado y haga clic en "Agregar una regla". Tenga en cuenta que las opciones de la regla de escalado se abren como un panel Contexto en el lado derecho. De forma predeterminada, establece la opción para escalar el recuento de instancias en 1 si el porcentaje de CPU del recurso supera el 70 %. Cambie el origen de métricas en la parte superior a "Application Insights", seleccione el recurso de Application Insights en el menú desplegable "Recurso" y luego seleccione la métrica personalizada en la que quiere basar el escalado.
  ![Escalar por métrica personalizada][6]
- De forma similar al paso anterior, agregue una regla de escalado que reduzca horizontalmente y disminuya el recuento de escala en 1 si la métrica personalizada está por debajo del umbral.
  ![Escala en función de la CPU][7]
- Establezca los límites de instancias. Por ejemplo, si desea escalar entre 2-5 instancias en función de las fluctuaciones de la métrica personalizada, establezca el "mínimo" en "2", el "máximo" en "5" y el "valor predeterminado" en "2".
> Nota: En caso de que haya algún problema al leer las métricas de recursos y la capacidad actual sea inferior a la predeterminada, el escalado automático escalará horizontalmente al valor predeterminado a fin de garantizar la disponibilidad del recurso. Si la capacidad actual ya es mayor que la predeterminada, el escalado automático no reducirá horizontalmente.
- Haga clic en "Guardar".

¡Enhorabuena! Ya ha establecido correctamente la configuración del escalado para escalar automáticamente la aplicación web en función de una métrica personalizada.

> Nota: Los mismos pasos son aplicables para empezar a trabajar con un rol de servicio en la nube o VMSS.

<!--Reference-->
[1]: https://docs.microsoft.com/en-us/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png
