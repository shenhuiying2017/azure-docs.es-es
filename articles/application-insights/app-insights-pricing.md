---
title: Administración de precios y volúmenes de datos de Azure Application Insights | Microsoft Docs
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
ms.openlocfilehash: 22c8616c1585e3f728a03a794c527cb34fc0c4eb
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32771405"
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Administración de precios y volúmenes de datos de Application Insights

Los precios para [Azure Application Insights][start] se basan en el volumen de datos por aplicación. Cada recurso de Application Insights se cobra como un servicio independiente y contribuye a la factura de la suscripción a Azure.

Application Insights dispone de dos planes de precios: Básico y Enterprise. El plan de precios Básico es el predeterminado. Incluye todas las características del plan Enterprise sin ningún costo adicional. En el plan Básico, la facturación se realiza principalmente en función del volumen de datos ingerido. 

En el plan Enterprise, se cobra por nodo y cada nodo recibe una asignación de datos diaria. En el plan de precios Enterprise, se cobran los datos ingeridos que sobrepasan la asignación incluida. Si usa Operations Management Suite, debería elegir el plan Enterprise. 

Si tiene preguntas sobre cómo funcionan los precios para Application Insights, no dude en publicar una pregunta en nuestro [foro](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Panes de tarifa

Para ver los precios vigentes actualmente en su moneda y región, consulte [Precios de Application Insights][pricing].

> [!NOTE]
> En abril de 2018, [introdujimos](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) un nuevo modelo de precios para la supervisión de Azure. Este modelo adopta un modelo de "pago por uso" sencillo en toda la cartera de servicios de supervisión. Más información sobre el [nuevo modelo de precios](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), cómo [valorar el impacto de pasar a este modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) en función de los patrones de uso y [cómo participar en el nuevo modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Plan Básico

El plan Básico es el predeterminado cuando se crea un nuevo recurso de Application Insights. El plan Básico es óptimo para todos los clientes menos aquellos con una suscripción a Operations Management Suite.

* En el plan Básico, se le cobra por volumen de datos. El volumen de datos es el número de bytes de telemetría que recibe Application Insights. El volumen de datos se mide como el tamaño del paquete de datos JSON sin comprimir que recibe Application Insights de su aplicación. Para [los datos tabulares importados en Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), el volumen de datos se mide como el tamaño descomprimido de los archivos enviados a Application Insights.
* Los cargos por volumen de datos de la aplicación se notifican a partir de abril de 2018 en un nuevo medidor de facturación denominado **Ingesta de datos**. Este nuevo medidor se comparte a través de tecnologías como Application Insights y Log Analytics, y su actual nombre de servicio es **App Services** (pronto cambiará a **Log Analytics**). 
* Los datos de la [secuencia de métricas en directo](app-insights-live-stream.md) no cuentan para calcular los precios.
* La [exportación continua](app-insights-export-telemetry.md) y el [conector de Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) se encuentran disponibles sin ningún cargo adicional en el plan Básico desde abril de 2018.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Derechos de suscripción del plan Enterprise y Operations Management Suite

Como se [anunció anteriormente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), los clientes que compran Operations Management Suite E1 y E2 pueden obtener Application Insights Enterprise como componente adicional sin costo alguno. Específicamente, cada unidad de Operations Management Suite E1 y E2 incluye el derecho a usar un nodo del plan Enterprise de Application Insights. Cada nodo de Application Insights incluye hasta 200 MB de datos ingeridos por día (independientes de la ingesta de datos de Log Analytics), con la retención de datos de 90 días sin costo adicional alguno. El plan se describe con más detalle más adelante en el artículo. 

Dado que este plan solo es aplicable a clientes con una suscripción a Operations Management Suite, los clientes sin una suscripción no verán una opción para seleccionar este plan.

> [!NOTE]
> Para garantizar que tiene este derecho, sus recursos de Application Insights deben estar en el plan de precios Enterprise. Este derecho se aplica solo como nodos. Los recursos de Application Insights del plan Básico no consiguen ningún beneficio. Este derecho no es visible en los costos estimados que se muestran en el panel **Uso y costos estimados**. Además, si traslada una suscripción al nuevo modelo de precios de supervisión de Azure en abril de 2018, el plan Básico será el único plan disponible. El traslado de una suscripción al nuevo modelo de precios de supervisión de Azure no es aconsejable si tiene una suscripción a Operations Management Suite.

Para más información sobre el plan Enterprise, consulte [Detalles de precios de Enterprise](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Pruebas web de varios pasos

Las [pruebas web de varios pasos](app-insights-monitor-web-app-availability.md#multi-step-web-tests) conllevan un cargo adicional. Las pruebas web de varios pasos son pruebas web que realizan una secuencia de acciones.

No hay ningún cargo aparte para las *pruebas de ping* de una sola página. La telemetría de las pruebas de ping y de las de varios pasos se carga igual que el resto de la telemetría de su aplicación.

## <a name="review-pricing-plans-and-estimate-costs"></a>Revisión de los planes de precios y cálculo de costos

Application Insights permite entender fácilmente los planes de precios disponibles y qué costos se basarán probablemente en los patrones de uso reciente. Para comenzar, en Azure Portal, en el recurso de Application Insights, vaya al panel **Uso y costos estimados**:

![Elija el precio](./media/app-insights-pricing/pricing-001.png)

A. Revise el volumen de datos para el mes. Esto incluye todos los datos recibidos y retenidos (después de cualquier [muestreo](app-insights-sampling.md)) de las aplicaciones de servidor y cliente, y de las pruebas de disponibilidad.  
B. Se realiza un cargo adicional para las [pruebas web de varios pasos](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Esto no incluye pruebas de disponibilidad simples, que se incluyen en la carga de volumen de datos).  
C. Vea las tendencias de volumen de datos durante el mes anterior.  
D. Habilite el [muestreo](app-insights-sampling.md) de ingesta de datos.   
E. Establezca el límite de volumen de datos diario.  

Los cargos de Application Insights se agregarán a la factura de Azure. Puede ver los detalles de su factura de Azure en la sección de **facturación** de Azure Portal o en el [Portal de facturación de Azure](https://account.windowsazure.com/Subscriptions). 

![En el menú de la izquierda, seleccione Facturación](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Velocidad de datos
El volumen de datos que envía se limita de tres formas:

* **Muestreo**: puede usar el muestreo para reducir la cantidad de telemetría enviada desde las aplicaciones de servidor y de cliente, con mínima distorsión de las métricas. El muestreo es la principal herramienta que puede usar para ajustar la cantidad de datos que envía. Obtenga más información sobre las [características de muestreo](app-insights-sampling.md). 
* **Límite diario**: al crear un recurso de Application Insights en Azure Portal, el límite diario se establece en 100 GB/día. Al crear un recurso de Application Insights en Visual Studio, la capacidad predeterminada es pequeña (solo 32,3 MB/día). El límite diario predeterminado se establece para facilitar las pruebas. El objetivo es que el usuario aumente el límite diario antes de implementar la aplicación en producción. 

    El límite máximo es de 1,000 GB/día, a menos que solicite un máximo superior para una aplicación con mucho tráfico. 

    Tenga cuidado al establecer el límite diario. Su objetivo debe ser *no alcanzar nunca el límite diario*. Si lo alcanza, perderá datos durante el resto del día y no podrá supervisar su aplicación. Para cambiar el límite diario, use la opción **Límite de volumen diario**. Puede tener acceso a esta opción en el panel **Uso y costos estimados** (se describe con más detalle más adelante en el artículo).
    Hemos quitado la restricción en algunos tipos de suscripción con crédito que no se podía usar para Application Insights. Anteriormente, si la suscripción tenía un límite de gasto, el cuadro de diálogo de límite diario mostraba instrucciones sobre cómo quitarlo y habilitarlo para superar los 32,3 MB/día.
* **Limitación**: la limitación restringe la velocidad de datos a 32 000 eventos por segundo, promediados durante 1 minuto.

*¿Qué ocurre si mi aplicación supera el porcentaje de limitación?*

* El volumen de datos que su aplicación envía se evalúa cada minuto. Si se supera la tasa por segundo promediada por minuto, el servidor rechazará algunas solicitudes. El SDK almacena en búfer los datos y, a continuación, intenta volver a enviarlos. Extiende un aumento durante varios minutos. Si su aplicación envía de forma consistente un volumen de datos que supera la tasa de limitación, es posible que algunos se eliminen (los SDK de ASP.NET, Java y JavaScript intentan volver a enviarlos de esta manera; otros SDK pueden simplemente eliminar los datos limitados). Si se produce la limitación, una notificación de advertencia le indica que esto ha sucedido.

*¿Cómo puedo saber cuántos datos envía mi aplicación?*

Puede usar una de las siguientes opciones para ver cuántos datos envía su aplicación:

* Vaya al panel **Uso y costos estimados** para ver el gráfico de volumen de datos diario. 
* En el Explorador de métricas, agregue un nuevo gráfico. En la métrica del gráfico, seleccione **volumen de punto de datos**. Activa la **agrupación** y, a continuación, agrupe por **Tipo de datos**.

## <a name="reduce-your-data-rate"></a>Reduzca la velocidad de datos
Estas son algunas cosas que puede hacer para reducir el volumen de datos:

* Use el [Muestreo](app-insights-sampling.md). Esta tecnología reduce la velocidad de datos sin sesgar las métricas. No perderá la capacidad de navegar entre elementos relacionados en Search. En las aplicaciones de servidor, el muestreo funciona automáticamente.
* [Limite el número de llamadas AJAX que se pueden notificar](app-insights-javascript.md#detailed-configuration) en cada vista de página o desactive los informes de AJAX.
* [Edite ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) para desactivar los módulos de recopilación que no necesite. Por ejemplo, podría decidir que los contadores de rendimiento o datos de dependencia no son esenciales.
* Divida la telemetría entre claves de instrumentación independientes. 
* Métricas agregadas previamente. Si coloca llamadas a TrackMetric en su aplicación, puede reducir el tráfico mediante la sobrecarga que acepta el cálculo de la media y la desviación estándar de un lote de medidas. O bien, puede usar un [paquete de agregación previa](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Administración del volumen de datos diario máximo

Puede usar el límite de volumen diario para restringir los datos recopilados. Sin embargo, si se alcanza el límite, se producirá una pérdida de todas las telemetrías enviadas desde su aplicación durante el resto del día. No *es aconsejable* hacer que su aplicación alcance el límite diario. No puede realizar un seguimiento del estado y rendimiento de su aplicación una vez que alcance el límite diario.

En lugar de usar el límite de volumen diario, use el [muestreo](app-insights-sampling.md) para ajustar el volumen de datos al nivel que desee. A continuación, use el límite diario solo como "último recurso" en caso de que su aplicación empiece a enviar de forma inesperada volúmenes de telemetría mucho más altos.

Para cambiar el límite diario, en la sección de **configuración** del recurso de Application Insights, en el panel **Uso y costos estimados**, seleccione  **Límite diario**.

![Ajuste del límite de volumen de telemetría diario](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>muestreo
[Muestreo](app-insights-sampling.md) es un método que permite reducir la velocidad a la que se envían datos de telemetría a la aplicación, al mismo tiempo que se conserva la capacidad de buscar eventos relacionados durante las búsquedas de diagnósticos. También retiene recuentos de eventos correctos.

El muestreo es una manera efectiva de reducir los gastos y permanecer dentro de la cuota mensual. El algoritmo de muestreo conserva elementos relacionados con la telemetría, de modo que, por ejemplo, al usar Search se puede buscar la solicitud relacionada con una excepción determinada. El algoritmo también conserva recuentos correctos, para que pueda ver en el Explorador de métricas los valores correctos de tasas de solicitudes, tasas de excepciones y otros recuentos.

Existen varias formas de muestreo.

* [Muestreo adaptable](app-insights-sampling.md) es el valor predeterminado del SDK de ASP.NET. El muestreo adaptable se ajusta automáticamente al volumen de telemetría que envía la aplicación. Funciona automáticamente en el SDK de la aplicación web, de modo que se reduce el tráfico de telemetría en la red. 
* *muestreo de ingesta* es una opción alternativa que funciona en el momento en que la telemetría procedente de su aplicación entra en el servicio Application Insights. El muestreo de ingesta no afecta al volumen de telemetría que envía la aplicación, pero reduce el volumen que retiene el servicio. Puede utilizar el muestreo de ingesta para reducir la cuota que utiliza la telemetría de exploradores y otros SDK.

Para establecer el muestreo de ingesta, vaya al panel **Precios**:

![En el panel Cuota y precios, seleccione el icono Ejemplos y, a continuación, seleccione una fracción de muestreo.](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> El panel **Muestreo de datos** solo controla el valor de muestreo de ingesta. No refleja la velocidad de muestreo que se aplica mediante el SDK de Application Insights en la aplicación. Si ya se ha muestreado la telemetría entrante en el SDK, no se aplica el muestreo de ingesta.
>

Para conocer la frecuencia de muestreo real independientemente de dónde se ha aplicado, use una [consulta de Analytics](app-insights-analytics.md). La consulta tiene este aspecto:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

En cada registro retenido, `itemCount` indica el número de registros originales que representa. Es igual a 1 + el número de registros descartados anteriores. 

## <a name="automation"></a>Automation

Puede escribir un script para establecer el plan de precios con la administración de recursos de Azure. [Más información](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Resumen de límites

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Muestreo](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/