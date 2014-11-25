<properties title="Search diagnostic logs with Application Insights" pageTitle="Search diagnostic logs" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Búsqueda de registros de diagnóstico con Application Insights

Puede capturar y buscar datos de diagnóstico desde System.Diagnostics.Trace, NLog y Log4Net. Application Insights proporciona una herramienta eficaz y fácil de usar para recopilar e investigar eventos de registro desde uno o varios orígenes, complementando las características de supervisión del estado de la aplicación.

La aplicación web supervisada se puede hospedar en local o en una máquina virtual, o puede ser un sitio web de Microsoft Azure.

1.  [Incorporación de un adaptador de registro][Incorporación de un adaptador de registro]
+ [Configuración de la recopilación de diagnósticos][Configuración de la recopilación de diagnósticos]
-   [Inserción de instrucciones, compilación e implementación de registros][Inserción de instrucciones, compilación e implementación de registros]
-   [Visualización de datos de registro][Visualización de datos de registro]
-   [Búsqueda de los datos][Búsqueda de los datos]
-   [Pasos siguientes][Pasos siguientes]

## <a name="add"></a>1. Incorporación de un adaptador de registro

1.  Si todavía no lo ha hecho, [agregue Application Insights al proyecto de servicio web][agregue Application Insights al proyecto de servicio web] en Visual Studio.

    Si agrega Application Insights después de agregar el registro al proyecto, encontrará que el adaptador de registro ya se ha instalado y configurado; solo tiene que [volver a implementar el proyecto][Inserción de instrucciones, compilación e implementación de registros] y [ver los datos][Visualización de datos de registro].

2.  En el Explorador de soluciones, en el menú contextual del proyecto, elija **Administrar paquetes de NuGet**.
3.  Seleccione En línea \> Todo, seleccione **Incluir versión preliminar** y busque "Microsoft.ApplicationInsights"

    ![Get the prerelease version of the appropriate adapter][Get the prerelease version of the appropriate adapter]

4.  Seleccione la versión preliminar del paquete apropiado, una de las siguientes:

-   Microsoft.ApplicationInsights.TraceListener
-   Microsoft.ApplicationInsights.NLogTarget
-   Microsoft.ApplicationInsights.Log4NetAppender

## <a name="configure"></a>2. Configuración de la recopilación de diagnósticos

### Para System.Diagnostics.Trace

En Web.config, inserte el código siguiente en la sección `<configuration>`:

    <system.diagnostics>
     <trace autoflush="true" indentsize="0">
      <listeners>
       <add name="myAppInsightsListener"  
          type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, 
         Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
     </trace>
    </system.diagnostics> 

### Para NLog

En Nlog.config, combine los fragmentos de código siguientes en las secciones `<extensions>`, `<targets>` y `<rules>`. Cree esas secciones si es necesario.

    <extensions> 
     <add  assembly="Microsoft.ApplicationInsights.NLogTarget" /> 
    </extensions> 

    <targets>
     <target xsi:type="ApplicationInsightsTarget" name="aiTarget"/>
    </targets>

    <rules>
     <logger name="*" minlevel="Trace" writeTo="aiTarget"/>
    </rules>

### Para Log4Net

En Web.config, combine estos fragmentos de código en las secciones `<configsections>` y `<log4net>`:

    <configSections>
      <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
    </configSections>

    <log4net>
     <root>
      <level value="ALL" /> <appender-ref ref="aiAppender" />
     </root>
     <appender name="aiAppender" type="Microsoft.ApplicationInsights.Log4NetAppender.ApplicationInsightsAppender, Microsoft.ApplicationInsights.Log4NetAppender">
      <layout type="log4net.Layout.PatternLayout">
       <conversionPattern value="%date [%thread] %-5level %logger - %message%newline" />
      </layout>
     </appender>
    </log4net>

## <a name="deploy"></a>3. Inserción de instrucciones, compilación e implementación de registros

Inserte las llamadas de registro de eventos utilizando el marco de registro elegido. Por ejemplo, si utiliza Log4Net, puede tener llamadas como

    log.Warn("Slow response - database01");

Los eventos registrados se enviarán a Application Insights tanto en desarrollo como en operación.

## <a name="view"></a>4. Visualización de datos de registro

En Application Insights, abra la búsqueda de diagnóstico.

![Open diagnostic search][Open diagnostic search]

Seleccione cualquier evento de registro para ver sus detalles.

![Open diagnostic search][1]

Los campos disponibles dependen del marco de registro y los parámetros utilizados en la llamada.

Puede usar cadenas sin formato (sin caracteres comodines) para filtrar los datos de campos dentro de un elemento.

## <a name="search"></a>5. Búsqueda de los datos

