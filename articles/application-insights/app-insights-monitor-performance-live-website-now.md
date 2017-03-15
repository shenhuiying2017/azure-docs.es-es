---
title: "Supervisión de una aplicación web de ASP.NET con Azure Application Insights | Microsoft Docs"
description: "Supervise el rendimiento de un sitio web sin volver a implementarlo. Funciona con las aplicaciones web de ASP.NET hospedadas en local, en las máquinas virtuales o en Azure."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 917f54248f4c9277caa3cf09d92f78593a901e89
ms.openlocfilehash: fd76f40f5a34b6adf9c6ec3bded604d59b6baa72
ms.lasthandoff: 02/09/2017


---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Instrumentar aplicaciones web en tiempo de ejecución con Application Insights


Puede instrumentar una aplicación web activa con Azure Application Insights sin tener que modificar ni volver a implementar el código. En las aplicaciones hospedadas por un servidor IIS local, instale el Monitor de estado; o bien, si son aplicaciones web de Azure o se ejecutan en una máquina virtual de Azure, puede instalar la extensión Application Insights. (También hay varios artículos sobre cómo configurar [aplicaciones web en directo de J2EE](app-insights-java-live.md) y [Azure Cloud Services](app-insights-cloudservices.md)). Necesita una suscripción a [Microsoft Azure](http://azure.com) .

![gráficos de ejemplo](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Puede optar entre tres vías de aplicar Application Insights a sus aplicaciones web .NET:

* **Tiempo de compilación:** [agregue el SDK de Application Insights][greenbrown] al código de la aplicación web.
* **Tiempo de ejecución:** instrumenta la aplicación web en el servidor, como se describe a continuación, sin volver a generar e implementar el código.
* **Ambos:** compila el SDK en el código de aplicación web y aplica las extensiones del sistema en tiempo de ejecución. Obtenga lo mejor de ambas opciones.

A continuación hay un resumen de lo que se obtiene por cada vía:

|  | Tiempo de compilación | Tiempo de ejecución |
| --- | --- | --- |
| Solicitudes y excepciones |Sí |Sí |
| [Excepciones más detalladas](app-insights-asp-net-exceptions.md) | |Sí |
| [Diagnósticos de dependencia](app-insights-asp-net-dependencies.md) |En .NET 4.6 +, pero con menos detalle |Sí, detalles completos: códigos de resultado, texto de comandos SQL, verbo HTTP|
| [Contadores de rendimiento del sistema](app-insights-performance-counters.md) |Sí |Sí |
| [API para la telemetría personalizada][api] |Sí | |
| [Integración del registro de seguimiento](app-insights-asp-net-trace-logs.md) |yes | |
| [Datos de usuario y página](app-insights-javascript.md) |Sí | |
| No es necesario volver a compilar el código |No | |


## <a name="monitor-a-live-azure-web-app"></a>Supervisión de una aplicación web de Azure activa

Si la aplicación se ejecuta como un servicio web de Azure, aquí se muestra cómo cambiar de supervisión:

* En el panel de control de la aplicación en Azure, seleccione Application Insights.

    ![Configuración de Application Insights para una aplicación web de Azure](./media/app-insights-monitor-performance-live-website-now/azure-web-setup.png)
* Cuando se abre la página de resumen de Application Insights, haga clic en el vínculo situado en la parte inferior para abrir el recurso completo de Application Insights.

    ![Recorrido por Application Insights](./media/app-insights-monitor-performance-live-website-now/azure-web-view-more.png)

[Supervisión de aplicaciones de nube y VM](app-insights-azure.md).

## <a name="monitor-a-live-iis-web-app"></a>Supervisión de una aplicación web de IIS activa

Si la aplicación se hospeda en un servidor IIS, habilite Application Insights con el Monitor de estado.

1. En el servidor web IIS, inicie sesión con las credenciales de administrador.
2. Si todavía no está instalado el Monitor de estado de Application Insights, descargue y ejecute el [instalador del Monitor de estado](http://go.microsoft.com/fwlink/?LinkId=506648).
3. En el Monitor de estado, seleccione la aplicación web instalada o el sitio web que desea supervisar. Inicie sesión con sus credenciales de Azure.

    Configure el recurso donde desee ver los resultados en el portal de Application Insights. (Normalmente, es mejor crear un nuevo recurso. Seleccione un recurso existente si ya tiene [pruebas web][availability] o la [supervisión de cliente][client] para esta aplicación). 

    ![Elija una aplicación y un recurso.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Reinicie IIS.

    ![Seleccione Reiniciar en la parte superior del cuadro de diálogo.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    El servicio web se interrumpirá durante un breve período.

## <a name="customize-monitoring-options"></a>Personalización de las opciones de supervisión

Si se habilita Application Insights, se agregan archivos DLL y ApplicationInsights.config a la aplicación web. También puede [editar el archivo .config](app-insights-configuration-with-applicationinsights-config.md) para cambiar algunas de las opciones.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Al volver a publicar la aplicación, vuelva a habilitar Application Insights

Antes de volver a publicar la aplicación, considere la posibilidad de [agregar Application Insights al código de Visual Studio][greenbrown]. Obtendrá una telemetría más detallada y la capacidad para escribir telemetría personalizada.

Si desea volver a publicar sin agregar Application Insights al código, tenga en cuenta que el proceso de implementación puede eliminar los archivos DLL y ApplicationInsights.config desde el sitio web publicado. Por lo tanto:

1. Si edita el archivo ApplicationInsights.config, realice una copia del mismo antes de volver a publicar la aplicación.
2. Vuelva a publicar la aplicación.
3. Vuelva a habilitar la supervisión de Application Insights. (Utilice el método adecuado: ya sea el panel de control de la aplicación web de Azure o el Monitor de estado en un host IIS).
4. Restablezca las modificaciones realizadas en el archivo .config.


## <a name="troubleshooting-runtime-configuration-of-application-insights"></a>Solución de problemas de configuración en tiempo de ejecución de Application Insights

### <a name="cant-connect-no-telemetry"></a>¿No se puede conectar? ¿No hay telemetría?

* Deberá abrir [algunos puertos de salida](app-insights-ip-addresses.md#outgoing-ports) en el firewall del servidor para permitir que el Monitor de estado funcione.

* Abrir el Monitor de estado y seleccione la aplicación en el panel izquierdo. Compruebe si hay algún mensaje de diagnóstico para esta aplicación en la sección "Notificaciones de configuración":

  ![Abra la hoja Rendimiento para ver la solicitud, el tiempo de respuesta, la dependencia y otros datos.](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* En el servidor, si ve en un mensaje acerca de "permisos insuficientes", intente lo siguiente:
  * En el Administrador de IIS, seleccione el grupo de aplicaciones, abra **Configuración avanzada** y en **Modelo de proceso**, anote la identidad.
  * En el panel de control de administración del equipo, agregue esta identidad al grupo Usuarios del monitor de sistema.
* Si tiene instalado MMA/SCOM en el servidor, algunas versiones pueden entrar en conflicto. Desinstale SCOM y el Monitor de estado y vuelva a instalar las versiones más recientes.
* Consulte [Solución de problemas][qna].

## <a name="system-requirements"></a>Requisitos del sistema
Compatibilidad de sistema operativo para el Monitor de estado de Application Insights en servidor:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

con el último Service Pack y .NET Framework 4.5

En Windows 7, 8, 8.1 y 10 del lado cliente, de nuevo con .NET Framework 4.5

La compatibilidad de IIS es: IIS 7, 7.5, 8 y 8.5 (se requiere IIS)

## <a name="automation-with-powershell"></a>Automatización con PowerShell
Puede iniciar y detener la supervisión mediante PowerShell en el servidor IIS.

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
  * `LatestAvailableSdkVersion`muestra la versión disponible en la galería de NuGet. Para actualizar la aplicación a esta versión, use `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` El nombre de la aplicación en IIS
* `-InstrumentationKey` El valor ikey del recurso de Application Insights donde quiere que se muestren los resultados.
* Este cmdlet solo afecta a las aplicaciones que no se han instrumentado, es decir, aquellas cuyo SdkState == NotInstrumented.

    El cmdlet no afecta a una aplicación que ya se ha instrumentado, ya sea en tiempo de compilación mediante la adición del SDK en el código o en tiempo de ejecución mediante un uso anterior de este cmdlet.

    La versión del SDK utilizada para instrumentar la aplicación es la versión que se ha descargado más recientemente en este servidor.

    Para descargar la versión más reciente, use Update-ApplicationInsightsVersion.
* Si se descarga correctamente, devuelve `ApplicationInsightsApplication` . Si se produce un error, inicia un seguimiento a stderr.

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

## <a name="next"></a>Pasos siguientes

Vea la telemetría:

* [Explore las métricas](app-insights-metrics-explorer.md) para supervisar el rendimiento y uso.
* [Busque eventos y registros][diagnostic] para diagnosticar problemas.
* [Análisis](app-insights-analytics.md) para más consultas avanzadas
* [Creación de paneles](app-insights-dashboards.md)

Agregue más telemetría:

* [Cree pruebas web][availability] para asegurarse de que el sitio permanece activo.
* [Agregue telemetría de cliente web][usage] para ver las excepciones de código de la página web y para que le permitan insertar llamadas de seguimiento.
* [Agregue el SDK de Application Insights al código][greenbrown] para que pueda insertar llamadas de seguimiento y registro.

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

