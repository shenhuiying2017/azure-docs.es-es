---
title: Procedimientos en Azure Application Insights | Microsoft Docs
description: P+F en Application Insights.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: mbullwin
ms.openlocfilehash: a32127f14c93012b5ace11ff982824f9ecba7d94
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="how-do-i--in-application-insights"></a>¿Cómo ... en Application Insights?
## <a name="get-an-email-when-"></a>Recibir un correo electrónico cuando...
### <a name="email-if-my-site-goes-down"></a>Enviar un correo electrónico si mi sitio deja de funcionar
Establecer una [prueba web de disponibilidad](app-insights-monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Enviar un correo electrónico si mi sitio está sobrecargado
Establecer una [alerta](app-insights-alerts.md) en **Tiempo de respuesta del servidor**. Un umbral entre 1 y 2 segundos debería funcionar.

![](./media/app-insights-how-do-i/030-server.png)

La aplicación también podría mostrar señales de esfuerzo al devolver códigos de error. Establezca una alerta en **Solicitudes con error**.

Si quiere establecer una alerta en **Excepciones de servidor**, puede que tenga que realizar [alguna configuración adicional](app-insights-asp-net-exceptions.md) para poder ver los datos.

### <a name="email-on-exceptions"></a>Envío de excepciones por correo electrónico
1. [Configurar supervisión de excepciones](app-insights-asp-net-exceptions.md)
2. [Establecer una alerta](app-insights-alerts.md) en la métrica de recuento de excepciones

### <a name="email-on-an-event-in-my-app"></a>Enviar un correo electrónico sobre un evento en mi aplicación
Supongamos que quiere recibir un correo electrónico cuando se produce un evento específico. Application Insights no ofrece esta función directamente, pero puede [enviar una alerta cuando una métrica sobrepasa un umbral](app-insights-alerts.md).

Las alertas se pueden establecer en [métricas personalizadas](app-insights-api-custom-events-metrics.md#trackmetric), aunque no así los eventos no personalizados. Escribir código para aumentar una métrica cuando se produce el evento:

    telemetry.TrackMetric("Alarm", 10);

o:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Dado que las alertas tienen dos estados, debe enviar un valor bajo cuando considere que la alerta ha finalizado:

    telemetry.TrackMetric("Alarm", 0.5);

Cree un gráfico en el [Explorador de métricas](app-insights-metrics-explorer.md) para ver la alarma:

![](./media/app-insights-how-do-i/010-alarm.png)

Ahora establezca que se desencadene una alerta cuando la métrica esté por encima de un valor medio durante un período breve de tiempo:

![](./media/app-insights-how-do-i/020-threshold.png)

Establecer el período promedio en el mínimo.

Recibirá mensajes de correo electrónico cuando la métrica esté por encima y por debajo del umbral.

Algunos puntos que se deben tener en cuenta:

* Una alerta tiene dos estados ("alerta" y "correcto"). El estado se evalúa solo cuando se recibe una métrica.
* Un mensaje de correo electrónico solo se envía cuando cambia el estado. Este es el motivo por el que tiene que enviar métricas tanto de valores altos como bajos.
* Para evaluar la alerta, la media se toma de los valores recibidos durante el período anterior. Esto se produce cada vez que se recibe una métrica, por lo que los mensajes de correo electrónico se pueden enviar con mayor frecuencia que el período definido.
* Puesto que los correos electrónicos se envían tanto en los estados "alerta" como "correcto", es posible que considere volver a pensar en su evento monoestable como en una condición de dos estados. Por ejemplo, en lugar de un evento de "trabajo completado", tiene una condición de "trabajo en curso", donde recibe correos electrónicos al principio y al final de un trabajo.

### <a name="set-up-alerts-automatically"></a>Configuración de alertas automáticamente
[Uso de PowerShell para crear nuevas alertas](app-insights-alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Uso de PowerShell para administrar Application Insights
* [Creación de nuevos recursos](app-insights-powershell-script-create-resource.md)
* [Creación de nuevas alertas](app-insights-alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Telemetría independiente de diferentes versiones

* Varios roles en una aplicación: usar un único recurso de Application Insights y filtrar por cloud_NombreDelRol. [Más información](app-insights-monitor-multi-role-apps.md)
* Separación de versiones de desarrollo, prueba y publicación: utilizar diferentes recursos de Application Insights. Tome las claves de instrumentación de web.config. [Más información](app-insights-separate-resources.md)
* Generación de informes de versiones de compilación: agregar una propiedad usando un inicializador de telemetría. [Más información](app-insights-separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Supervisar servidores back-end y aplicaciones de escritorio
[Use el módulo de SDK de Windows Server](app-insights-windows-desktop.md).

## <a name="visualize-data"></a>Visualización de datos
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Panel con métricas de varias aplicaciones
* En el [Explorador de métricas](app-insights-metrics-explorer.md), personalice el gráfico y guárdelo como favorito. Ánclelo al panel de Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Panel con datos de otros orígenes y Application Insights
* [Exporte la telemetría a Power BI](app-insights-export-power-bi.md).

O

* Use SharePoint como panel, mostrando los datos en elementos web de SharePoint. [Use la exportación continua y Análisis de transmisiones para exportar a SQL](app-insights-code-sample-export-sql-stream-analytics.md).  Use PowerView para examinar la base de datos y crear un elemento web de SharePoint para PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrado de usuarios anónimos o autenticados
Si los usuarios inician sesión, puede establecer el [identificador de usuario autenticado](app-insights-api-custom-events-metrics.md#authenticated-users). (No ocurre automáticamente).

A continuación, puede:

* Buscar identificadores de usuario específicos

![](./media/app-insights-how-do-i/110-search.png)

* Filtrar métricas a los usuarios anónimos o autenticados

![](./media/app-insights-how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modificación de valores o nombres de propiedad
Cree un [filtro](app-insights-api-filtering-sampling.md#filtering). Esto le permite modificar o filtrar telemetría antes de que se envíe desde su aplicación a Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Enumeración de usuarios específicos y su uso
Si solo desea [buscar usuarios específicos](#search-specific-users), puede establecer el [identificador de usuario autenticado](app-insights-api-custom-events-metrics.md#authenticated-users).

Si desea obtener una lista de usuarios con datos como las páginas que ven o la frecuencia con la que inician sesión, tiene dos opciones:

* [Establezca el identificador de usuario autenticado](app-insights-api-custom-events-metrics.md#authenticated-users), [exporte a una base de datos](app-insights-code-sample-export-sql-stream-analytics.md) y use las herramientas adecuadas para analizar los datos de usuario.
* Si solo dispone de un número reducido de usuarios, envíe eventos o métricas personalizados, mediante el uso de los datos de interés como el valor de métrica o el nombre de evento y el establecimiento del identificador de usuario como una propiedad. Para analizar las vistas de página, reemplace la llamada trackPageView de JavaScript estándar. Para analizar la telemetría en el servidor, use un inicializador de telemetría para agregar el identificador de usuario para toda la telemetría de servidor. Después puede filtrar y segmentar las métricas y las búsquedas en el identificador de usuario.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Reducción del tráfico de la aplicación a Application Insights
* En [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), deshabilite los módulos que no necesite, como el recopilador de contadores de rendimiento.
* Use [Muestreo y filtrado](app-insights-api-filtering-sampling.md) en el SDK.
* En las páginas web, limite el número de llamadas AJAX que se notifican para cada vista de página. En el fragmento de script después de `instrumentationKey:...`, inserte: `,maxAjaxCallsPerView:3` (o un número adecuado).
* Si está usando [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric), calcule el agregado de los lotes de los valores de métricas antes de enviar el resultado. Hay una sobrecarga de TrackMetric() que se proporciona para eso.

Obtenga más información sobre [precios y cuotas](app-insights-pricing.md).

## <a name="disable-telemetry"></a>Deshabilitar la telemetría
Para **iniciar y detener dinámicamente** la recopilación y la transmisión de telemetría desde el servidor:

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



Para **deshabilitar los recopiladores estándar seleccionados** , por ejemplo, los contadores de rendimiento, las solicitudes HTTP o las dependencias, elimine o comente las líneas correspondientes en [ApplicationInsights.config](app-insights-api-custom-events-metrics.md). Podría hacer esto, por ejemplo, si quiere enviar sus propios datos de TrackRequest.

## <a name="view-system-performance-counters"></a>Visualización de contadores de rendimiento del sistema
Entre las métricas que se pueden mostrar en el Explorador de métricas se encuentra un conjunto de contadores de rendimiento del sistema. Hay una hoja predefinida denominada **Servidores** que muestra varios de ellos.

![Abra el recurso de Application Insights y haga clic en Servidores](./media/app-insights-how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Si no ve ningún dato de contadores de rendimiento
* **servidor IIS** en su propia máquina o en una VM. [Instale el Monitor de estado](app-insights-monitor-performance-live-website-now.md).
* **Sitio web de Azure** : todavía no se admiten los contadores de rendimiento. Hay varias métricas que se puede obtener como parte estándar del sitio web de Azure en el panel de control.
* **Servidor Unix** - [instale collectd](app-insights-java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Para mostrar más contadores de rendimiento
* En primer lugar, [agregue un nuevo gráfico](app-insights-metrics-explorer.md) y compruebe si el contador está en el conjunto básico que se ofrece.
* Si no es así, [agregue el contador al conjunto recopilado por el módulo del contador de rendimiento](app-insights-performance-counters.md).
