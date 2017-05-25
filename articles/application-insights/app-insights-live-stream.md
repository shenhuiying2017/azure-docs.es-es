---
title: "Diagnósticos y métricas activas personalizadas de Azure Application Insights | Microsoft Docs"
description: "Supervise la aplicación web en tiempo real, con métricas personalizadas, y diagnostique problemas con una fuente directa de errores, seguimientos y eventos."
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
ms.date: 05/10/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 86e01cf6cb14334e85da4102610fa7feb66cb543
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="custom-live-metrics-and-events-monitor--diagnose-with-1-second-latency"></a>Eventos y métricas activas personalizadas: supervisión y diagnóstico con una latencia inferior a un segundo 
Live Metrics Stream muestra las métricas y los eventos de [Application Insights](app-insights-overview.md) con una latencia casi en tiempo real de un segundo. Esta supervisión instantánea ayuda a reducir el tiempo promedio de detección y diagnóstico, así como a cumplir el Acuerdo de Nivel de Servicio. Puede:
* Supervisar los KPI personalizados en vivo: experimente con los filtros, con los que podrá segmentar y desglosar los datos de telemetría de Application Insights existentes para obtener los KPI más pertinentes al instante desde el portal. No se requiere realizar cambios de configuración o codificación, ni implementaciones. También están disponibles las medidas o métricas personalizadas que podría estar enviándose.
* Detectar y diagnosticar en vivo: vea los errores de solicitud y dependencia cuando se produzcan junto con los seguimientos de excepción detallados. Filtre todos los problemas conocidos para centrarse en los reales o los nuevos.
* Depuración en vivo: reproduzca un problema y vea todos los datos de telemetría en vivo relacionados, filtrando de forma personalizado por un identificador de sesión específico (o con cualquier atributo personalizado) que identifique la interacción de reproducción. Recopile información de todos los servidores para empezar a corregir el problema.
* Vea al instante cómo el consumo de recursos reaccione a la carga: supervise *cualquier* contador de rendimiento en vivo, mientras que ejecuta pruebas de carga, o supervise la producción para tomar medidas antes de cualquier recurso se vea afectado. No se requiere realizar cambios de configuración o codificación, ni implementaciones.
* Valide una corrección que va a publicarse. Asegúrese de que la actualización de su servicio funcione correctamente cuando se lleve a cabo. Valide que el error que corrigió ya no se vuelve a producir.
* Identifique fácilmente un servidor con problemas y filtre todos los KPI o fuente directa solo en dicho servidor.

Los datos de Live Metrics Stream y de streaming de eventos son gratuitos: no se agregarán a la facturación. Estos se envían desde los servidores a petición al abrir el portal. Los datos se conservan solo mientras se encuentran en el gráfico, y luego se descartan. Toda la funcionalidad está disponible para las aplicaciones clásicas de ASP.NET, y las aplicaciones de .NET Core solo tienen actualmente un conjunto fijo de métricas activas y errores de ejemplo. Obtenemos todos los SDK compatibles para las funcionalidades de streaming en vivo más recientes. 

Recopilamos la secuencia activa de las instancias de la aplicación antes de cualquier muestreo, o de que se hayan aplicado procesos de telemetría personalizados. 

![Vídeo de Live Metrics Stream](./media/app-insights-live-stream/youtube.png) [Vídeo de Live Metrics Stream](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Acceda a las métricas y los eventos activos haciendo clic en la opción de la izquierda, o bien el botón en la hoja Información general:

![En la hoja de información general, haga clic en Stream en vivo.](./media/app-insights-live-stream/live-stream-2.png)

## <a name="custom-live-kpi"></a>KPI personalizado en vivo
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

## <a name="authenticated-channel"></a>Canal autenticado
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

## <a name="troubleshooting"></a>Solución de problemas

¿No hay datos? Si la aplicación está en una red protegida, Live Metrics Stream usará una dirección IP diferente que otra telemetría de Application Insights. Asegúrese de que [esas direcciones IP](app-insights-ip-addresses.md) están abiertos en el firewall.



## <a name="next-steps"></a>Pasos siguientes
* [Supervisión del uso con Application Insights](app-insights-web-track-usage.md)
* [Uso de la Búsqueda de diagnóstico](app-insights-diagnostic-search.md)


