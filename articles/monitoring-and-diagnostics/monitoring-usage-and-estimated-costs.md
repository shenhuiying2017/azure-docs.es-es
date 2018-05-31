---
title: Supervisión del uso y costos estimados en Azure Monitor | Microsoft Docs
description: Introducción al proceso de uso de la página Uso y costos estimados de Azure Monitor
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: 6cc35697573ae2997f289f67c7867d9c522149be
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203784"
---
# <a name="monitoring-usage-and-estimated-costs"></a>Supervisión del uso y costos estimados

En el centro Monitor de Azure Portal, la página **Uso y costos estimados** explica el uso de características de supervisión principales como [alertas, métricas, notificaciones](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) y [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Para los clientes en los planes de precios disponibles antes de abril de 2018, esto también incluye el uso de Log Analytics adquirido a través de la oferta Insights y Analytics.

En esta página, los usuarios pueden ver el uso de sus recursos durante los últimos 31 días, agregados por suscripción. La obtención de detalles muestra las tendencias de uso durante el período de 31 días. Es necesario que una gran cantidad de datos se reúna para este cálculo, por lo que agradecemos su paciencia mientras se carga la página.

En este ejemplo se muestra la supervisión del uso y una estimación de los costos resultantes:

![Captura de pantalla de la página Uso y costos estimados del portal](./media/monitoring-usage-and-estimated-costs/001.png)

Seleccione el vínculo de la columna de uso mensual para abrir un gráfico que muestra las tendencias de uso en el último período de 31 días:

![Captura de la pantalla del gráfico de barras Incluido por nodo](./media/monitoring-usage-and-estimated-costs/002.png)

Este es otro resumen similar del uso y los costos. En este ejemplo se muestra una suscripción en el nuevo modelo de precios basado en el consumo de abril de 2018. Tenga en cuenta la falta de facturación basada en nodos. La ingesta de datos y la retención para Log Analytics y Application Insights ahora se notifican en un nuevo medidor común.

![Captura de pantalla de la página Uso y costos estimados del portal: precios de abril de 2018](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nuevo modelo de precios

En abril de 2018 se lanzó un [nuevo modelo de precios de supervisión](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Con características sencillas y en la nube y precios basados en el consumo. Solo paga por lo que usa, sin compromisos basados en nodos. Hay detalles del nuevo modelo de precios disponibles para [alertas, métricas y notificaciones](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) y [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Para los clientes que se incorporan a Log Analytics o Application Insights después del 2 de abril de 2018, el nuevo modelo de precios es la única opción. Para los clientes que ya utilizan estos servicios, el traslado al nuevo modelo de precios es opcional.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Evaluación del impacto del nuevo modelo de precios
El nuevo modelo de precios tendrá efectos diferentes en cada cliente en función de sus patrones de uso de la supervisión. Para los clientes que usaban Log Analytics o Application Insights antes del 2 de abril de 2018, la página **Uso y costos estimados** en Azure Monitor estima cualquier cambio en los costos si se trasladan al nuevo modelo de precios. Proporciona la forma de trasladar una suscripción al nuevo modelo. Para la mayoría de los clientes, el nuevo modelo de precios será ventajoso. Para clientes con patrones de uso de datos especialmente elevados o en regiones de mayor costo, podría no ser el caso.

Para ver una estimación de los costos de las suscripciones que eligió en la página **Uso y costos estimados**, seleccione el banner azul situado cerca de la parte superior de la página. Es mejor realizar este proceso con una suscripción cada vez, ya que es el nivel en el que se puede adoptar el nuevo modelo de precios.

![Captura de pantalla de la página Uso y costo estimado de Azure Monitor en el nuevo modelo de precios](./media/monitoring-usage-and-estimated-costs/004.png)

En la nueva página se muestra una versión similar de la página anterior con un banner verde:

![Captura de pantalla de la página Uso y costo estimado de Azure Monitor en el modelo de precios actual](./media/monitoring-usage-and-estimated-costs/005.png)

En la página también se muestra un conjunto diferente de medidores que se corresponden con el nuevo modelo de precios. Esta lista es un ejemplo:

- Insights y Analytics\Uso por encima del límite por nodo
- Insights y Analytics\Incluido por nodo
- Application Insights\Datos por encima del límite básico
- Application Insights\Datos incluidos

El nuevo modelo de precios no tiene asignaciones de datos incluidas basadas en nodos. Por lo tanto, estos medidores de ingesta de datos se combinan en un nuevo medidor de ingesta de datos común llamado **Shared Services\Data Ingestion**. 

Hay otro cambio en los datos ingeridos en Log Analytics o Application Insights en regiones con costos más elevados. Los datos para estas regiones con costos elevados se mostrarán con los nuevos medidores regionales. Un ejemplo es **Ingesta de datos (Centro-oeste de EE. UU.)**.

> [!NOTE]
> Los costos estimados según la suscripción no gestionan los derechos por nodo del nivel de cuenta de la suscripción de Operations Management Suite (OMS). Consulte con su representante de cuentas para obtener una explicación más detallada del nuevo modelo de precios en este caso.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nuevo modelo de precios y derechos de suscripción de Operations Management Suite

Los clientes que compraron Microsoft Operations Management Suite E1 y E2 son aptos para los derechos de ingesta de datos por nodo para [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) y [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing#the-price-plans). Para recibir estos derechos para las áreas de trabajo de Log Analytics o los recursos de Application Insights en una suscripción determinada: 

- El modelo de precios de la suscripción debe ser igual que el modelo anterior a abril de 2018.
- Las áreas de trabajo de Log Analytics deben usar el plan de tarifa "por nodo (OMS)".
- Los recursos de Application Insights deben utilizar el plan de tarifa "Enterprise".

En función del número de nodos del conjunto de aplicaciones que adquiriera la organización, puede ser de utilidad mover alguna suscripción al nuevo modelo de precios, pero le recomendamos que antes lo piense detenidamente. En general, es aconsejable quedarse en el modelo anterior a abril de 2018, tal como se describió anteriormente.

> [!WARNING]
> Si la organización compró Microsoft Operations Management Suite E1 y E2, es mejor mantener las suscripciones según el modelo de precios anterior a abril de 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Cambios en el traslado al nuevo modelo de precios

El nuevo modelo de precios simplifica las opciones de precios que ofrecen Log Analytics y Application Insights en un solo nivel (o plan). Si lleva una suscripción al nuevo modelo de precios, podrá realizar lo siguiente:

- Cambiar el plan de tarifa de cada instancia de Log Analytics a un nuevo nivel por GB (denominado "pergb2018" en Azure Resource Manager).
- Cambiar cualquier recurso de Application Insights del plan Enterprise al plan Básico.

La estimación de costos muestra los efectos de estos cambios.

> [!WARNING]
> Tenga en cuenta lo siguiente si usa Azure Resource Manager o PowerShell para implementar [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) o [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) en una suscripción que haya trasladado al nuevo modelo de precios. Si especifica un plan de tarifa que no sea "pergb2018" para Log Analytics, o "Básico" para Application Insights, no se producirá un error en la implementación aunque haya especificado un plan de tarifa no válido; es más, la implementación se realizará correctamente, **pero usará solo el plan de tarifa válido** (esto no corresponde al plan Gratuito de Log Analytics, donde se genera un mensaje de plan de tarifa no válido).
>

## <a name="moving-to-the-new-pricing-model"></a>Traslado al nuevo modelo de precios

Si ha decidido adoptar el nuevo modelo de precios para una suscripción, seleccione la opción **Selección del modelo de precios** en la parte superior de la página **Uso y costos estimados**:

![Captura de pantalla de la página Uso y costo estimado de Azure Monitor en el nuevo modelo de precios](./media/monitoring-usage-and-estimated-costs/006.png)

La página **Selección del modelo de precios** se abrirá. Muestra una lista de cada una de las suscripciones que vio en la página anterior:

![Captura de pantalla de la selección del modelo de precios](./media/monitoring-usage-and-estimated-costs/007.png)

Para trasladar una suscripción al nuevo modelo de precios, simplemente active la casilla y seleccione **Guardar**. Puede volver al modelo de precios anterior de la misma manera. Tenga en cuenta que se requieren permisos de propietario o colaborador de la suscripción para cambiar el modelo de precios.