Establezca un intervalo de tiempo y busque los términos. Las búsquedas en un intervalo más breve son más rápidas.

![Open diagnostic search][2]

Tenga en cuenta que está buscando términos, no subcadenas. Los términos son cadenas alfanuméricas incluyendo algunos signos de puntuación, como '.' y '\_'. Por ejemplo:

<table>
  <tr><th>t&eacute;rmino</th><th>NO coincide con</th><th>pero coincide</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

A continuación se muestran las expresiones de búsqueda que puede utilizar:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><p>Consulta de ejemplo</p></th>
<th align="left"><p>Efecto</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><span class="code">lento</span></p></td>
<td align="left"><p>Busca todos los eventos del intervalo de datos cuyos campos incluyen el término &quot;lento&quot;</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">base de datos??</span></p></td>
<td align="left"><p>Coincide con base de datos01, base de datosAB, ...</p>
<p>? no se permite al comienzo de un término de búsqueda.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">base de datos*</span></p></td>
<td align="left"><p>Coincide con base de datos, base de datos01, base de datosNNNN</p>
<p>* no se permite al comienzo de un término de búsqueda.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">manzana AND plátano</span></p></td>
<td align="left"><p>Buscar eventos que contienen ambos términos. Utilizar &quot;AND&quot; en mayúsculas, no &quot;and&quot;.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">manzana OR plátano</span></p>
<p><span class="code">manzana plátano</span></p></td>
<td align="left"><p>Buscar eventos que contienen cualquiera de los dos términos. Usar &quot;OR&quot; no &quot;or&quot;.</p>
<p>Forma breve.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">manzana NOT plátano</span></p>
<p><span class="code">manzana -plátano</span></p></td>
<td align="left"><p>Encontrar eventos que contienen un término pero no el otro.</p>
<p>Forma breve.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>manz* AND plátano NOT (uvas OR pera)</p>
<p><span class="code">manz* AND plátano -(uvas pera)</span></p></td>
<td align="left"><p>Operadores lógicos y corchetes.</p>
<p>Forma más breve.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">mensaje:lento</span></p>
<p><span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span></p>
<p><span class="code">properties.logEventInfo.level:Error</span></p></td>
<td align="left"><p>Coincidir con el campo especificad. De manera predeterminada, se buscan todos los campos. Para ver qué campos están disponibles, seleccione un evento para consultarlo con detalle.</p></td>
</tr>
</tbody>
</table>

## <a name="add"></a>Pasos siguientes

-   [Incorporación de Application Insights al proyecto][agregue Application Insights al proyecto de servicio web]
-   [Configuración de pruebas de disponibilidad y de capacidad de respuesta][Configuración de pruebas de disponibilidad y de capacidad de respuesta]
-   [Solución de problemas][Solución de problemas]

## Más información

-   [Application Insights][Application Insights]
-   [Incorporación de Application Insights al proyecto][agregue Application Insights al proyecto de servicio web]
-   [Supervisión inmediata de un servidor web activo][Supervisión inmediata de un servidor web activo]
-   [Exploración de métricas en Application Insights][Exploración de métricas en Application Insights]
-   [Búsqueda del registro de diagnóstico][Búsqueda del registro de diagnóstico]
-   [Seguimiento de disponibilidad con pruebas web][Configuración de pruebas de disponibilidad y de capacidad de respuesta]
-   [Seguimiento de uso con eventos y métricas][Seguimiento de uso con eventos y métricas]
-   [Preguntas y repuestas y solución de problemas][Solución de problemas]

<!--Link references-->

  [Incorporación de un adaptador de registro]: #add
  [Configuración de la recopilación de diagnósticos]: #configure
  [Inserción de instrucciones, compilación e implementación de registros]: #deploy
  [Visualización de datos de registro]: #view
  [Búsqueda de los datos]: #search
  [Pasos siguientes]: #next
  [agregue Application Insights al proyecto de servicio web]: ../app-insights-monitor-application-health-usage/
  [Get the prerelease version of the appropriate adapter]: ./media/appinsights/appinsights-36nuget.png
  [Open diagnostic search]: ./media/appinsights/appinsights-30openDiagnostics.png
  [1]: ./media/appinsights/appinsights-32detail.png
  [2]: ./media/appinsights/appinsights-31search.png
  [Configuración de pruebas de disponibilidad y de capacidad de respuesta]: ../app-insights-monitor-web-app-availability/
  [Solución de problemas]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Supervisión inmediata de un servidor web activo]: ../app-insights-monitor-performance-live-website-now/
  [Exploración de métricas en Application Insights]: ../app-insights-explore-metrics/
  [Búsqueda del registro de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Seguimiento de uso con eventos y métricas]: ../app-insights-track-usage-custom-events-metrics/
