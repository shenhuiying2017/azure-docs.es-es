<properties
	pageTitle="Supervisión del rendimiento de aplicaciones web de Azure | Microsoft Azure"
	description="Carga y tiempo de respuesta de gráfico, información de dependencia y establecer alertas en el rendimiento."
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="awills"/>

# Supervisión del rendimiento de aplicaciones web de Azure

En el [Portal de Azure](https://portal.azure.com), puede configurar la supervisión para que recopile estadísticas y detalles sobre las dependencias de las aplicaciones en las [aplicaciones web](../app-service-web/app-service-web-overview.md) o [máquinas virtuales](../virtual-machines/virtual-machines-linux-about.md) de Azure.

Azure permite usar extensiones para admitir Supervisión de rendimiento de aplicaciones (APM). Dichas extensiones se instalan en la aplicación, donde recopilan datos e informan a los servicios de supervisión.

**Application Insights** y **New Relic** son dos de las extensiones de supervisión de rendimiento que hay disponibles. Para usarlas, instale un agente en tiempo de ejecución. Con Application Insights, también existe la posibilidad de generar el código con un SDK. El SDK permite escribir código para supervisar el uso y el rendimiento de la aplicación con más detalle.

## Application Insights

### (Opcional) Volver a compilar la aplicación con el SDK...

Application Insights puede proporcionar una telemetría más detallada instalando un SDK en la aplicación.

En Visual Studio (2013 Update 2 o posteriores), agregue el SDK de Application Insights al proyecto.

![Haga clic con el botón derecho y elija Agregar Application Insights.](./media/insights-perf-analytics/03-add.png)

Cuando se la pide que inicie sesión, use las credenciales de su cuenta de Azure.

La operación tiene dos efectos:

1. Crea un recurso de Application Insights en Azure, donde se almacenan, analizan y muestran los datos de telemetría.
2. Agrega el paquete NuGet de Application Insights al código y lo configura para enviar los datos de telemetría al recurso de Azure.

Para probar la telemetría, ejecute la aplicación en el equipo de desarrollo (F5) o vuelva a publicarla directamente.

El SDK proporciona una API para que pueda [escribir telemetría personalizada](../application-insights/app-insights-api-custom-events-metrics.md) para realizar un seguimiento del uso.

### ...o configurar un recurso manualmente

Si no agregó el SDK a Visual Studio, debe configurar el recurso de Application Insights en Azure, donde se almacenan, analizan y muestran los datos de telemetría.

![Haga clic en Agregar, Servicios para desarrolladores, Application Insights. Elija el tipo de aplicación ASP.NET.](./media/insights-perf-analytics/01-new.png)


## Habilitación de extensiones

1. Vaya a la hoja de control de la aplicación web o la máquina virtual que quiere instrumentar.

2. Agregue toda la extensión de Application Insights o New Relic.

    Si va a instrumentar una aplicación web:

![Configuración, Extensiones, Agregar, Application Insights](./media/insights-perf-analytics/05-extend.png)

O bien, si utiliza una máquina virtual:

![Haga clic en el icono Análisis](./media/insights-perf-analytics/10-vm1.png)



## Exploración de los datos

1. Abra el recurso de Application Insights (directamente mediante Examinar o con la herramienta de supervisión de rendimiento de la aplicación web).

2. Haga clic en cualquier gráfico para obtener información más detallada:

    ![En la hoja de información general de Application Insights, haga clic en un gráfico.](./media/insights-perf-analytics/07-dependency.png)

    También puede [personalizar las hojas de métricas](../application-insights/app-insights-metrics-explorer.md).

3. Siga haciendo clic para ver los eventos individuales y sus propiedades:

    ![Haga clic en un tipo de evento para abrir una búsqueda filtrada en ese tipo.](./media/insights-perf-analytics/08-requests.png)

    Observe el vínculo "..." para abrir todas las propiedades.

    También puede [personalizar las búsquedas](../application-insights/app-insights-diagnostic-search.md).

Para realizar búsquedas más eficaces sobre los datos de telemetría, use el [lenguaje de consulta Analytics](../application-insights/app-insights-analytics-tour.md).


## Preguntas y respuestas

¿Cómo cambio para enviar datos a un recurso de Application Insights diferente?

* *Si agregó Application Insights a su código en Visual Studio:* haga clic con el botón derecho en el proyecto, elija **Application Insights > Configurar** y elija el recurso que quiera. Tiene la opción de crear un nuevo recurso. Vuelva a compilar e implementar.
* *En caso contrario:* en Azure, abra la hoja de control de la aplicación web y abra **Herramientas > Extensiones**. Elimine la extensión de Application Insights. Después, abra **Herramientas > Rendimiento**, 'haga clic aquí', elija Application Insights y el recurso que quiera. (Si quiere crear un nuevo recurso de Application Insights, haga esto primero).


## Pasos siguientes

* [Supervise las métricas del estado del servicio](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y responde adecuadamente.
* [Habilite la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en su servicio.
* [Reciba notificaciones de alerta](insights-receive-alert-notifications.md) cada vez que se produzcan eventos de operaciones o las métricas traspasen un umbral.
* Use [aplicaciones y páginas web de Application Insights para JavaScript](../application-insights/app-insights-web-track-usage.md) para obtener la telemetría del cliente de los exploradores que visitan una página web.
* [Configure pruebas web de disponibilidad](../application-insights/app-insights-monitor-web-app-availability.md) para recibir una alerta si el sitio deja de estar activo.

<!---HONumber=AcomDC_0803_2016-->