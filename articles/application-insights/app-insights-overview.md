<properties 
	pageTitle="¿Qué es Application Insights?" 
	description="Realice el seguimiento del uso y el rendimiento de su aplicación para dispositivo o web activa. Detecte, evalúe y diagnostique problemas. Supervise y mejore continuamente el éxito con los usuarios." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="awills"/>
 
# ¿Qué es Application Insights?

Visual Studio Application Insights permite realizar el seguimiento del rendimiento y el uso de su aplicación web o para dispositivo activa.

* [Detecte, evalúe y diagnostique][detect] problemas de rendimiento y corríjalos antes de que se den cuenta la mayoría de los usuarios.
 *  Alertas sobre cambios en el rendimiento o bloqueos.
 *  Métricas para ayudar a diagnosticar problemas de rendimiento, como tiempos de respuesta, uso de CPU o seguimiento de dependencias.
 *  Pruebas de disponibilidad para aplicaciones web.
 *  Informes y alertas de excepciones y bloqueos
 *  Búsqueda eficaz de registros de diagnósticos (lo que incluye seguimientos de registro de sus marcos de registro favoritos).
* [Continúe mejorando su aplicación][knowUsers] al comprender lo que hacen con ella sus usuarios. 
 * Recuentos de vistas de página, usuarios nuevos y existentes y otros análisis del uso principal
 * Realice el seguimiento de sus propios eventos para evaluar los patrones de uso y el éxito de cada característica.

## ¿Cómo funciona?

Instale un pequeño SDK en su aplicación y configure una cuenta en el portal de Application Insights. El SDK supervisa la aplicación y envía los datos de telemetría al portal. El portal le muestra gráficos estadísticos y ofrece eficaces herramientas de búsqueda para ayudarle a diagnosticar los problemas.

![El SDK de Application Insights de su aplicación envía datos de telemetría al recurso de Application Insights en el Portal de Azure.](./media/app-insights-overview/01-scheme.png)

El SDK cuenta con varios módulos que recopilan datos de telemetría, con el fin de contar, por ejemplo, usuarios, sesiones y el rendimiento. También puede escribir su propio código personalizado para enviar datos de telemetría al portal. La telemetría personalizada resulta especialmente útil para realizar el seguimiento de los casos de usuario: puede contar eventos como los clics de botón, la consecución de objetivos concretos o los errores de los usuarios.

Para servidores ASP.NET y aplicaciones web de Azure, también puede instalar el [Monitor de estado][redfield], que tiene dos usos. Así, le permite:

* Supervisar una aplicación web sin necesidad de volverla a compilar o instalar.
* Realizar el seguimiento de llamadas a módulos dependientes.

## ¿Con qué clase de aplicaciones funciona?

Actualmente hay SDK para:

* Aplicaciones web
 * [ASP.NET][greenbrown] en Azure o en el servidor IIS
 * [Java][java] en JRE 
 * [Páginas web][client]: HTML + JavaScript
* Aplicaciones para dispositivo
 * [Windows][windows]
 * [iOS][ios]
 * [Android][android]
 * Cordova
 * [Otras plataformas][platforms]


## ¿Cómo se usa?

* [Detección, evaluación y diagnóstico de problemas][detect]
* [Análisis del uso de la aplicación][knowUsers]


## ¿Qué se necesita para usarlo?

* Una suscripción a Microsoft Azure. Application Insights es uno de los muchos servicios en la nube de Azure, entre los que también se incluyen sitios web, bases de datos, máquinas virtuales y muchos más. (Pero puede usar Application Insights para supervisar cualquier aplicación: la aplicación no tiene que ejecutarse en Azure.) 

 * Puede que su organización tenga una cuenta.


## ¿Cómo empiezo?

Elija su plataforma en el menú de introducción de la izquierda.

En todos los casos, el procedimiento es el siguiente:

1. Cree un recurso de Application Insights en [Azure][portal] (y obtenga su clave de instrumentación).
2. Instrumente la aplicación con el SDK adecuado (y configúrela con la clave de instrumentación).
3. Ejecute la aplicación en modo de depuración o en directo.
4. Vea los resultados en el recurso en [Azure][portal].

En algunos casos, hay un complemento disponible para su IDE (por ejemplo, Visual Studio o Eclipse) que realiza los dos primeros pasos por usted. Sin embargo, siempre puede recorrer el procedimiento manualmente.

Si su aplicación es un sitio o un servicio web, existen algunas variaciones y adiciones opcionales en el procedimiento básico:

* Agregue un SDK a la aplicación de lado servidor y al [dispositivo][windows] cliente o la [página web][client]. Los datos de telemetría se combinan en el portal de modo que puede correlacionar eventos en los dos extremos.
* Configure pruebas web para supervisar la disponibilidad de su sitio desde distintos puntos del mundo.
* Instrumente una aplicación del lado servidor sin que sea necesario volverla a compilar o instalar. Esta opción está disponible para [servidores IIS][redfield] y [aplicaciones web de Azure][azure].
* Supervise las llamadas de dependencia que realiza su aplicación a otros componentes, por ejemplo a las bases de datos, o bien a través de las API de REST. Esta opción está disponible para [servidores IIS][redfield] y [aplicaciones web de Azure][azure].


<!--Link references-->

[android]: app-insights-android.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[ios]: app-insights-ios.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

<!---HONumber=58--> 