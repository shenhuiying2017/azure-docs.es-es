---
title: "Administración de precios y volúmenes de datos de Azure Application Insights | Microsoft Docs"
description: "Administre los volúmenes de telemetría y supervise los costos en Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mbullwin
ms.openlocfilehash: 95c5195ac2ea832586211cce37eb2094e06eaf03
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Administración de precios y volúmenes de datos de Application Insights


Los precios para [Azure Application Insights][start] se basan en el volumen de datos por aplicación. Lo más probable es que sea gratuito en el caso de una baja utilización durante el desarrollo o para una aplicación pequeña, porque hay una provisión mensual de 1 GB de datos de telemetría.

Cada recurso de Application Insights se cobra como un servicio independiente y contribuye a la factura de la suscripción a Azure.

Hay dos planes de precios. El plan predeterminado se conoce como Básico. Puede elegir el plan Enterprise, que tiene un cargo diario, pero permite que determinadas características adicionales como la [exportación continua](app-insights-export-telemetry.md).

Si tiene preguntas sobre cómo funciona los precios para Application Insights, no dude en publicar una pregunta en nuestro [foro](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights). 

## <a name="the-price-plans"></a>Los planes de precios

Consulte la [página de precios de Application Insights][pricing] para ver los precios actuales en su moneda.

### <a name="basic-plan"></a>Plan Básico

El plan Básico es el predeterminado cuando se crea un nuevo recurso de Application Insights y bastará para la mayoría de los clientes.

* En el plan Básico, se le cobrará por volumen de datos: el número de bytes de telemetría que reciba Application Insights. El volumen de datos se mide como el tamaño del paquete de datos JSON sin comprimir que reciba Application Insights de su aplicación.
Para [los datos tabulares importados en Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), el volumen de datos se mide como el tamaño descomprimido de los archivos enviados a Application Insights.  
* El primer GB de cada aplicación es gratuito, por lo que si solo está experimentando o desarrollando, lo más probable es que no tenga que pagar nada.
* Los datos de la [secuencia de métricas en directo](app-insights-live-stream.md) no cuentan para calcular los precios.
* La [exportación continua](app-insights-export-telemetry.md) se encuentra disponible a cambio de un cargo adicional por cada GB en el plan Básico.

### <a name="enterprise-plan"></a>Plan Enterprise

* En el plan Enterprise, su aplicación puede usar todas las características de Application Insights. La [exportación continua](app-insights-export-telemetry.md) y 

el [conector de Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) se encuentran disponibles sin ningún cargo adicional en el plan Enterprise.
* En dicho plan, se paga por nodo que envíe telemetría desde cualquier aplicación. 
 * Un *nodo* es un servidor físico o virtual o una instancia de rol de plataforma como servicio que hospeda su aplicación.
 * Los equipos de desarrollo, los exploradores cliente y los dispositivos móviles no se cuentan como nodos.
 * Si su aplicación presenta varios componentes que envían telemetría, como un servicio web y un trabajo de back-end, se cuentan por separado.
 * Los datos de [Live Metrics Stream](app-insights-live-stream.md) no cuentan para calcular los precios.* En una suscripción, sus cargos son por nodo, no por aplicación. Si tiene cinco nodos que envían telemetría de 12 aplicaciones, se le cobrará por 5 nodos.
* Aunque los cargos se presupuestan mensualmente, solo se le cobrarán las horas en las que un nodo envíe telemetría de una aplicación. El cargo por hora es el cargo mensual presupuestado partido por 744 (el número de horas en un mes de 31 días).
* Se proporciona una asignación de volumen de datos de 200 MB por día para cada nodo detectado (con una granularidad de horas). Las asignaciones de datos no utilizadas no se guardarán de un día para otro.
 * Si elige el plan Enterprise, cada suscripción recibe una asignación de datos diaria en función del número de nodos que envíen telemetría a los recursos de Application Insights de esa suscripción. De modo que, si tiene 5 nodos enviando datos todos los días, tendrá una asignación global de 1 GB para todos los recursos de Application Insights de esa suscripción. No importa si algunos nodos envían más datos que otros, porque los datos incluidos se comparten entre todos los nodos. Si, un día determinado, los recursos de Application Insights reciben más datos de los que se incluyen en la asignación de datos diaria para esta suscripción, se aplicarán los cargos de datos por encima del límite por cada GB. 
 * La asignación de datos diaria se calcula como el número de horas del día (de acuerdo con UTC) que cada nodo envía telemetría dividido entre 24 por 200 MB. Es decir, si tiene 4 nodos enviando telemetría durante 15 de las 24 horas del día, los datos incluidos para ese día serán ((4 x 15) / 24) x 200 MB = 500 MB. A un precio de 2,30 USD por GB en el caso de uso por encima del límite de datos, el cargo sería de 1,15 USB si los nodos envían 1 GB de datos ese día.
 * Tenga en cuenta que la asignación diaria del plan Enterprise no se comparte con las aplicaciones para las que haya elegido la opción Básica y que las asignaciones no utilizadas no se guardan de un día a otro. 
