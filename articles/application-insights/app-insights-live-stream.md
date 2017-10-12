---
title: "Live Metrics Stream con métricas y diagnósticos personalizados en Azure Application Insights | Microsoft Docs"
description: "Supervise la aplicación web en tiempo real con métricas personalizadas y diagnostique problemas con una fuente directa de errores, seguimientos y eventos."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: bwren
ms.openlocfilehash: 1eb2e0c467d4fb4cb263047caf58d36231578d9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: supervisión y diagnóstico con una latencia de 1 segundo 

Sondee la maquinaria que mantiene operativa la aplicación web, ya activa y en producción, con Live Metrics Stream de [Application Insights](app-insights-overview.md). Seleccione métricas y contadores de rendimiento y fíltrelos para inspeccionarlos en tiempo real sin que el servicio se vea afectado. Inspeccione seguimientos de la pila procedentes de ejemplos de errores de solicitudes y excepciones. Junto con [Profiler](app-insights-profiler.md), el [depurador de instantáneas](app-insights-snapshot-debugger.md) y la [prueba de rendimiento](app-insights-monitor-web-app-availability.md#performance-tests), Live Metrics Stream proporciona una herramienta de diagnóstico eficaz y no invasiva para sitios web activos.

Con Live Metrics Stream, puede:

* Inspeccionar el rendimiento y los recuentos de errores mientras se publica una solución para validarla.
* Inspeccionar el efecto de las cargas de prueba y diagnosticar problemas en tiempo real. 
* Centrarse en ciertas sesiones de prueba o filtrar problemas conocidos seleccionando las métricas que quiera inspeccionar y filtrándolas.
* Obtener seguimientos de las excepciones cuando se produzcan.
* Experimentar con filtros para localizar los indicadores clave de rendimiento más pertinentes.
* Supervisar cualquier contador de rendimiento de Windows en tiempo real.
* Identifique fácilmente un servidor con problemas y filtre todos los KPI o fuente directa solo en dicho servidor.

[![Vídeo de Live Metrics Stream](./media/app-insights-live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Live Metrics Stream está disponible actualmente en aplicaciones de ASP.NET que se ejecuten de forma local o en la nube. 

## <a name="get-started"></a>Introducción

1. Si aún no ha [instalado Application Insights](app-insights-asp-net.md) en la aplicación web de ASP.NET o la [aplicación de Windows Server](app-insights-windows-services.md), hágalo ahora. 
2. **Actualice a la versión más reciente** del paquete de Application Insights. En Visual Studio, haga clic con el botón derecho en el proyecto y elija **Administrar paquetes NuGet**. Abra la pestaña **Actualizaciones**, marque **Incluir versión previa** y seleccione todos los paquetes Microsoft.ApplicationInsights.*

    Vuelva a implementar la aplicación.

3. En [Azure Portal](https://portal.azure.com), abra el recurso de Application Insights de la aplicación y, a continuación, abra Live Stream.

4. [Proteja el canal de control](#secure-the-control-channel) si puede que vaya a usar información confidencial como nombres de clientes en los filtros.


![En la hoja de información general, haga clic en Stream en vivo.](./media/app-insights-live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>¿No hay datos? Comprobación del firewall del servidor

Compruebe que [los puertos de salida de Live Metrics Stream](app-insights-ip-addresses.md#outgoing-ports) estén abiertos en el firewall de los servidores. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>¿En qué se diferencia Live Metrics Stream de Explorador de métricas y Analytics?

| |Live Stream | Explorador de métricas y Analytics |
|---|---|---|
|Latency|Los datos se muestran en un segundo.|La agregación se realiza en minutos.|
|Sin retención|Los datos se conservan solo mientras se encuentren en el gráfico y luego se descartan.|[Los datos se conservan durante 90 días.](app-insights-data-retention-privacy.md#how-long-is-the-data-kept)|
|A petición|Se transmiten datos mientras se abre Live Metrics.|Se envían datos siempre que el SDK esté instalado y habilitado.|
|Gratuito|No se efectúa ningún cargo por los datos de Live Stream.|Están sujetos a aplicación de [precios](app-insights-pricing.md).
|muestreo|Se transmiten todas las métricas y los contadores seleccionados. Se muestrean los errores y seguimientos de la pila. No se aplican elementos TelemetryProcessor.|Se pueden [muestrear](app-insights-api-filtering-sampling.md) eventos.|
|Canal de control|Las señales de control de filtro se envían al SDK. Se recomienda [proteger este canal](#secure-channel).|La comunicación es unidireccional, hacia el portal.|


## <a name="select-and-filter-your-metrics"></a>Selección y filtrado de métricas

(Disponible para aplicaciones de ASP.NET clásicas con el SDK más reciente).

Puede supervisar KPI personalizados en vivo aplicando filtros arbitrarios en cualquier telemetría de Application Insights desde el portal. Haga clic en el control de filtro que se muestra cuando coloca el mouse sobre cualquiera de los gráficos. En el siguiente gráfico se traza un KPI de recuento de solicitudes personalizado con filtros en atributos de URL y duración. Valide los filtros con la sección de versión preliminar de secuencias que muestra una fuente activa de telemetría que coincide con los criterios especificados en cualquier momento. 

![KPI personalizado de solicitudes](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Puede supervisar un valor que no sea el de recuento. Las opciones dependen del tipo de secuencia, que podría ser cualquier telemetría de Application Insights: solicitudes, dependencias, excepciones, seguimientos, eventos o métricas. Puede ser su propia [medida personalizada](app-insights-api-custom-events-metrics.md#properties):

![Opciones de valor](./media/app-insights-live-stream/live-stream-valueoptions.png)

Además de telemetría de Application Insights, también puede supervisar cualquier contador de rendimiento de Windows eligiendo entre las opciones de secuencias y proporcionando el nombre del contador de rendimiento.

Las métricas activas se agregan en dos puntos: localmente en cada servidor y, a continuación, en todos los servidores. Puede cambiar el valor predeterminado en cualquiera de ellos seleccionando otras opciones en las correspondientes listas desplegables.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetría de ejemplo: eventos personalizados de diagnóstico en vivo
De forma predeterminada, la fuente directa de eventos muestra ejemplos de solicitudes con error y llamadas de dependencia, excepciones, eventos y seguimientos. Haga clic en el icono de filtro para ver los criterios aplicados en cualquier momento. 

![Fuente directa predeterminada](./media/app-insights-live-stream/live-stream-eventsdefault.png)

Como con las métricas, puede especificar cualquier criterio arbitrario en cualquiera de los tipos de telemetría de Application Insights. En este ejemplo, seleccionaremos los eventos, seguimientos y errores de solicitud específicos. También se han seleccionado todas las excepciones y errores de dependencia.

![Fuente personalizada en vivo](./media/app-insights-live-stream/live-stream-events.png)

Nota: Actualmente, para los criterios basados en mensajes de excepción, use el mensaje de excepción más externo. En el ejemplo anterior, para filtrar la excepción benigna con el mensaje de excepción interna (lo que sigue al delimitador "<--"): "El cliente está desconectado". Use un mensaje que no contenga criterios de error al leer el contenido de la solicitud.

Consulte los detalles de un elemento en la fuente directa haciendo clic en él. Puede pausar la fuente haciendo clic en **Pausar** o simplemente desplazándose hacia abajo o haciendo clic en un elemento. La fuente directa se reanudará una vez que se desplace hacia la parte superior, o bien al hacer clic en el contador de elementos recopilados mientras estaba en pausa.

![Errores de muestreo de Live](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrado por instancia de servidor

Si quiere supervisar una instancia de rol de servidor en particular, puede filtrar por servidor.

![Errores de muestreo de Live](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Requisitos de SDK
Las secuencias personalizadas de Live Metrics Stream están disponibles con la versión 2.4.0-beta2 o la más reciente del [SDK de Application Insights para la web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). No olvide seleccionar la opción "Incluir versión previa" desde el Administrador de paquetes de NuGet.

## <a name="secure-the-control-channel"></a>Protección del canal de control
Los criterios de filtros personalizados que especifique se envían hacia el componente de Live Metrics del SDK de Application Insights. Los filtros podrían contener información confidencial, como identificadores de clientes. Puede hacer que el canal seguro con una clave secreta de API, además de la clave de instrumentación.
### <a name="create-an-api-key"></a>Creación de una clave de API

![Creación de una clave de API](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Incorporación de la clave de API a la configuración
En el archivo applicationinsights.config, agregue AuthenticationApiKey a QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add> 

```
O bien, en el código, establezca esto en QuickPulseTelemetryModule:

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```

Sin embargo, si reconoce y confía en todos los servidores conectados, puede probar los filtros personalizados sin el canal autenticado. Esta opción está disponible durante seis meses. Esta invalidación se requiere una vez cada nueva sesión, o cuando se pone en línea un nuevo servidor.

![Opciones de autenticación de Live Metrics](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>Se recomienda que configure el canal autenticado antes de escribir información potencialmente confidencial, como identificadores de clientes en los criterios de filtro.
>

## <a name="generating-a-performance-test-load"></a>Generación de una carga de prueba de rendimiento

Si quiere ver el efecto que tendría un aumento de carga, use la hoja Prueba de rendimiento. Simula solicitudes de varios usuarios simultáneos. Puede ejecutar "pruebas manuales" (pruebas de ping) de una única URL, o bien ejecutar una [prueba de rendimiento web de varios pasos](app-insights-monitor-web-app-availability.md#multi-step-web-tests) que cargue (de la misma forma que una prueba de disponibilidad).

> [!TIP]
> Después de crear la prueba de rendimiento, abra la prueba y la hoja de Live Stream en ventanas diferentes. Puede ver el momento en el que comienza la prueba de rendimiento en cola e inspeccionar Live Stream al mismo tiempo.
>


## <a name="troubleshooting"></a>Solución de problemas

¿No hay datos? Si la aplicación está en una red protegida, Live Metrics Stream usará una dirección IP diferente que otra telemetría de Application Insights. Asegúrese de que [esas direcciones IP](app-insights-ip-addresses.md) están abiertos en el firewall.



## <a name="next-steps"></a>Pasos siguientes
* [Supervisión del uso con Application Insights](app-insights-web-track-usage.md)
* [Uso de la Búsqueda de diagnóstico](app-insights-diagnostic-search.md)
* [Generador de perfiles](app-insights-profiler.md)
* [Depurador de instantáneas](app-insights-snapshot-debugger.md)
