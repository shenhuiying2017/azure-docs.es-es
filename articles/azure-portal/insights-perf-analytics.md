<properties 
	pageTitle="Supervisión del rendimiento de la aplicación" 
	description="Representa en un gráfico el tiempo de carga y respuesta, así como la información de dependencia. Además, define alertas sobre el rendimiento." 
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="awills"/>

# Supervisión del rendimiento de la aplicación

En el[portal de Azure](http://portal.azure.com), puede configurar la supervisión para recopilar estadísticas y detalles sobre las dependencias de la aplicación en las aplicaciones web o las máquinas virtuales.

Azure admite la supervisión del rendimiento de las aplicaciones (o *APM*) haciendo uso de las *extensiones.*. Dichas extensiones se instalan en la aplicación, donde recopilan los datos e informan a los servicios de supervisión.

## Cómo habilitar una extensión 1.

1. Haga clic en **Examinar** y seleccione la aplicación web o la máquina virtual que desee instrumentar.

2. Haga clic en el icono **Supervisión de aplicaciones** en **Supervisión**.

3. Elija el proveedor de extensión que le gustaría utilizar, como **Application Insights** o **New Relic**.

![APM de las aplicaciones web](./media/insights-perf-analytics/05-extend.png)

O, si está usando una máquina virtual:

![Máquina virtual](./media/insights-perf-analytics/10-vm1.png)


## Exploración de los datos

Use la aplicación para generar telemetría.

1. A continuación, desde la hoja de la máquina virtual o la aplicación web, podrá ver la extensión instalada.
2. Haga clic en la fila que representa la aplicación para acceder a ese proveedor:![Hacer clic en Actualizar](./media/insights-perf-analytics/06-overview.png)

También puede utilizar **Examinar** para ir directamente al componente Application Insights o a la cuenta de New Relic que utilizó previamente.

Una vez que obtenga la hoja, para Application Insights, por ejemplo, puede realizar estas acciones: - Open Performance:

![En la hoja de descripción general de Application Insights, haga clic en el icono Rendimiento](./media/insights-perf-analytics/07-dependency.png)

- Acceda a las solicitudes individuales:

![En la cuadrícula, haga clic en una dependencia para ver las solicitudes relacionadas.](./media/insights-perf-analytics/08-requests.png)

- En este ejemplo, se muestra la cantidad de tiempo gastado en una dependencia SQL, incluido el número de llamadas SQL y las estadísticas relacionadas, por ejemplo, la duración media y la desviación estándar. 

![](./media/insights-perf-analytics/01-example.png) 

### Los requisitos adicionales de Application Insights

New Relic se pueden instalar automáticamente sin ningún tipo de instrumentación adicional, pero Application Insights tiene un requisito adicional.

![En el cuadro de diálogo Nuevo proyecto, marque la opción Agregar Application Insights](./media/insights-perf-analytics/03-add.png)

Cuando se le pide que inicie sesión, use las credenciales para la cuenta de Azure.

## Pasos siguientes

* [Supervisar las métricas de estado de servicio ](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio esté disponible y ofrezca capacidad de respuesta.
* [Habilite la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas de alta frecuencia detalladas sobre el servicio.
* [Recibir notificaciones de alerta](insights-receive-alert-notifications.md) cada vez que se produzca un evento operativo o cuando una métrica supere un umbral establecido.
* Uso [Application Insights para las páginas web y aplicaciones de JavaScript](../app-insights-web-track-usage.md) para obtener análisis de cliente sobre los exploradores que visitan una página web.
* [Supervise la disponibilidad y la capacidad de respuesta de cualquier página web](../app-insights-monitor-web-app-availability.md) con Application Insights para que pueda averiguar si la página está inactiva.
 