* Estos son algunos de los ejemplos de cómo determinar el número concreto de nodos:
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
  * Tenga en cuenta que si su aplicación está usando el SDK para establecer roleInstance en un valor personalizado, ese mismo valor se usará para determinar el recuento de nodos de forma predeterminada. 
  * Si utiliza una nueva versión del SDK con una aplicación que se ejecuta desde equipos cliente o dispositivos móviles, es posible que el recuento de nodos devuelva un número muy grande (del gran número de equipos cliente o dispositivos móviles). 

### <a name="multi-step-web-tests"></a>Pruebas web de varios pasos

Se realiza un cargo adicional para las [pruebas web de varios pasos](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Es decir, pruebas web que realizan una secuencia de acciones. 

No hay ningún cargo aparte para las "pruebas de ping" de una sola página. La telemetría de las pruebas de ping y de las de varios pasos se carga junto con el resto de la telemetría de su aplicación.
 
## <a name="operations-management-suite-subscription-entitlement"></a>Derechos de suscripción de Operations Management Suite

Como se [anunció recientemente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), los clientes que compran Microsoft Operations Management Suite E1 y E2 pueden obtener Application Insights Enterprise como componente adicional sin costo alguno. Específicamente, cada unidad de Operations Management Suite E1 y E2 incluye el derecho a usar un nodo del plan Enterprise de Application Insights. Como se mencionó antes, cada nodo de Application Insights incluye hasta 200 MB de datos ingeridos por día (independientes de la ingesta de datos de Log Analytics), con la retención de datos de 90 días sin costo adicional alguno. 

> [!NOTE]
> Para garantizar que tiene este derecho, debe disponer de los recursos de Application Insights en el plan de precios Enterprise. Este derecho se aplica solo como nodos, por lo que los recursos de Application Insights en el plan Básico no tendrán ninguna ventaja. Tenga en cuenta que este derecho no será visible en los costos estimados que se muestran en la hoja Características y precios. 
>
 
## <a name="review-pricing-plans-and-estimate-costs"></a>Revisión de los planes de precios y cálculo de costos

Application Insights permite entender fácilmente los planes de precios disponibles y qué costos se basarán probablemente en los patrones de uso reciente. Comience abriendo la hoja **Características y precios** en el recurso de Application Insights de Azure Portal.

![Elija el precio.](./media/app-insights-pricing/01-pricing.png)

**a.** Revise el volumen de datos para el mes. Esto incluye todos los datos recibidos y retenidos (después de cualquier [muestreo](app-insights-sampling.md) de las aplicaciones de servidor y cliente, y de las pruebas de disponibilidad.

**b.** Se realiza un cargo adicional para las [pruebas web de varios pasos](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Esto no incluye pruebas de disponibilidad simples, que se incluyen en la carga de volumen de datos).

**c.** Habilite el plan Enterprise.

**d.** Haga clic en las opciones de administración de datos para ver el volumen de datos del último mes, establecer un límite diario o configurar un muestreo de ingesta.

Los cargos de Application Insights se agregarán a la factura de Azure. Puede ver los detalles de su factura de Azure en la sección de facturación de Azure Portal o en el [Portal de facturación de Azure](https://account.windowsazure.com/Subscriptions). 

![En el menú lateral, elija Facturación.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Velocidad de datos
Hay tres formas en que se limita el volumen de envío de datos:

* **Muestreo:** este mecanismo se puede usar para reducir la cantidad de telemetría enviada desde las aplicaciones de servidor y de cliente, con mínima distorsión de las métricas. Se trata de la principal herramienta de la que dispone para ajustar la cantidad de datos. Obtenga más información sobre las [características de muestreo](app-insights-sampling.md). 
* **Límite diario:** al crear un recurso de Application Insights desde Azure Portal, este se establece en 100 GB/día. La capacidad predeterminada al crear un recurso de Application Insights desde Visual Studio, es pequeña (solo 32,3 MB/día), cuya finalidad no es más que facilitar las pruebas. En este caso, el objetivo es que el usuario aumente el límite diario antes de implementar la aplicación en producción. El límite máximo es de 1000 GB/día, a menos que haya solicitado un máximo superior para una aplicación con mucho tráfico. Actúe con precaución al establecer el límite diario, ya que su intención debería ser **no alcanzarlo nunca**, pues en ese caso perderá datos durante el resto del día y no podrá supervisar la aplicación. Para cambiarlo, use la hoja Límite de volumen diario, vinculada desde la hoja Administración de datos (consúltese a continuación). Tenga en cuenta que algunos tipos de suscripción disponen de un crédito que no se puede usar para Application Insights. Si la suscripción tiene un límite de gasto, la hoja Límite diario contará con instrucciones sobre cómo quitarlo y habilitarlo para que supere los 32,3 MB/día.  
* **Limitación**: esta restringe la velocidad de datos a 32 000 eventos por segundo, promediados durante 1 minuto. 


*¿Qué ocurre si mi aplicación supera el porcentaje de limitación?*

* El volumen de datos que su aplicación envía se evalúa cada minuto. Si se supera la tasa por segundo promediada por minuto, el servidor rechazará algunas solicitudes. El SDK almacena los datos en el búfer e intenta volver a enviarlos, lo que genera un sobrevoltaje durante varios minutos. Si su aplicación envía de forma consistente un volumen de datos que supera la tasa de limitación, es posible que algunos se eliminen. (Los SDK de ASP.NET, Java y JavaScript intentan volver a enviarlos de esta manera; otros SDK pueden simplemente eliminar los datos limitados.) Si se produce la limitación, verá una notificación de advertencia que indica que esto ha sucedido.

*¿Cómo puedo saber cuántos datos envía mi aplicación?*

* Abra la hoja **Administración de datos** para ver el gráfico de volumen de datos diario. 
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

Para cambiar el límite diario, en la sección de configuración del recurso de Application Insights, haga clic en **Administración de datos** y, a continuación, en **Límite diario**.

![Ajuste del límite de volumen de telemetría diario](./media/app-insights-pricing/daily-cap.png) 

## <a name="sampling"></a>muestreo
[Sampling](app-insights-sampling.md)es un método que permite reducir la velocidad a la que se envían datos de telemetría a la aplicación, al mismo tiempo que se conserva la capacidad de buscar eventos relacionados durante las búsquedas de diagnósticos y se conservan los recuentos de eventos correctos. 

El muestreo es una manera efectiva de reducir los gastos y permanecer dentro de la cuota mensual. El algoritmo de muestreo conserva elementos relacionados con la telemetría, de modo que, por ejemplo, al usar la búsqueda se puede buscar la solicitud relacionada con una excepción determinada. El algoritmo también conserva recuentos correctos, para que pueda ver en el Explorador de métricas los valores correctos de tasas de solicitudes, tasas de excepciones y otros recuentos.

Existen varias formas de muestreo.

* [Adaptive sampling](app-insights-sampling.md) es el predeterminado para el SDK de ASP.NET, que ajusta automáticamente el volumen de telemetría que envía la aplicación. Funciona automáticamente en el SDK de la aplicación web, de modo que se reduce el tráfico de telemetría en la red. 
* *muestreo de ingesta* es una opción alternativa que funciona en el momento en que la telemetría procedente de su aplicación entra en el servicio Application Insights. No afecta al volumen de telemetría que envía la aplicación, pero reduce el volumen que retiene el servicio. Puede utilizarlo para reducir la cuota que utiliza la telemetría de exploradores y otros SDK.

Para establecer el muestreo de ingesta, establezca el control en la hoja de precios:

![En la hoja Cuota y precios, haga clic en el icono Ejemplos y seleccione una fracción de muestreo.](./media/app-insights-pricing/04.png)

> [!WARNING]
> La hoja de muestreo Datos solo controla el valor de muestreo de la ingesta. No refleja la velocidad de muestreo que se aplica mediante el SDK de Application Insights en la aplicación. Si ya se ha muestreado la telemetría entrante en el SDK, no se aplica el muestreo de ingesta.
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

## <a name="next-steps"></a>pasos siguientes

* [Muestreo](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

