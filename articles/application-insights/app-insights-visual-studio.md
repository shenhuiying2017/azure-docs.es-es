---
title: Depurar aplicaciones con Azure Application Insights en Visual Studio | Microsoft Docs
description: "Análisis del rendimiento y diagnóstico de aplicaciones web durante la depuración y en producción."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 2059802b-1131-477e-a7b4-5f70fb53f974
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: mbullwin
ms.openlocfilehash: 656c62e7227eef967696715f0882114631249c6c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Depure sus aplicaciones con Azure Application Insights en Visual Studio
En Visual Studio (2015 y versiones posteriores), se pueden diagnosticar y analizar los problemas de rendimiento de las aplicaciones web, tanto en tiempo de depuración como en producción, mediante los datos de telemetría de [Azure Application Insights](app-insights-overview.md).

Si ha creado la aplicación web de ASP.NET mediante Visual Studio 2017 o versiones posteriores, ya tiene el SDK de Application Insights. Si todavía no lo ha hecho, [agregue Application Insights a su aplicación](app-insights-asp-net.md).

Para supervisar la aplicación cuando se encuentra activa en producción, normalmente verá la telemetría de Application Insights en [Azure Portal](https://portal.azure.com), donde puede establecer alertas y aplicar eficaces herramientas de supervisión. Pero para la depuración, también puede buscar y analizar la telemetría en Visual Studio. Puede usar Visual Studio para analizar la telemetría desde su sitio de producción y desde los procesos de depuración que se ejecutan en el equipo de desarrollo. En este caso, puede analizar los procesos de depuración aunque todavía no haya configurado el SDK para enviar telemetría a Azure Portal. 

## <a name="run"></a> Depure el proyecto
Ejecute la aplicación web en modo de depuración local mediante F5. Abra distintas páginas para generar telemetría.

En Visual Studio, puede ver un recuento de los eventos que el módulo Application Insights ha registrado en el proyecto.

![En Visual Studio, el botón de Application Insights se muestra durante la depuración.](./media/app-insights-visual-studio/appinsights-09eventcount.png)

Haga clic en este botón para buscar la telemetría. 

## <a name="application-insights-search"></a>Búsqueda de Application Insights
La ventana de búsqueda de Application Insights muestra los eventos que se han registrado. (Si inició sesión en Azure al configurar Application Insights, puede buscar los mismos eventos en Azure Portal).

![Haga clic con el botón derecho en el proyecto y seleccione Application Insights, Búsqueda.](./media/app-insights-visual-studio/34.png)

> [!NOTE] 
> Después de seleccionar o anular la selección de filtros, haga clic en el botón de búsqueda al final del campo de búsqueda de texto.
>

La búsqueda de texto sin formato funciona en todos los campos de los eventos. Por ejemplo, buscar parte de la dirección URL de una página; o el valor de una propiedad, como la ciudad del cliente; o palabras específicas en un registro de seguimiento.

Haga clic en cualquier evento para ver sus propiedades con todo detalle.

Para las solicitudes a la aplicación web, puede hacer clic en el código.

![En Detalles de la solicitud, haga clic en el código](./media/app-insights-visual-studio/31.png)

También puede abrir los elementos relacionados para ayudar a diagnosticar las solicitudes con errores o excepciones.

![En Detalles de la solicitud, desplácese hasta los elementos relacionados](./media/app-insights-visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Ver solicitudes con error y excepciones
Informes de excepciones en la ventana de búsqueda. (En algunos tipos anteriores de aplicaciones de ASP.NET, tendrá que [configurar la supervisión de excepciones](app-insights-asp-net-exceptions.md) para ver las excepciones que el entorno administra).

Haga clic en una excepción para obtener un seguimiento de la pila. Si el código de la aplicación es abierto en Visual Studio, puede hacer clic para recorrer el seguimiento de la pila hasta dar con la línea correspondiente del código.

![Seguimiento de la pila de excepción](./media/app-insights-visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Ver resúmenes de solicitudes y excepciones en el código
En la línea de Code Lens, encima de cada método de controlador, puede ver un recuento de las solicitudes y excepciones registradas por Application Insights en las últimas 24 horas.

![Seguimiento de la pila de excepción](./media/app-insights-visual-studio/21.png)

> [!NOTE] 
> Code Lens muestra los datos de Application Insights solo si tiene [configurada la aplicación para enviar telemetría al portal de Application Insights](app-insights-asp-net.md).
>

[Más información acerca de Application Insights en Code Lens](app-insights-visual-studio-codelens.md)

## <a name="trends"></a>Tendencias
Tendencias es una herramienta para visualizar cómo se comporta la aplicación con el paso del tiempo. 

Elija **Explorar tendencias de telemetría** con el botón de la barra de herramientas de Application Insights o en la ventana Búsqueda de Application Insights. Seleccione una de las cinco consultas comunes para empezar. Puede analizar diferentes conjuntos de datos en función de los tipos de telemetría, los intervalos de tiempo y otras propiedades. 

Para detectar anomalías en los datos, elija una de las opciones de anomalía en la lista desplegable "Tipo de vista". Con las opciones de filtrado de la parte inferior de la ventana, resulta más sencillo centrarse en subconjuntos específicos de la telemetría.

![Tendencias](./media/app-insights-visual-studio/51.png)

[Más sobre Tendencias](app-insights-visual-studio-trends.md).

## <a name="local-monitoring"></a>Supervisión local
(Desde Visual Studio 2015 Update 2) Si no ha configurado el SDK para enviar datos de telemetría al portal de Application Insights (para que no haya ninguna clave de instrumentación en ApplicationInsights.config), la ventana diagnóstico muestra los datos de telemetría de la sesión de depuración más reciente. 

Esto es conveniente si ya ha publicado una versión anterior de la aplicación. No quiere que los datos de telemetría de las sesiones de depuración se mezclen con los datos de telemetría en el portal de Application Insights de la aplicación publicada.

También es útil si tiene [datos de telemetría personalizados](app-insights-api-custom-events-metrics.md) que desea depurar antes de enviarlos al portal.

* *En primer lugar, configuré totalmente Application Insights para enviar los datos de telemetría al portal. Pero ahora me gustaría ver los datos de telemetría solo en Visual Studio.*
  
  * En la configuración de la ventana de búsqueda, hay una opción para buscar diagnósticos locales, incluso si la aplicación envía datos de telemetría al portal.
  * Para detener el envío de datos de telemetría al portal, convierta en comentario la línea `<instrumentationkey>...` de ApplicationInsights.config. Cuando esté listo para enviar de nuevo datos de telemetría al portal, quite los comentarios.


## <a name="next-steps"></a>Pasos siguientes
|  |  |
| --- | --- |
| **[Incorporación de datos adicionales](app-insights-asp-net-more.md)**<br/>Supervise el uso, la disponibilidad, las dependencias y las excepciones. Integrar seguimientos de marcos de registro. Escribir telemetría personalizada. |![Visual Studio](./media/app-insights-visual-studio/64.png) |
| **[Trabajo con el portal de Application Insights](app-insights-dashboards.md)**<br/>Consulte paneles, eficaces herramientas de diagnóstico y análisis, alertas, un mapa activo de dependencias de la aplicación y datos de telemetría exportados. |![Visual Studio](./media/app-insights-visual-studio/62.png) |

