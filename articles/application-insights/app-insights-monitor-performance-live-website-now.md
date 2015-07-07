<properties 
	pageTitle="Diagnostique problemas de rendimiento en un sitio web en ejecución" 
	description="Supervise el rendimiento de un sitio web sin volver a implementarlo. Úselo de forma independiente o con el SDK de Application Insights para obtener la telemetría de dependencia." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="awills"/>
 

# Instalación del Monitor de estado de Application Insights para supervisar el rendimiento del sitio web

*Application Insights se encuentra en su versión de vista previa.*

El Monitor de estado de Application Insights de Visual Studio permite diagnosticar las excepciones y los problemas de rendimiento de las aplicaciones web que se ejecutan en cualquier servidor IIS. Basta con que lo instale en el servidor web IIS y empezará a utilizar las aplicaciones web de ASP.NET que encuentre ahí, enviando datos al portal de Application Insights para que usted pueda buscarlos y analizarlos. Puede instalarlo en cualquier servidor físico o virtual al que tenga acceso de administrador.

![gráficos de ejemplo](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Puede optar entre tres formas de aplicar Application Insights a sus aplicaciones web IIS:

* **Tiempo de compilación:** [agregue el SDK de Application Insights][greenbrown] al código de la aplicación web. El resultado obtenido es el siguiente:
 * Un intervalo de telemetría estándar de diagnóstico y uso. 
 * Y puede usar la [API de Application Insights][api] si desea escribir su propia telemetría para realizar un seguimiento del uso o diagnosticar los problemas.
* **Tiempo de ejecución:** utilice el Monitor de estado para instrumentar su aplicación web en el servidor.
 * Supervise las aplicaciones web que ya se están ejecutando, sin necesidad de volver a compilarlas o publicarlas.
 * Un intervalo de telemetría estándar de diagnóstico y uso.
 * Diagnóstico de dependencia: busque errores o rendimiento deficiente donde su aplicación usa otros componentes, como bases de datos, API de REST u otros servicios.
 * Solucione cualquier problema con la telemetría.
* **Ambos:** compile el SDK en el código de la aplicación web y ejecute el Monitor de estado en el servidor web. Lo mejor de ambos mundos:
 * Telemetría estándar de diagnóstico y uso.
 * Diagnósticos de dependencia.
 * Escriba la telemetría personalizada mediante la API.
 * Solucione cualquier problema con el SDK y la telemetría.



> [AZURE.TIP]¿Es la aplicación una [aplicación web del Servicio de aplicaciones de Azure](../app-service-web/websites-learning-map.md)? [Agregue el SDK de Application Insights][greenbrown] y, a continuación, [agregue la extensión Application Insights](../insights-perf-analytics.md) desde el panel de control de la aplicación en Azure.


## Instalación del Monitor de estado de Application Insights en el servidor web IIS

1. Necesita una suscripción a [Microsoft Azure](http://azure.com) 

1. En el servidor web IIS, inicie sesión con las credenciales de administrador.
2. Descargue y ejecute el [instalador del Monitor de estado](http://go.microsoft.com/fwlink/?LinkId=506648).

4. En el asistente de instalación, inicie sesión en Microsoft Azure.

    ![Inicie sesión en Azure con las credenciales de la cuenta Microsoft.](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

5. Seleccione la aplicación web instalada o el sitio web que desea supervisar y, a continuación, configure el recurso en el cual desea ver los resultados del portal Application Insights.

    ![Elija una aplicación y un recurso.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Normalmente, debe optar por configurar un nuevo recurso y [grupo de recursos][roles].

    Utilizaría un recurso existente si ya ha configurado [pruebas web][availability] para su sitio, o la [supervisión de cliente web][client].

6. Reinicie IIS.

    ![Seleccione Reiniciar en la parte superior del cuadro de diálogo.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    El servicio web se interrumpirá durante un breve período.

6. Tenga en cuenta que ApplicationInsights.config se ha insertado en las aplicaciones web que desea supervisar.

    ![Busque el archivo .config junto con los archivos de código de la aplicación web.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   También hay algunos cambios en web.config.

### ¿Desea (volver a) configurarlo después?

Una vez completado el asistente, puede volver a configurar el agente siempre que lo desee. También puede utilizarlo si ha instalado el agente pero hay algún problema con la configuración inicial.

![Hacer clic en el icono de Application Insights en la barra de tareas](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## Visualización de la telemetría de rendimiento

Inicie sesión en el [Portal de vista previa de Azure](http://portal.azure.com), examine Application Insights y abra el recurso que ha creado.

![Elija Examinar, Application Insights y, después, seleccione su aplicación.](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Abra la hoja Rendimiento para ver la dependencia y otros datos.

![Rendimiento](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Haga clic en cualquier gráfico para ver su contenido con más detalle.


![](./media/app-insights-monitor-performance-live-website-now/appinsights-038-dependencies.png)

#### Dependencias

Los gráficos con la etiqueta HTTP, SQL, AZUREBLOB muestran los tiempos de respuesta y los recuentos de llamadas a las dependencias; es decir, los servicios externos que utiliza la aplicación.



#### Contadores de rendimiento

Haga clic en cualquier gráfico del contador de rendimiento para cambiar lo que se muestra. O bien, puede agregar un nuevo gráfico.
 
#### Excepciones

![Haga clic en el gráfico de excepciones de servidor.](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Puede profundizar en las excepciones específicas (de los últimos siete días) y obtener seguimientos de la pila y datos de contexto.


### ¿No hay telemetría?

  * Utilice su sitio para generar algunos datos.
  * Espere unos minutos para permitir que los datos lleguen y, a continuación, haga clic en Actualizar.
  * Abra la Búsqueda de diagnóstico (icono Buscar) para ver los eventos individuales. Los eventos suelen estar visibles en la Búsqueda de diagnóstico antes de que los datos agregados aparezcan en los gráficos.
  * Abrir el Monitor de estado y seleccione la aplicación en el panel izquierdo. Compruebe si hay algún mensaje de diagnóstico para esta aplicación en la sección "Notificaciones de configuración":
  
  ![](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * Asegúrese de que el firewall del servidor permite el tráfico saliente en el puerto 443 a dc.services.visualstudio.com. 
  * Si ve en el servidor un mensaje acerca de "permisos insuficientes":
  * En el Administrador de IIS, seleccione el grupo de aplicaciones, abra Opciones avanzadas y bajo el Modelo de proceso, observe la Identidad.
  * En el panel de control de administración del equipo, agregue esta identidad al grupo Usuarios del monitor de sistema.
  * Vea [Solución de problemas][qna].

## Requisitos del sistema

Compatibilidad de sistema operativo para el Monitor de estado de Application Insights en servidor:

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

con el último Service Pack y .NET Framework 4.0 y 4.5

En Windows 7, 8 y 8.1 del lado cliente, de nuevo con .NET Framework 4.0 y 4.5

La compatibilidad de IIS es: IIS 7, 7.5, 8 y 8.5 (se requiere IIS)

## <a name="next"></a>Pasos siguientes

* [Cree pruebas web][availability] para asegurarse de que el sitio permanece activo.
* [Busque eventos y registros][diagnostic] para ayudar a diagnosticar problemas.
* [Agregue telemetría de cliente web][usage] para ver las excepciones de código de la página web y para que le permitan insertar llamadas de seguimiento.
* [Agregue el SDK de Application Insights SDK al código del servicio web][greenbrown] para que pueda insertar llamadas de seguimiento y registro en el código del servidor.

## Vídeo

#### Supervisión del rendimiento

[AZURE.VIDEO app-insights-performance-monitoring]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=62-->