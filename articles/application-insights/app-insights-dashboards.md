<properties
	pageTitle="Paneles en el portal de Application Insights"
	description="Muestre datos de telemetría desde varios componentes de la aplicación en un panel."
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
	ms.date="04/25/2016"
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

* **Cualquier gráfico o icono** para ver más detalles.
* **Configuración** para acceder a las páginas predefinidas de otras métricas.
* [**Explorador de métricas**](app-insights-metrics-explorer.md) para crear páginas de las métricas de su elección.
* [**Búsqueda**](app-insights-diagnostic-search.md) para investigar casos específicos de eventos como solicitudes, excepciones o seguimientos de registro.
* [**Análisis**](app-insights-analytics.md) para consultas eficaces sobre los datos de telemetría.


![Rutas principales para ver los datos de telemetría](./media/app-insights-dashboards/010-oview.png)


### Personalizar hoja de información general 

Elija lo que desea ver en la información general. En Personalizar, puede insertar títulos de sección, arrastrar iconos y gráficos, quitar elementos y agregar nuevos iconos y gráficos de la galería.

![Haga clic en Editar. Arrastre los iconos y gráficos. Agregue iconos de la galería. Luego haga clic en Hecho.](./media/app-insights-dashboards/020-customize.png)

## Paneles

Lo primero que verá después de iniciar sesión en el [Portal de Microsoft Azure](https://portal.azure.com) es un panel. Aquí puede agrupar los gráficos que le resultan más importantes de todos los recursos de Azure, incluidos los datos de telemetría desde [Visual Studio Application Insights](app-insights-overview.md).
 

![Panel personalizado.](./media/app-insights-dashboards/30.png)

1. Haga clic en la esquina superior en cualquier momento para volver al panel.
2. Haga clic en un gráfico o en un icono del panel para ver más detalles acerca de sus datos.
3. Utilice la barra de navegación para obtener una visión completa de todos los recursos.
4. Edite, cree y comparta paneles con la barra de herramientas del panel.

## Adición a un panel

Cuando esté consultando una hoja o un conjunto de gráficos especialmente interesante, puede anclar una copia al panel. Lo verá la próxima vez que regrese.

![Para anclar un gráfico, mantenga el puntero encima y haga clic en "..." en el encabezado.](./media/app-insights-dashboards/33.png)

Observe que los gráficos se agrupan en iconos: un icono puede contener más de un gráfico. El icono se ancla entero al panel.

## Ajuste de un icono en el panel

Una vez que un icono se encuentra en el panel, es posible ajustarlo.

![Mantenga el mouse sobre un gráfico para modificarlo.](./media/app-insights-dashboards/36.png)

1. Agregue un gráfico al icono. 
2. Configure la métrica, la dimensión de agrupación y el estilo (tabla, gráfico) de un gráfico.
3. Configure las propiedades de intervalo de tiempo y filtrado de los gráficos en el icono.
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

Obtenga información acerca de [Recursos, roles y control de acceso en Application Insights](app-insights-resources-roles-access-control.md).

<!---HONumber=AcomDC_0427_2016-->