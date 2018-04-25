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
ms.openlocfilehash: ce295c449b01de4fa99df9198805a6b0727c0d18
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Supervisión del uso y costos estimados

En el centro Monitor de Azure Portal, la página **Uso y costos estimados** está diseñada para ayudar a entender el uso de características de supervisión principales como [alertas, métricas, notificaciones](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) y [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Para los clientes en los planes de precios disponibles antes de abril de 2018, esto también incluye el uso de Log Analytics adquirido a través de la oferta Insights y Analytics.

En esta página, los usuarios pueden ver el uso de estos recursos durante los últimos 31 días, agregado por suscripción, con obtención de detalles para ver las tendencias de uso durante este período. Hay una gran cantidad de datos necesarios para reunir y realizar este cálculo, por lo que agradecemos su paciencia mientras se carga la página.
Este es un ejemplo que muestra la supervisión del uso y una estimación de los costos resultantes:

![Captura de pantalla de la página Uso y costos estimados del portal](./media/monitoring-usage-and-estimated-costs/001.png)

Al hacer clic en el vínculo de la columna de uso mensual, se abrirá un gráfico que muestra las tendencias de uso en el último período de 31 días:

![Captura de la pantalla Incluido por nodo de 671,47 GB](./media/monitoring-usage-and-estimated-costs/002.png)

Este es otro resumen de uso y costo similar, en este caso de una suscripción del nuevo modelo de precios basado en el consumo desde abril de 2018. Observe la ausencia de facturación basada en nodos y que la ingesta de datos y la retención para Log Analytics y Application Insights ahora se notifican en un nuevo medidor común.

![Captura de pantalla de la página Uso y costos estimados del portal](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nuevo modelo de precios

En abril de 2018 se ha liberado un nuevo modelo de precios de supervisión.  Con características sencillas y en la nube y precios basados en el consumo. Solo paga por lo que usa, sin compromisos basados en nodos. Hay detalles del nuevo modelo de precios disponibles para [alertas, métricas y notificaciones](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) y [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

Para los clientes que se incorporan a Log Analytics o Application Insights después del 2 de abril de 2018, el nuevo modelo de precios es la única opción. Para los clientes que ya han estado utilizando estos servicios, el traslado al nuevo modelo de precios es opcional.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Evaluación del impacto del nuevo modelo de precios

El nuevo modelo de precios tendrá efectos diferentes para cada cliente en función de sus patrones de uso de la supervisión. Para los clientes que ya usan Log Analytics o Application Insights antes del 2 de abril de 2018, la página **Uso y costo estimado** de Azure Monitor proporciona una manera de calcular los cambios en los costos si se traslada al nuevo modelo de precios y proporciona también el modo para trasladar una suscripción al nuevo modelo. Para la mayoría de los clientes, el nuevo modelo de precios será ventajoso, pero para clientes con patrones de uso de datos especialmente elevados o en regiones de mayor costo, podría no ser el caso.

Para ver una estimación de los costos de las suscripciones que ha seleccionado en la página **Uso y costos estimados**, haga clic en el banner azul situado cerca de la parte superior de la página. Es mejor realizar este proceso con una suscripción cada vez, ya que es el nivel en el que se puede adoptar el nuevo modelo de precios.

![Captura de pantalla de la selección de modelo de precios](./media/monitoring-usage-and-estimated-costs/004.png)

Ahora verá una versión similar de esta página con un banner verde:

![Captura de pantalla de la selección de modelo de precios](./media/monitoring-usage-and-estimated-costs/005.png)

Aquí verá un conjunto diferente de medidores: los medidores que se corresponden con el nuevo modelo de precios. Por ejemplo, los medidores de ingesta de datos como

1. Insights y Analytics\Uso por encima del límite por nodo
2. Insights y Analytics\Incluido por nodo
3. Application Insights\Datos por encima del límite básico
4. Application Insights\Datos incluidos

se combinan para crear un nuevo medidor de ingesta de datos común llamado **Shared Services\Data Ingestion** ya que el nuevo modelo de precios no incluye derechos de datos según los nodos.

Otro cambio que verá es que los datos ingeridos en Log Analytics o Application Insights en regiones con mayor costo se mostrarán con los nuevos medidores regionales para reflejarlo correctamente, por ejemplo **"Ingesta de datos (Centro-oeste de EE. UU.)"**.

> [!NOTE]
> Los costos estimados según la suscripción no gestionan los derechos por nodo del nivel de cuenta de la suscripción de Operations Management Suite (OMS). Consulte con su representante de cuentas para obtener una explicación más detallada del nuevo modelo de precios en este caso.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nuevo modelo de precios y derechos de suscripción de Operations Management Suite

Los clientes que compraron Microsoft Operations Management Suite E1 y E2 son aptos para los derechos de ingesta de datos por nodo para [Log Analytics](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) y [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans). Para recibir estos derechos para las áreas de trabajo de Log Analytics o los recursos de Application Insights en una suscripción determinada, el modelo de precios de esta debe ser de antes de abril de 2018, ya que son los que ofrecen el plan de tarifa por nodo (OMS) de Log Analytics y Enterprise de Application Insights. En función del número de nodos de Suite que adquiriera la organización, mover alguna suscripción al nuevo modelo de precios puede ser ventajoso, pero debe considerarse con atención. 

## <a name="changes-when-moving-to-the-new-pricing-model"></a>Cambios en el traslado al nuevo modelo de precios

Si mueve una suscripción al nuevo modelo de precios, cambiará el plan de tarifa de Log Analytics a un nuevo plan por GB y trasladará los existentes (llamado "pergb2018" en Azure Resource Manager). Este traslado también cambiará cualquier recurso de Application Insights en el plan Empresarial al plan Básico. Los efectos de esto se muestran en la estimación de costos que se ha descrito anteriormente. 

## <a name="moving-to-the-new-pricing-model"></a>Traslado al nuevo modelo de precios

Si ha decidido adoptar el nuevo modelo de precios para una suscripción, haga clic en la opción **Selección del modelo de precios** en la parte superior de la página **Uso y costos estimados**:

![Captura de pantalla de la página Uso y costo estimado de Azure Monitor en el nuevo modelo de precios](./media/monitoring-usage-and-estimated-costs/006.png)

Se abrirá la página **Selección del modelo de precios**, con una lista de cada una de las suscripciones que estaba viendo en la página anterior:

![Captura de pantalla de la selección del modelo de precios](./media/monitoring-usage-and-estimated-costs/007.png)

Para trasladar una suscripción al nuevo modelo de precios, simplemente active la casilla y haga clic en **Guardar**.  Puede volver al modelo de precios anterior de la misma manera. Tenga en cuenta que se requieren permisos de propietario o colaborador de la suscripción para cambiar el modelo de precios.
