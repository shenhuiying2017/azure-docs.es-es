<properties
	pageTitle="Diagnóstico de problemas de rendimiento en un sitio web de IIS en ejecución | Microsoft Azure"
	description="Supervise el rendimiento de un sitio web sin volver a implementarlo. Úselo de forma independiente o con el SDK de Application Insights para obtener la telemetría de dependencia."
	services="application-insights"
    documentationCenter=".net"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/24/2016"
	ms.author="awills"/>


# Instrumentar aplicaciones web en tiempo de ejecución con Application Insights

*Application Insights se encuentra en su versión de vista previa.*

Puede instrumentar una aplicación web activa con Visual Studio Application Insights, sin tener que modificar o volver a implementar el código. En las aplicaciones hospedadas por un servidor IIS local, instale el Monitor de estado; o bien, si son aplicaciones web de Azure o se ejecutan en una máquina virtual de Azure, puede instalar la extensión Application Insights. (También hay varios artículos sobre cómo configurar [aplicaciones web en directo de J2EE](app-insights-java-live.md) y [Servicios en la nube de Azure](app-insights-cloudservices.md)).

![gráficos de ejemplo](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Puede optar entre tres formas de aplicar Application Insights a sus aplicaciones web .NET:

* **Tiempo de compilación:** [agregue el SDK de Application Insights][greenbrown] al código de la aplicación web.
* **Tiempo de ejecución:** instrumenta la aplicación web en el servidor, como se describe a continuación, sin volver a generar e implementar el código.
* **Ambos:** compila el SDK en el código de aplicación web y aplica las extensiones del sistema en tiempo de ejecución. Obtenga lo mejor de ambas opciones.

A continuación hay un resumen de lo que se obtiene de cada forma:

||Tiempo de compilación|Tiempo de ejecución|
|---|---|---|
|Solicitudes y excepciones|Sí|Sí|
|[Excepciones más detalladas](app-insights-asp-net-exceptions.md)||Sí|
|[Diagnósticos de dependencia](app-insights-asp-net-dependencies.md)|En .NET 4.6 +|Sí|
|[Contadores de rendimiento del sistema](app-insights-web-monitor-performance.md#system-performance-counters)||Servicio en la nube de IIS o Azure, aplicación web no de Azure|
|[API para la telemetría personalizada][api]|Sí||
|[Integración del registro de seguimiento](app-insights-asp-net-trace-logs.md)|Sí||
|[Datos de usuario y página](app-insights-javascript.md)|Sí||
|No es necesario volver a compilar el código|No||




## Instrumentación de la aplicación web en tiempo de ejecución

Necesita una suscripción a [Microsoft Azure](http://azure.com).

### Si la aplicación se hospeda en el servidor IIS

1. En el servidor web IIS, inicie sesión con las credenciales de administrador.
2. Descargue y ejecute el [instalador del Monitor de estado](http://go.microsoft.com/fwlink/?LinkId=506648).
4. En el asistente de instalación, inicie sesión en Microsoft Azure.

    ![Inicie sesión en Azure con las credenciales de la cuenta Microsoft.](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

    *¿Errores de conexión? Consulte [Solución de problemas](#troubleshooting).*

5. Seleccione la aplicación web instalada o el sitio web que desea supervisar y, a continuación, configure el recurso en el cual desea ver los resultados del portal Application Insights.

    ![Elija una aplicación y un recurso.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Normalmente, debe optar por configurar un nuevo recurso y un [grupo de recursos][roles].

    De lo contrario, usará un recurso existente si ya ha configurado [pruebas web][availability] para su sitio, o bien, la [supervisión de cliente web][client].

6. Reinicie IIS.

    ![Seleccione Reiniciar en la parte superior del cuadro de diálogo.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    El servicio web se interrumpirá durante un breve período.

6. Tenga en cuenta que ApplicationInsights.config se ha insertado en las aplicaciones web que desea supervisar.

    ![Busque el archivo .config junto con los archivos de código de la aplicación web.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   También hay algunos cambios en web.config.

#### ¿Desea (volver a) configurarlo después?

Una vez completado el asistente, puede volver a configurar el agente siempre que lo desee. También puede utilizarlo si ha instalado el agente pero hay algún problema con la configuración inicial.

![Hacer clic en el icono de Application Insights en la barra de tareas](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)


### Si la aplicación es una aplicación web de Azure

1. En el [Portal de Azure](https://portal.azure.com), cree un recurso de Application Insights con el tipo ASP.NET. Aquí se almacenará, analizará y mostrará la telemetría de la aplicación.

    ![Agregue Application Insights. Seleccione el tipo ASP.NET.](./media/app-insights-monitor-performance-live-website-now/01-new.png)
     
2. Ahora abra la hoja de control de la aplicación web de Azure, abra **Herramientas > Supervisión de rendimiento** y agregue la extensión Application Insights.

    ![En la aplicación web, Herramientas, Extensiones, Agregar, Application Insights](./media/app-insights-monitor-performance-live-website-now/05-extend.png)

    Seleccione el recurso de Application Insights que acaba de crear.


### Si se trata de un proyecto de servicios en la nube de Azure

[Agregar scripts a roles web y de trabajo](app-insights-cloudservices.md)


## Visualización de la telemetría de rendimiento

Inicie sesión en el [Portal de Azure](https://portal.azure.com), examine Application Insights y abra el recurso que ha creado.

![Elija Examinar, Application Insights y, después, seleccione su aplicación.](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Abra la hoja Rendimiento para ver la solicitud, el tiempo de respuesta, la dependencia y otros datos.

![Rendimiento](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Haga clic en cualquier gráfico para abrir una vista más detallada.

También puede [Editar, reorganizar, guardar](app-insights-metrics-explorer.md) y anclar gráficos o toda la hoja a un [panel](app-insights-dashboards.md).

## Dependencias

El gráfico de duración de dependencia muestra el tiempo que tardan las llamadas desde la aplicación a los componentes externos, como bases de datos, API de REST o almacenamiento de blobs de Azure.

Para segmentar el gráfico por llamadas a diferentes dependencias, edite el gráfico, active la agrupación y, luego, agrupe por la dependencia, el tipo de dependencia o el rendimiento de dependencia.

![Dependencia](./media/app-insights-monitor-performance-live-website-now/23-dep.png)

## Contadores de rendimiento 

(No para aplicaciones web de Azure). Haga clic en Servidores en la hoja de información general para ver los gráficos de contadores de rendimiento de servidor, como el uso de memoria y la ocupación de CPU.

Si tiene varias instancias de servidor, puede editar los gráficos para agruparlos por instancia de rol.

![Servidores](./media/app-insights-monitor-performance-live-website-now/22-servers.png)

También puede [cambiar el conjunto de contadores de rendimiento que se notifican mediante el SDK](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3).


## Excepciones

![Haga clic en el gráfico de excepciones de servidor.](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Puede profundizar en las excepciones específicas (de los últimos siete días) y obtener seguimientos de la pila y datos de contexto.

## Muestreo

Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. [Aprenda más sobre el muestreo.](app-insights-sampling.md)


## Solución de problemas

### Errores de conexión

Deberá abrir [algunos puertos de salida](app-insights-ip-addresses.md#outgoing-ports) en el firewall del servidor para permitir que el Monitor de estado funcione.

### ¿No hay telemetría?

  * Utilice su sitio para generar algunos datos.
  * Espere unos minutos a que lleguen los datos y, luego, haga clic en **Actualizar**.
  * Abra la Búsqueda de diagnóstico (icono Buscar) para ver los eventos individuales. Los eventos suelen estar visibles en la Búsqueda de diagnóstico antes de que los datos agregados aparezcan en los gráficos.
  * Abrir el Monitor de estado y seleccione la aplicación en el panel izquierdo. Compruebe si hay algún mensaje de diagnóstico para esta aplicación en la sección "Notificaciones de configuración":

  ![Abra la hoja Rendimiento para ver la solicitud, el tiempo de respuesta, la dependencia y otros datos.](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * Asegúrese de que el firewall del servidor permite el tráfico de saliente en los puertos indicados más arriba.
  * En el servidor, si ve en un mensaje acerca de "permisos insuficientes", intente lo siguiente:
    * En el Administrador de IIS, seleccione el grupo de aplicaciones, abra **Configuración avanzada** y en **Modelo de proceso**, anote la identidad.
    * En el panel de control de administración del equipo, agregue esta identidad al grupo Usuarios del monitor de sistema.
  * Si tiene instalado MMA/SCOM en el servidor, algunas versiones pueden entrar en conflicto. Desinstale SCOM y el Monitor de estado y vuelva a instalar las versiones más recientes.
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

## Automatización con PowerShell

Puede iniciar y detener la supervisión mediante PowerShell.

En primer lugar, importe el módulo de Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Encuentre las aplicaciones en supervisión:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Opcional) Nombre de una aplicación web.
* Muestra el estado de supervisión de Application Insights para cada aplicación web (u otra aplicación con nombre) en este servidor IIS.

* Devuelve `ApplicationInsightsApplication` para cada aplicación:
 * `SdkState==EnabledAfterDeployment`: la aplicación se está supervisando y se ha instrumentado en tiempo de ejecución, ya sea con la herramienta Monitor de estado o con `Start-ApplicationInsightsMonitoring`.
 * `SdkState==Disabled`: la aplicación no se ha instrumentado para Application Insights. Nunca se ha instrumentado, o bien se deshabilitó la supervisión en tiempo de ejecución con la herramienta Monitor de estado o con `Stop-ApplicationInsightsMonitoring`.
 * `SdkState==EnabledByCodeInstrumentation`: se ha instrumentado la aplicación agregando el SDK al código fuente. El SDK no se puede actualizar ni detener.
 * `SdkVersion` muestra la versión en uso para la supervisión de esta aplicación.
 * `LatestAvailableSdkVersion` muestra la versión disponible en la galería de NuGet. Para actualizar la aplicación a esta versión, use `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` El nombre de la aplicación en IIS
* `-InstrumentationKey` El valor ikey del recurso de Application Insights donde quiere que se muestren los resultados.

* Este cmdlet solo afecta a las aplicaciones que no se han instrumentado, es decir, aquellas cuyo SdkState == NotInstrumented.

    El cmdlet no afecta a una aplicación que ya se ha instrumentado, ya sea en tiempo de compilación mediante la adición del SDK en el código o en tiempo de ejecución mediante un uso anterior de este cmdlet.

    La versión del SDK utilizada para instrumentar la aplicación es la versión que se ha descargado más recientemente en este servidor.

    Para descargar la versión más reciente, use Update-ApplicationInsightsVersion.

* Si se descarga correctamente, devuelve `ApplicationInsightsApplication`. Si se produce un error, inicia un seguimiento a stderr.

    
          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` El nombre de una aplicación en IIS
* `-All` Detiene la supervisión de todas las aplicaciones en este servidor IIS con `SdkState==EnabledAfterDeployment`

* Detiene la supervisión de las aplicaciones especificadas y quita la instrumentación. Solo funciona para las aplicaciones que se han instrumentado en tiempo de ejecución con la herramienta Monitor de estado o Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)

* Devuelve ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: el nombre de la aplicación web en IIS.
* `-InstrumentationKey` (Opcional). Utilice esto para cambiar el recurso al que se envía la telemetría de la aplicación.
* Este cmdlet:
 * Permite actualizar la aplicación con nombre a la versión del SDK descargada más recientemente en este equipo. (Solo funciona si `SdkState==EnabledAfterDeployment`)
 * Si proporciona una clave de instrumentación, se vuelve a configurar la aplicación con nombre para enviar los datos de telemetría al recurso con esa clave. (Funciona si `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Permite descargar el SDK más reciente de Application Insights en el servidor.

## Plantilla de Azure

Si la aplicación web está en Azure y crea los recursos mediante una plantilla de Azure Resource Manager, puede configurar Application Insights agregando lo siguiente al nodo de recursos:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource`: nombre para el recurso de Application Insights
* `myWebAppName`: identificador de la aplicación web

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
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=AcomDC_0831_2016-->