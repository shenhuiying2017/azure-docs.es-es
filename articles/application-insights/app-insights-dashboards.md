<properties
	pageTitle="Uso del portal de Application Insights"
	description="Métricas, búsqueda, paneles y configuración en el portal de Application Insights."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="07/30/2016"
	ms.author="awills"/>

# Navegación y paneles en el portal de Application Insights

Una vez [configurado Application Insights en su proyecto](app-insights-overview.md), aparecerán los datos de telemetría acerca del rendimiento y el uso de la aplicación en el recurso del proyecto Application Insights en el [portal de Azure](https://portal.azure.com).

## Encontrar la telemetría

En el [portal de Azure](https://portal.azure.com), busque el recurso de Application Insights que ha creado para la aplicación.

![Haga clic en Examinar, seleccione Application Insights y, después, seleccione su aplicación.](./media/app-insights-dashboards/00-start.png)

La página de información general proporciona la telemetría básica, además de vínculos a más elementos. El contenido depende del tipo de aplicación y se puede personalizar.


## Hoja de información general de la aplicación

La hoja de información general (página) de la aplicación muestra un resumen de las métricas de diagnóstico clave de la aplicación y sirve de puerta de enlace a las demás características del portal.

Haga clic en:

* **Cualquier gráfico o icono** para más información sobre lo que se muestra en el gráfico.
* **Configuración** para obtener acceso a hojas predefinidas de otras métricas, así como páginas de configuración.
* [**Explorador de métricas**](app-insights-metrics-explorer.md) para crear páginas de las métricas de su elección.
* [**Búsqueda**](app-insights-diagnostic-search.md) para investigar casos específicos de eventos como solicitudes, excepciones o seguimientos de registro.
* [**Análisis**](app-insights-analytics.md) para consultas eficaces sobre los datos de telemetría.
* [**Stream en vivo**](app-insights-metrics-explorer.md#live-stream) para obtener un conjunto fijo de métricas casi instantáneas, útiles al implementar una nueva compilación o depurar.


![Rutas principales para ver los datos de telemetría](./media/app-insights-dashboards/010-oview.png)


### Personalizar hoja de información general 

Elija lo que desea ver en la información general. En Personalizar, puede insertar títulos de sección, arrastrar iconos y gráficos, quitar elementos y agregar nuevos iconos y gráficos de la galería.

![Haga clic en Editar. Arrastre los iconos y gráficos. Agregue iconos de la galería. Luego haga clic en Hecho.](./media/app-insights-dashboards/020-customize.png)

## Paneles

Lo primero que verá después de iniciar sesión en el [Portal de Microsoft Azure](https://portal.azure.com) es un panel. Aquí puede agrupar los gráficos que le resultan más importantes de todos los recursos de Azure, incluidos los datos de telemetría desde [Visual Studio Application Insights](app-insights-overview.md).
 

![Panel personalizado.](./media/app-insights-dashboards/31.png)


1. Edite, cree y comparta paneles con la barra de herramientas del panel.
2. Vuelva al panel actual o cambie a otras vistas recientes.
3. Cambie los paneles.
4. Cree, edite y comparta paneles.
5. Mantenga el puntero sobre un icono y, a continuación, utilice la barra superior para moverlo, personalizarlo o quitarlo.

## Adición a un panel

Cuando esté consultando una hoja o un conjunto de gráficos especialmente interesante, puede anclar una copia al panel. Lo verá la próxima vez que regrese.

![Para anclar un gráfico, mantenga el puntero encima y haga clic en "..." en el encabezado.](./media/app-insights-dashboards/33.png)

1. Ancle un gráfico al panel. Una copia del gráfico aparece en el panel.
2. Ancle la hoja completa en el panel: aparecerá en el panel como un icono en el que puede hacer clic.
3. Haga clic en la esquina superior izquierda para volver al panel actual. A continuación, puede utilizar el menú desplegable para volver a la vista actual.

Observe que los gráficos se agrupan en iconos: un icono puede contener más de un gráfico. El icono se ancla entero al panel.

### Gráficos de Analytics

También puede [anclar gráficos de Analytics](app-insights-analytics-using.md#pin-to-dashboard) a un panel [compartido](#share-dashboards-with-your-team). Esto permite agregar gráficos de cualquier consulta arbitraria junto con las métricas estándares.

## Ajuste de un icono en el panel

Una vez que un icono se encuentra en el panel, es posible ajustarlo.

![Mantenga el mouse sobre un gráfico para poder editarlo.](./media/app-insights-dashboards/36.png)

1. Agregue un gráfico al icono.
2. Configure la métrica, la dimensión de agrupación y el estilo (tabla, gráfico) de un gráfico.
3. Configure las propiedades de intervalo de tiempo y filtrado de los gráficos en el icono. Puede establecer su propio intervalo de tiempo del gráfico o heredar el intervalo de tiempo del panel.
4. Establezca el título del icono.

Los iconos anclados desde hojas del explorador de métricas tienen más opciones de edición que los iconos anclados desde una hoja de información general.

El icono original que ancló no se ve afectado por las modificaciones.


## Cambio entre paneles

Puede guardar más de un panel y cambiar de uno a otro. Cuando se ancla un gráfico o una hoja, se agrega al panel actual.

![Para cambiar entre los paneles, haga clic en Panel y seleccione un panel guardado. Para crear un panel y guardarlo, haga clic en Nuevo. Para reorganizar, haga clic en Editar.](./media/app-insights-dashboards/32.png)

Por ejemplo, podría tener un panel para mostrarlo en pantalla completa en la sala de reuniones y otro para el desarrollo general.


En el panel, una hoja aparece como un icono. Haga clic en él para ir a la hoja. Un gráfico replica el gráfico en su ubicación original.

![Haga clic en un icono para abrir la hoja que representa.](./media/app-insights-dashboards/35.png)


## Uso compartido de paneles con su equipo

Cuando haya creado un panel, puede compartirlo con otros usuarios.

![En el encabezado del panel, haga clic en Compartir.](./media/app-insights-dashboards/41.png)

Aprenda más acerca de [Roles y control de acceso](app-insights-resources-roles-access-control.md).

## Settings

El botón Settings (Configuración) de la hoja de información general no sirve solo para la configuración, sino que también ofrece una puerta a varios tipos de gráficos.

* **Registros de auditoría**: revise las alertas y acciones que se han realizado en el recurso de aplicación.
* [Nueva solicitud de soporte técnico](app-insights-get-dev-support.md): opciones para obtener ayuda sobre cualquier aspecto de Azure. En Application Insights, otra forma de obtener ayuda es usar el botón de emoticono de la parte superior derecha de la ventana.
* [Mapa de aplicación](app-insights-dependencies.md#application-map): mapa activo que muestra los componentes de la aplicación, obtenidos de la información de dependencia.
* [Secuencia de métricas en directo](app-insights-metrics-explorer.md#live-metrics-stream): métricas clave con una latencia de 1 segundo aproximadamente, para la supervisión de una nueva versión.
* **Errores**: errores de solicitudes, errores de llamadas de dependencias y excepciones. [Hoja de métricas editable](app-insights-metrics-explorer.md).
* **Rendimiento**: tiempo de respuesta, tiempos de respuesta de dependencia. [Hoja de métricas editable](app-insights-metrics-explorer.md).
* [Servidores](app-insights-web-monitor-performance.md): contadores de rendimiento. Está disponible si se [instala el Monitor de estado](app-insights-monitor-performance-live-website-now.md).
* **Explorador**: vista de página y rendimiento de AJAX. Está disponible si se [instrumentan las páginas web](app-insights-javascript.md).
* **Uso**: vista de página, recuentos de usuarios y sesiones. Está disponible si se [instrumentan las páginas web](app-insights-javascript.md).
* **Introducción**: tutorial en línea.
* **Propiedades**: clave de instrumentación, suscripción e identificador de recursos.
* [Disponibilidad](app-insights-monitor-web-app-availability.md): configuración y resultados de pruebas web.
* [Alertas](app-insights-alerts.md): configuración de alertas de métricas.
* [Exportación continua](app-insights-export-telemetry.md): configuración de la exportación de los datos de telemetría a Almacenamiento de Azure.
* [Pruebas de rendimiento](app-insights-monitor-web-app-availability.md#performance-tests): configuración de una carga simétrica en el sitio web.
* [Cuotas y precios](app-insights-pricing.md) y [muestreo de ingesta](app-insights-sampling.md).
* **Acceso a API**: se utiliza actualmente para crear [anotaciones de versión](app-insights-annotations.md) y para la API de acceso a datos.
* [**Elementos de trabajo**](app-insights-diagnostic-search.md#create-work-item): conéctese a un sistema de seguimiento de trabajos de modo que pueda crear errores mientras se inspeccionan los datos de telemetría.
* [**Usuarios**](app-insights-resources-roles-access-control.md): administre quién tiene acceso al recurso de la aplicación.
* [**Roles**](app-insights-resources-roles-access-control.md): administre qué pueden hacer los usuarios.
* [**Etiquetas**](..\resource-group-using-tags.md): organice los recursos de Azure.
* [**Bloqueos**](..\resource-group-lock-resources.md): bloquee recursos de Azure.
* [**Exportar plantilla**](app-insights-powershell.md): exporte una definición del recurso de Azure de forma que pueda usarla como plantilla para crear nuevos recursos.

## Pasos siguientes

||
|---|---
|[Explorador de métricas](app-insights-metrics-explorer.md)<br/>Filtre y segmente las métricas|![Ejemplo de búsqueda](./media/app-insights-dashboards/64.png)
|[Búsqueda de diagnóstico](app-insights-diagnostic-search.md)<br/>Busque y examine eventos y eventos relacionados, y cree errores. |![Ejemplo de búsqueda](./media/app-insights-dashboards/61.png)
|[Analytics](app-insights-analytics.md)<br/>Lenguaje de consulta eficaz| ![Ejemplo de búsqueda](./media/app-insights-dashboards/63.png)

<!---HONumber=AcomDC_0817_2016-->