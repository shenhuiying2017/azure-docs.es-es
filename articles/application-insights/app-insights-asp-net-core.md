---
title: Azure Application Insights para ASP.NET Core | Microsoft Docs
description: Supervise la disponibilidad, el rendimiento y el uso de las aplicaciones web.
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: d86495eea467977f6c079de72e2b49a2a1da2b60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights para ASP.NET Core
[Application Insights](app-insights-overview.md) permite supervisar la disponibilidad, el rendimiento y el uso de una aplicación web. Con los comentarios que obtendrá sobre el rendimiento y la eficacia de la aplicación en su entorno natural, pueda tomar decisiones meditadas sobre la dirección del diseño en cada ciclo de vida de desarrollo.

![Ejemplo](./media/app-insights-asp-net-core/sample.png)

Necesitará una suscripción a [Microsoft Azure](http://azure.com). Inicie sesión con una cuenta Microsoft, que podría tener para Windows, XBox Live u otros servicios en la nube de Microsoft. Si su equipo tiene una suscripción organizativa a Azure, el propietario puede agregarle a esta con la cuenta de Microsoft.

## <a name="getting-started"></a>Introducción

* En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto y elija **Configurar Application Insights** o **Agregar > Application Insights**. [Más información](app-insights-asp-net.md).
* Si no ve los comandos de menú, siga la [guía de introducción manual](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started). Puede necesitar hacerlo si el proyecto se creó con una versión de Visual Studio anterior a 2017.

## <a name="using-application-insights"></a>Uso de Application Insights
Inicie sesión en [Microsoft Azure portal](https://portal.azure.com), seleccione **Todos los recursos** o **Application Insights**; luego, seleccione el recurso que ha creado para supervisar la aplicación.

En una ventana del explorador independiente, use la aplicación durante un tiempo. Verá los datos que aparecen en los gráficos de Application Insights. (Es posible que tenga que hacer clic en Actualizar). Solo habrá una pequeña cantidad de datos mientras está desarrollando, pero estos gráficos realmente cobrarán vida cuando publique su aplicación y disponga de varios usuarios. 

La página de información general muestra los gráficos de rendimiento clave: tiempo de respuesta del servidor, tiempo de carga de la página y recuentos de solicitudes con error. Haga clic en cualquier gráfico para ver más gráficos y datos.

Las vistas del portal se dividen en tres categorías principales:

* El [Explorador de métricas](app-insights-metrics-explorer.md) muestra gráficos y tablas de métricas y recuentos, como tiempos de respuesta, tasas de errores o las métricas que crea el usuario con la [API](app-insights-api-custom-events-metrics.md). Filtre y segmente los datos por valores de propiedad para obtener una mejor comprensión de la aplicación y sus usuarios.
* El [Explorador de búsqueda](app-insights-diagnostic-search.md) enumera los eventos individuales, como solicitudes específicas, excepciones, seguimientos de registros o eventos creados por el usuario con la [API](app-insights-api-custom-events-metrics.md). Filtre y busque en los eventos y desplácese entre los eventos relacionados para investigar los problemas.
* [Analytics](app-insights-analytics.md) permite ejecutar consultas de tipo SQL a través de los datos de telemetría; además, constituye una eficaz herramienta de análisis y diagnóstico.

## <a name="alerts"></a>Alertas
* Obtendrá automáticamente [alertas de diagnóstico proactivo](app-insights-proactive-diagnostics.md) que le comunican cambios anómalos en las tasas de error y otras métricas.
* Configure [pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) para probar su sitio web continuamente desde ubicaciones de todo el mundo y obtener correos electrónicos en cuanto alguna prueba falle.
* Configure [alertas de métricas](app-insights-monitor-web-app-availability.md) para saber si métricas como los tiempos de respuesta o las tasas de excepciones sobrepasan los límites aceptables.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="open-source"></a>Código abierto
[Lectura y contribución al código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


## <a name="next-steps"></a>Pasos siguientes
* [Agregue datos de telemetría a las páginas web](app-insights-javascript.md) para supervisar el uso y el rendimiento de las páginas.
* [Supervise dependencias](app-insights-asp-net-dependencies.md) para ver si REST, SQL u otros recursos externos se están ralentizando.
* [Use la API](app-insights-api-custom-events-metrics.md) para enviar sus propios eventos y métricas para obtener una vista más detallada del rendimiento y el uso de la aplicación.
* [Las pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) comprueban su aplicación constantemente desde cualquier parte del mundo. 

