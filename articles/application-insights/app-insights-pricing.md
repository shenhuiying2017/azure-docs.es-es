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
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Administración de precios y volúmenes de datos de Application Insights

Los precios para [Azure Application Insights][start] se basan en el volumen de datos por aplicación. Cada recurso de Application Insights se cobra como un servicio independiente y contribuye a la factura de la suscripción a Azure.

Hay dos planes de precios. El plan predeterminado se denomina Básico e incluye las mismas características que el plan Enterprise sin costo adicional, facturándose principalmente en función del volumen de ingestión de datos. Si utiliza Operations Management Suite, debería elegir el plan Enterprise, en el que se aplica un cargo por nodo junto con las concesiones de datos diarias y, posteriormente, se aplica otro cargo por los datos ingeridos por encima de la concesión incluida.

Si tiene preguntas sobre cómo funciona los precios para Application Insights, no dude en publicar una pregunta en nuestro [foro](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="the-price-plans"></a>Los planes de precios

Consulte en la [página Precios de Application Insights][pricing] los precios actuales en su divisa y región.

### <a name="basic-plan"></a>Plan Básico

El plan Básico es la opción predeterminada cuando se crea un nuevo recurso de Application Insights y resulta el más apropiado para todos los clientes excepto para aquellos que tienen una suscripción de Operations Management Suite.

* En el plan Básico, se le cobrará por volumen de datos: el número de bytes de telemetría que reciba Application Insights. El volumen de datos se mide como el tamaño del paquete de datos JSON sin comprimir que reciba Application Insights de su aplicación.
Para [los datos tabulares importados en Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), el volumen de datos se mide como el tamaño descomprimido de los archivos enviados a Application Insights.
* Los datos de la [secuencia de métricas en directo](app-insights-live-stream.md) no cuentan para calcular los precios.
* La [exportación continua](app-insights-export-telemetry.md) y el [conector de Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) se encuentran disponibles sin ningún cargo adicional en el plan Básico desde abril de 2018.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Derechos de suscripción del plan Enterprise y Operations Management Suite

Como se [anunció anteriormente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), los clientes que compran Microsoft Operations Management Suite E1 y E2 pueden obtener Application Insights Enterprise como componente adicional sin costo alguno. Específicamente, cada unidad de Operations Management Suite E1 y E2 incluye el derecho a usar un nodo del plan Enterprise de Application Insights. Como se describe más adelante en detalle, cada nodo de Application Insights incluye hasta 200 MB de datos ingeridos por día (independientes de la ingesta de datos de Log Analytics), con retención de datos de 90 días sin costo adicional alguno. Dado que esto solo es aplicable a clientes con una suscripción a Operations Management Suite, los clientes sin una suscripción no verán una opción para seleccionar este plan.

> [!NOTE]
> Para garantizar que tiene este derecho, debe disponer de los recursos de Application Insights en el plan de precios Enterprise. Este derecho se aplica solo como nodos, por lo que los recursos de Application Insights en el plan Básico no tendrán ninguna ventaja. Tenga en cuenta que este derecho no será visible en los costos estimados que se muestran en la página *Uso y costos estimados*. Además, si transfiere una suscripción al nuevo modelo de precios de supervisión de Azure de abril de 2018, el plan Básico será el único disponible, por lo que no se recomienda si tiene una suscripción a Operations Management Suite.

Para más información acerca del plan Enterprise, visite la [página de detalles de precios del plan Enterprise](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Pruebas web de varios pasos

Se realiza un cargo adicional para las [pruebas web de varios pasos](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Es decir, pruebas web que realizan una secuencia de acciones.

No hay ningún cargo aparte para las "pruebas de ping" de una sola página. La telemetría de las pruebas de ping y de las de varios pasos se carga junto con el resto de la telemetría de su aplicación.

## <a name="review-pricing-plans-and-estimate-costs"></a>Revisión de los planes de precios y cálculo de costos

Application Insights permite entender fácilmente los planes de precios disponibles y qué costos se basarán probablemente en los patrones de uso reciente. Comience abriendo la página **Uso y costos estimados** en el recurso de Application Insights de Azure Portal:

![Elija el precio.](./media/app-insights-pricing/pricing-001.png)

**a.** Revise el volumen de datos para el mes. Esto incluye todos los datos recibidos y retenidos (después de cualquier [muestreo](app-insights-sampling.md) de las aplicaciones de servidor y cliente, y de las pruebas de disponibilidad.

**b.** Se realiza un cargo adicional para las [pruebas web de varios pasos](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Esto no incluye pruebas de disponibilidad simples, que se incluyen en la carga de volumen de datos).

**c.** Vea las tendencias de volumen de datos durante el último mes.

**d.** Habilite el [muestreo](app-insights-sampling.md) de ingesta de datos. 

**e.** Configure el límite de volumen de datos diario.

Los cargos de Application Insights se agregarán a la factura de Azure. Puede ver los detalles de su factura de Azure en la sección de facturación de Azure Portal o en el [Portal de facturación de Azure](https://account.windowsazure.com/Subscriptions). 

![En el menú lateral, elija Facturación.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Velocidad de datos
Hay tres formas en que se limita el volumen de envío de datos:

* **Muestreo:** este mecanismo se puede usar para reducir la cantidad de telemetría enviada desde las aplicaciones de servidor y de cliente, con mínima distorsión de las métricas. Se trata de la principal herramienta de la que dispone para ajustar la cantidad de datos. Obtenga más información sobre las [características de muestreo](app-insights-sampling.md). 
* **Límite diario:** al crear un recurso de Application Insights desde Azure Portal, este se establece en 100 GB/día. La capacidad predeterminada al crear un recurso de Application Insights desde Visual Studio, es pequeña (solo 32,3 MB/día), cuya finalidad no es más que facilitar las pruebas. En este caso, el objetivo es que el usuario aumente el límite diario antes de implementar la aplicación en producción. El límite máximo es de 1000 GB/día, a menos que haya solicitado un máximo superior para una aplicación con mucho tráfico. Actúe con precaución al establecer el límite diario, ya que su intención debería ser **no alcanzarlo nunca**, pues en ese caso perderá datos durante el resto del día y no podrá supervisar la aplicación. Para cambiarlo, use la opción Límite de volumen diario, accesible a través de un vínculo de la página Uso y costos estimados (véase a continuación). Hemos quitado la restricción en algunos tipos de suscripción con crédito que no se podía usar para Application Insights. Anteriormente, si la suscripción tenía un límite de gasto, el cuadro de diálogo de límite diario mostraba instrucciones sobre cómo quitarlo y habilitarlo para superar los 32,3 MB/día.
* **Limitación**: restringe la velocidad de datos a 32 000 eventos por segundo, promediados durante 1 minuto.

*¿Qué ocurre si mi aplicación supera el porcentaje de limitación?*

* El volumen de datos que su aplicación envía se evalúa cada minuto. Si se supera la tasa por segundo promediada por minuto, el servidor rechazará algunas solicitudes. El SDK almacena los datos en el búfer e intenta volver a enviarlos, lo que genera un sobrevoltaje durante varios minutos. Si su aplicación envía de forma consistente un volumen de datos que supera la tasa de limitación, es posible que algunos se eliminen. (Los SDK de ASP.NET, Java y JavaScript intentan volver a enviarlos de esta manera; otros SDK pueden simplemente eliminar los datos limitados.) Si se produce la limitación, verá una notificación de advertencia que indica que esto ha sucedido.

*¿Cómo puedo saber cuántos datos envía mi aplicación?*

* Abra la página **Uso y costos estimados** para ver el gráfico de volumen de datos diario. 
* O bien, en el Explorador de métricas, agregue un nuevo gráfico y seleccione **Volumen de punto de datos** como su métrica. Active la agrupación y agrupe por **Tipo de datos**.

## <a name="to-reduce-your-data-rate"></a>Reducción de la velocidad de datos
Estas son algunas cosas que puede hacer para reducir el volumen de datos:

* Use el [Muestreo](app-insights-sampling.md). Esta tecnología reduce la velocidad de los datos sin sesgar las métricas y sin interrumpir la capacidad de navegar entre los elementos relacionados en la búsqueda. En las aplicaciones de servidor, funciona automáticamente.
* [Limite el número de llamadas AJAX que se pueden notificar](app-insights-javascript.md#detailed-configuration) en cada vista de página o desactive los informes de AJAX.
* Desactivar los módulos de recopilación que no necesite; para ello, [edite ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Por ejemplo, podría decidir que los contadores de rendimiento o datos de dependencia no son esenciales.
* Divida la telemetría para separar las claves de instrumentación. 
* Métricas agregadas previamente. Si ha colocado llamadas a TrackMetric en su aplicación, puede reducir el tráfico mediante la sobrecarga que acepta el cálculo de la media y la desviación estándar de un lote de medidas. O bien, puede usar un [paquete de agregación previa](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="managing-the-maximum-daily-data-volume"></a>Administración del volumen de datos diario máximo

Puede usar el límite diario de volumen para limitar los datos recopilados pero, si se alcanza el límite, se producirá una pérdida de todos los datos de telemetría enviados desde la aplicación para el resto del día. **No se recomienda** que la aplicación alcance el límite diario, ya que no puede realizar el seguimiento del estado y el rendimiento de la aplicación una vez que se alcanza.

En su lugar, use el [muestreo](app-insights-sampling.md) para optimizar el volumen de datos en el nivel que desee y use el límite diario únicamente como "último recurso" por si la aplicación comienza a enviar volúmenes de datos de telemetría mucho mayores de forma inesperada.

Para cambiar el límite diario, en la sección de configuración del recurso de Application Insights, en la página **Uso y costos estimados**, haga clic en **Límite diario**.

![Ajuste del límite de volumen de telemetría diario](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>muestreo
[Sampling](app-insights-sampling.md)es un método que permite reducir la velocidad a la que se envían datos de telemetría a la aplicación, al mismo tiempo que se conserva la capacidad de buscar eventos relacionados durante las búsquedas de diagnósticos y se conservan los recuentos de eventos correctos.

El muestreo es una manera efectiva de reducir los gastos y permanecer dentro de la cuota mensual. El algoritmo de muestreo conserva elementos relacionados con la telemetría, de modo que, por ejemplo, al usar la búsqueda se puede buscar la solicitud relacionada con una excepción determinada. El algoritmo también conserva recuentos correctos, para que pueda ver en el Explorador de métricas los valores correctos de tasas de solicitudes, tasas de excepciones y otros recuentos.

Existen varias formas de muestreo.

* [Adaptive sampling](app-insights-sampling.md) es el predeterminado para el SDK de ASP.NET, que ajusta automáticamente el volumen de telemetría que envía la aplicación. Funciona automáticamente en el SDK de la aplicación web, de modo que se reduce el tráfico de telemetría en la red. 
* *muestreo de ingesta* es una opción alternativa que funciona en el momento en que la telemetría procedente de su aplicación entra en el servicio Application Insights. No afecta al volumen de telemetría que envía la aplicación, pero reduce el volumen que retiene el servicio. Puede utilizarlo para reducir la cuota que utiliza la telemetría de exploradores y otros SDK.

Para establecer el muestreo de ingesta, establezca el control en el cuadro de diálogo de precios:

![En el cuadro de diálogo Cuota y precios, haga clic en el icono Ejemplos y seleccione una fracción de muestreo.](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> El cuadro de diálogo de muestreo Datos solo controla el valor de muestreo de la ingesta. No refleja la velocidad de muestreo que se aplica mediante el SDK de Application Insights en la aplicación. Si ya se ha muestreado la telemetría entrante en el SDK, no se aplica el muestreo de ingesta.
>

Para conocer la frecuencia de muestreo real independientemente de dónde se ha aplicado, use una [consulta de Analytics](app-insights-analytics.md) como esta:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

En cada registro retenido, `itemCount` indica el número de registros originales que representa, equivale a 1 + el número de registros descartados anteriores. 

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
