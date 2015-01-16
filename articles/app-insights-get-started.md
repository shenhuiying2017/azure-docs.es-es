<properties title="Application Insights" pageTitle="Application Insights: comience a supervisar el estado y el uso de la aplicación" description="Analice el uso, la disponibilidad y el rendimiento de su aplicación web de Microsoft Azure o local con Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Application Insights: comience a supervisar el estado y el uso de la aplicación

*Application Insights se encuentra en su versión de vista previa.*

Application Insights permite supervisar una aplicación activa en cuanto a:

* **Disponibilidad**: las URL se prueban cada pocos minutos desde distintos lugares del mundo.
* **Rendimiento**: detecte y diagnostique excepciones y problemas de rendimiento.
* **Uso**: descubra cómo interactúan los usuarios con la aplicación para mejorarla.

La configuración es muy sencilla y obtendrá resultados en cuestión de minutos. Actualmente, se ofrece compatibilidad con las aplicaciones web de ASP.NET (en servidores particulares o Azure).


## Primeros pasos

Comience con cualquier combinación, en cualquier orden, de los puntos de entrada a la izquierda de este gráfico. Elija las rutas que mejor le vengan. Si está desarrollando una aplicación web ASP.NET, empiece por agregar Application Insights a su proyecto web: es fácil agregar los demás bits más adelante.

Necesitará una cuenta de [Microsoft Azure](http://azure.com) (a menos que utilice la versión VSO).

<table >
<tr valign="top"><th>Qué necesita</th><th colspan="2">Qué hace</th><th>Qué obtiene</th></tr>
<tr valign="top"><td>Obtener el análisis de rendimiento y uso para mi aplicación de ASP.NET</td><td colspan="2"><a href="../app-insights-start-monitoring-app-health-usage/">Agregar Application Insights a su proyecto web</a></td><td>Métricas de rendimiento: recuentos de carga, tiempos de respuesta...</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Enviar eventos y métrica desde el código de servidor</a></td><td>Análisis de negocios personalizados</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Enviar telemetría de seguimiento y excepción desde el servidor, o capturar otros datos de registro de terceros.</td><td>Diagnóstico de aplicaciones de servidor. Buscar y filtrar datos del registro.</a></td></tr>
<tr valign="top"><td>Obtener análisis de uso para mis páginas web (en cualquier plataforma)</td><td colspan="2"><a href="../app-insights-web-track-usage/">Insertar el script de AI en sus páginas web</a></td><td>Análisis de uso: vistas de página, usuarios habituales, recuentos de sesión</td></tr>
<tr valign="top"><td></td><td>&nbsp;&nbsp;</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Escribir llamadas de eventos y métricas en los scripts de la página web</a></td><td>Personalizar análisis de experiencia de usuario</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Escribir llamadas de seguimiento y diagnóstico en los scripts de página web</a></td><td>Buscar y filtrar datos de registro.</td></tr>
<tr valign="top"><td>Diagnosticar problemas en una aplicación ASP.NET que se está ejecutando en mi servidor web</td><td colspan="2"><a href="../app-insights-monitor-performance-live-website-now/">Instalar el monitor de estado en su servidor web</a></td><td>Duraciones y recuentos de llamadas de dependencia: CPU, contadores de memoria y red; recuentos de carga, tiempos de respuesta</td></tr>
<tr valign="top"><td>Supervisar la disponibilidad de las páginas web</td><td colspan="2"><a href="../app-insights-monitor-web-app-availability/">Configurar pruebas web en Application Insights</a></td><td>Monitor y alertas de disponibilidad</td></tr>
<tr valign="top"><td>Obtener el análisis de rendimiento y uso de aplicaciones de Windows Phone, aplicaciones de la Tienda Windows o sitios Web de Java</td><td colspan="2"><a href="http://msdn.microsoft.com/library/dn481095.aspx">Por ahora, utilice la versión anterior de VSO de Application Insights</a></td><td>Análisis de rendimiento y uso. <a href="http://msdn.microsoft.com/library/dn793604.aspx">Estamos compilando gradualmente las características en la versión de Azure.</a></td></tr>
</table>


## <a name="video"></a>Vídeos

#### Introducción

> [AZURE.VIDEO application-insights-introduction]

#### Primeros pasos

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]


