---
title: "Paneles y navegación en Azure Application Insights | Microsoft Docs"
description: "Cree vistas de los gráficos APM y consultas principales."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: 9987f04e7e71df5fe10c8bc209a390cb940ec4f2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navegación y paneles en el portal de Application Insights
Una vez [configurado Application Insights en su proyecto](app-insights-overview.md), aparecerán los datos de telemetría acerca del rendimiento y el uso de la aplicación en el recurso del proyecto Application Insights en [Azure Portal](https://portal.azure.com).

## <a name="find-your-telemetry"></a>Encontrar la telemetría
Inicie sesión en [Azure Portal](https://portal.azure.com), vaya al recurso de Application Insights que ha creado para la aplicación.

![Haga clic en Examinar, seleccione Application Insights y, después, seleccione su aplicación.](./media/app-insights-dashboards/00-start.png)

La hoja de información general (página) de la aplicación muestra un resumen de las métricas de diagnóstico clave de la aplicación y sirve de puerta de enlace a las demás características del portal.

![Rutas principales para ver los datos de telemetría](./media/app-insights-dashboards/010-oview.png)

Puede personalizar cualquiera de los gráficos y cuadrículas y anclarlos a un panel. De este modo, se puede reunir la telemetría clave de aplicaciones diferentes en un panel central.

## <a name="dashboards"></a>Paneles
Lo primero que verá después de iniciar sesión en el [Portal de Microsoft Azure](https://portal.azure.com) es un panel. Aquí puede agrupar los gráficos que le resultan más importantes de todos los recursos de Azure, incluidos los datos de telemetría desde [Azure Application Insights](app-insights-overview.md).

![Panel personalizado.](./media/app-insights-dashboards/31.png)

1. **Vaya a los recursos específicos** como la aplicación en Application Insights: utilice la barra de la izquierda.
2. **Vuelva al panel actual** o cambie a otras vistas recientes: use el menú desplegable situado en la parte superior izquierda.
3. **Cambie entre los paneles**: use el menú desplegable en el título del panel
4. **Cree, edite y comparta paneles** con la barra de herramientas del panel.
5. **Edite el panel**: mantenga el puntero sobre un icono y, a continuación, utilice la barra superior para moverlo, personalizarlo o quitarlo.

## <a name="add-to-a-dashboard"></a>Adición a un panel
Cuando esté consultando una hoja o un conjunto de gráficos especialmente interesante, puede anclar una copia al panel. Lo verá la próxima vez que regrese.

![Para anclar un gráfico, mantenga el puntero encima y haga clic en "..." en el encabezado.](./media/app-insights-dashboards/33.png)

1. Ancle un gráfico al panel. Una copia del gráfico aparece en el panel.
2. Ancle la hoja completa en el panel: aparecerá en el panel como un icono en el que puede hacer clic.
3. Haga clic en la esquina superior izquierda para volver al panel actual. A continuación, puede utilizar el menú desplegable para volver a la vista actual.

Observe que los gráficos se agrupan en iconos: un icono puede contener más de un gráfico. El icono se ancla entero al panel.

El gráfico se actualiza automáticamente con una frecuencia que depende del intervalo de tiempo del gráfico:

* Intervalo de tiempo hasta una hora: actualizar cada 5 minutos
* Intervalo de tiempo de 1 a 24 horas: actualizar cada 15 minutos
* Tiempo de intervalo superior a 24 horas: (intervalo de tiempo)/60.

### <a name="pin-any-query-in-analytics"></a>Ancle cualquier consulta en Analytics
También puede [anclar gráficos de Analytics](app-insights-analytics-using.md#pin-to-dashboard) a un panel [compartido](#share-dashboards-with-your-team). Esto permite agregar gráficos de cualquier consulta arbitraria junto con las métricas estándares. 

Los resultados se recalculan automáticamente cada hora. Haga clic en el icono de actualización en el gráfico para recalcular inmediatamente. (Cuando el explorador se actualiza, no se recalculan los resultados).

## <a name="adjust-a-tile-on-the-dashboard"></a>Ajuste de un icono en el panel
Una vez que un icono se encuentra en el panel, es posible ajustarlo.

![Mantenga el mouse sobre un gráfico para poder editarlo.](./media/app-insights-dashboards/36.png)

1. Agregue un gráfico al icono.
2. Configure la métrica, la dimensión de agrupación y el estilo (tabla, gráfico) de un gráfico.
3. Arrastre por el diagrama para acercar; haga clic en el botón Deshacer para restablecer el intervalo de tiempo; establezca las propiedades de filtro para los gráficos del icono.
4. Establezca el título del icono.

Los iconos anclados desde hojas del explorador de métricas tienen más opciones de edición que los iconos anclados desde una hoja de información general.

El icono original que ancló no se ve afectado por las modificaciones.

## <a name="switch-between-dashboards"></a>Cambio entre paneles
Puede guardar más de un panel y cambiar de uno a otro. Cuando se ancla un gráfico o una hoja, se agrega al panel actual.

![Para cambiar entre los paneles, haga clic en Panel y seleccione un panel guardado. Para crear un panel y guardarlo, haga clic en Nuevo. Para reorganizar, haga clic en Editar.](./media/app-insights-dashboards/32.png)

Por ejemplo, podría tener un panel para mostrarlo en pantalla completa en la sala de reuniones y otro para el desarrollo general.

En el panel, una hoja aparece como un icono. Haga clic en él para ir a la hoja. Un gráfico replica el gráfico en su ubicación original.

![Haga clic en un icono para abrir la hoja que representa.](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Uso compartido de paneles
Cuando haya creado un panel, puede compartirlo con otros usuarios.

![En el encabezado del panel, haga clic en Compartir.](./media/app-insights-dashboards/41.png)

Aprenda más acerca de [Roles y control de acceso](app-insights-resources-roles-access-control.md).

## <a name="app-navigation"></a>Navegación en la aplicación
La hoja de información general es la puerta de enlace para más información acerca de la aplicación.

* **Cualquier gráfico o icono**: haga clic en cualquier icono o gráfico para ver más detalles acerca de lo que muestra.

### <a name="overview-blade-buttons"></a>Botones de la hoja de información general
![Barra de navegación superior de hoja de información general](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Explorador de métricas**](app-insights-metrics-explorer.md): cree sus propios gráficos de rendimiento y uso.
* [**Búsqueda**](app-insights-diagnostic-search.md): investigue casos específicos de eventos como solicitudes, excepciones o seguimientos de registro.
* [**Analytics**](app-insights-analytics.md): consultas eficaces sobre los datos de telemetría.
* **Intervalo de tiempo**: ajuste el intervalo mostrado por todos los gráficos de la hoja.
* **Eliminar**: elimine el recurso de Application Insights para esta aplicación. Debe también quitar los paquetes de Application Insights del código de la aplicación, o editar la [clave de instrumentación](app-insights-create-new-resource.md#copy-the-instrumentation-key) de la aplicación para dirigir la telemetría a un recurso de Application Insights diferente.

### <a name="essentials-tab"></a>Pestaña Essentials
* [Clave de instrumentación](app-insights-create-new-resource.md#copy-the-instrumentation-key): identifica este recurso de la aplicación.
* Precios: hacen que las características estén disponibles y establecen límites de volumen.

### <a name="app-navigation-bar"></a>Barra de navegación de la aplicación
![Barra de navegación izquierda](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Información general**: vuelve a la hoja de información general de la aplicación.
* **Registro de actividad**: alertas y eventos administrativos de Azure.
* [**Control de acceso**](app-insights-resources-roles-access-control.md): proporciona acceso a los miembros del equipo y a otros elementos.
* [**Etiquetas**](../azure-resource-manager/resource-group-using-tags.md): usa etiquetas para agrupar la aplicación con otras.

INVESTIGAR

* [**Mapa de aplicación**](app-insights-app-map.md): mapa activo que muestra los componentes de la aplicación, obtenidos de la información de dependencia.
* [**Detección inteligente**](app-insights-proactive-diagnostics.md): revise las alertas recientes de rendimiento.
* [**Stream en vivo**](app-insights-live-stream.md): un conjunto fijo de métricas casi instantáneas, útiles al implementar una nueva compilación o depurar.
* [**Disponibilidad / pruebas web**](app-insights-monitor-web-app-availability.md): envíe solicitudes regulares a la aplicación web desde cualquier parte del mundo*.
* [**Errores, rendimiento**](app-insights-web-monitor-performance.md): excepciones, tasas de error y tiempos de respuesta para las solicitudes realizadas a la aplicación y para las solicitudes realizadas desde la aplicación a las [dependencias](app-insights-asp-net-dependencies.md).
* [**Rendimiento**](app-insights-web-monitor-performance.md): tiempo de respuesta, tiempos de respuesta de dependencia.
* [Servidores](app-insights-web-monitor-performance.md) : contadores de rendimiento. Está disponible si se [instala el Monitor de estado](app-insights-monitor-performance-live-website-now.md).
* **Explorador** : vista de página y rendimiento de AJAX. Está disponible si se [instrumentan las páginas web](app-insights-javascript.md).
* **Uso** : vista de página, recuentos de usuarios y sesiones. Está disponible si se [instrumentan las páginas web](app-insights-javascript.md).

CONFIGURAR

* **Introducción** : tutorial en línea.
* **Propiedades** : clave de instrumentación, suscripción e identificador de recursos.
* [Alertas](app-insights-alerts.md) : configuración de alertas de métricas.
* [Exportación continua](app-insights-export-telemetry.md) : configuración de la exportación de los datos de telemetría a Almacenamiento de Azure.
* [Pruebas de rendimiento](app-insights-monitor-web-app-availability.md#performance-tests) : configuración de una carga simétrica en el sitio web.
* [Cuotas y precios](app-insights-pricing.md) y [muestreo de ingesta](app-insights-sampling.md).
* **Acceso a API**: cree [anotaciones de versión](app-insights-annotations.md) y para la API de acceso a datos.
* [**Elementos de trabajo**](app-insights-diagnostic-search.md#create-work-item): conéctese a un sistema de seguimiento de trabajos de modo que pueda crear errores mientras se inspeccionan los datos de telemetría.

CONFIGURACIÓN

* [**Bloqueos**](../azure-resource-manager/resource-group-lock-resources.md): bloquee recursos de Azure.
* [**Script de automatización**](app-insights-powershell.md): exporte una definición del recurso de Azure de forma que pueda usarla como plantilla para crear nuevos recursos.


## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Pasos siguientes

|  |  |
| --- | --- |
| [Explorador de métricas](app-insights-metrics-explorer.md)<br/>Filtre y segmente las métricas |![Ejemplo de búsqueda](./media/app-insights-dashboards/64.png) |
| [Búsqueda de diagnóstico](app-insights-diagnostic-search.md)<br/>Busque y examine eventos y eventos relacionados, y cree errores. |![Ejemplo de búsqueda](./media/app-insights-dashboards/61.png) |
| [Analytics](app-insights-analytics.md)<br/>Lenguaje de consulta eficaz |![Ejemplo de búsqueda](./media/app-insights-dashboards/63.png) |
