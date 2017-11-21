---
title: "Administración del costo de la retención de datos en Azure Log Analytics | Microsoft Docs"
description: "Obtenga información sobre cómo cambiar la directiva de retención de datos y el plan de precios para el área de trabajo de Log Analytics en Azure Portal."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 35064c792b72222d59b1d3f0913a92a4a2b34612
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="manage-cost-of-data-retention-with-your-log-analytics-workspace"></a>Administración del costo de retención de datos con el área de trabajo de Log Analytics
Al suscribirse a Log Analytics, según el plan que seleccione, hay un límite de cuánto tiempo se almacenan los datos generados por los orígenes conectados en el área de trabajo.  Este artículo resalta las consideraciones que pueden influir en los costos de retención de estos datos para distintos períodos y cómo configurar ese límite.   

Puesto que Log Analytics puede consumir grandes cantidades de datos desde orígenes locales, entornos en la nube e híbridos, el costo de almacenamiento de esos datos durante un período puede ser considerables dependiendo de los factores siguientes:

* Número de sistemas, componentes de la infraestructura, recursos en la nube, etc. desde los que se efectúa la recopilación
* Tipo de datos creados por el origen, como las colas de mensajes, los registros, los eventos, los datos relacionados con la seguridad o las métricas de rendimiento
* Volumen de datos generado por estos orígenes 
* Número de solución de administración habilitado, el origen de datos y la frecuencia de recopilación

> [!NOTE]
> Consulte la documentación de cada solución, ya que proporciona una estimación de la cantidad de datos que recopila.   

Si se encuentra en un plan *gratuito*, los datos están limitados a una retención de siete días.  En el nivel *independiente* o *de pago*, los datos recopilados están disponibles durante los últimos 31 días.  

Cuando use el plan *gratuito*, si observa que supera de forma uniforme las cantidades permitidas, puede cambiar el área de trabajo a un plan de pago para recopilar datos más allá de este límite. 

> [!NOTE]
> Pueden aplicarse cargos si elige seleccionar un período de retención mayor para el nivel de pago. Puede cambiar el tipo de plan en cualquier momento. Para obtener más información sobre los precios, vea la [información sobre los precios](https://azure.microsoft.com/pricing/details/log-analytics/). 

## <a name="change-the-data-retention-period"></a>Cambio del período de retención de datos 

1. Inicie sesión en el [Portal de Azure](http://portal.azure.com). 
2. Haga clic en **Más servicios** en la esquina inferior izquierda. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.
3. En el panel de suscripciones de Log Analytics, seleccione el área de trabajo que modificar en la lista.
4. En la página de área de trabajo, haga clic en **Retención** desde el panel izquierdo.
5. En el panel de retención del área de trabajo, mueva el control deslizante para aumentar o disminuir el número de días y, luego, haga clic en **Guardar**.  Si el nivel es *gratuito*, no podrá modificar el período de retención de datos y tendrá que actualizar al nivel de pago para controlar esta configuración.<br><br> ![Cambio de la configuración de retención de datos del área de trabajo](media/log-analytics-manage-cost/manage-cost-change-retention.png)

## <a name="next-steps"></a>Pasos siguientes  

Para determinar la cantidad de datos recopilada, qué orígenes se envían y los diferentes tipos de datos enviados para ayudar a administrar el consumo y el costo, vea [Análisis del uso de datos en Log Analytics](log-analytics-usage.md).