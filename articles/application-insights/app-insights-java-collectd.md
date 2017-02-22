---
title: "Supervisión del rendimiento de la aplicación web de Java en Linux: Azure | Microsoft Docs"
description: "Supervisión del rendimiento de aplicaciones extendida de sitios web de Java con el complemento CollectD para Application Insights."
services: application-insights
documentationcenter: java
author: harelbr
manager: douge
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: e085f90d3f34d32d2e065ede6674842000e29fb8


---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: métricas de rendimiento de Linux en Application Insights


Para explorar las métricas de rendimiento del sistema de Linux en [Application Insights](app-insights-overview.md), instale [collectd](http://collectd.org/) junto con su complemento Application Insights. Esta solución de código abierto recopila diversas estadísticas de red y del sistema.

Normalmente, usará collectd si ya ha [instrumentado el servicio web de Java con Application Insights][java]. Así obtendrá más datos para ayudarlo a mejorar el rendimiento de la aplicación o a diagnosticar los problemas. 

![Gráficos de ejemplo](./media/app-insights-java-collectd/sample.png)

## <a name="get-your-instrumentation-key"></a>Obtención de la clave de instrumentación
En [Microsoft Azure Portal](https://portal.azure.com), abra el recurso [Application Insights](app-insights-overview.md) donde desea que aparezcan los datos. (o bien, [cree un nuevo recurso](app-insights-create-new-resource.md)).

Realice una copia de la clave de instrumentación, que identifica al recurso.

![Examine todo, abra el recurso y, en la lista desplegable de Essentials, seleccione y copie la clave de instrumentación.](./media/app-insights-java-collectd/02-props.png)

## <a name="install-collectd-and-the-plug-in"></a>Instalación de collectd y del complemento
En los equipos de servidor Linux:

1. Instale la versión de [collectd](http://collectd.org/) 5.4.0 o posterior.
2. Descargue el [complemento del escritor collectd de Application Insights](https://aka.ms/aijavasdk). Anote el número de versión.
3. Copie el archivo JAR del complemento en `/usr/share/collectd/java`.
4. Edite `/etc/collectd/collectd.conf`:
   * Asegúrese de que [el complemento de Java](https://collectd.org/wiki/index.php/Plugin:Java) está habilitado.
   * Actualice el elemento JVMArg para que java.class.path incluya el archivo JAR siguiente. Actualice el número de versión para que coincida con el que descargó:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Agregue este fragmento de código con la clave de instrumentación del recurso:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

A continuación se muestra un archivo de configuración de ejemplo:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Configure otros [complementos de collectd](https://collectd.org/wiki/index.php/Table_of_Plugins)que pueden recopilar diversos datos de orígenes diferentes.

Reinicie collectd según su [manual](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Visualización de los datos en Application Insights
En el recurso de Application Insights, abra el [Explorador de métricas y agregue gráficos][metrics] seleccionando las métricas que desea ver en la categoría Personalizado.

![](./media/app-insights-java-collectd/result.png)

De forma predeterminada, las métricas se agregan en todos los equipos host desde los que se recopilaron estas. Para ver las métricas por host, en la hoja Detalles del gráfico, active Agrupación y elija Agrupar por CollectD-Host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Para excluir la carga de estadísticas específicas
De forma predeterminada, el complemento de Application Insights envía todos los datos recopilados por todos los complementos de lectura de collectd habilitados. 

Para excluir los datos de orígenes de datos o complementos específicos:

* Edite el archivo de configuración. 
* En `<Plugin ApplicationInsightsWriter>`, agregue líneas de directiva de esta manera:

| Directiva | Efecto |
| --- | --- |
| `Exclude disk` |Excluir todos los datos recopilados por el complemento `disk` |
| `Exclude disk:read,write` |Excluir los orígenes denominados `read` y `write` del complemento `disk`. |

Separar directivas con una nueva línea.

## <a name="problems"></a>¿Tiene problemas?
*No veo datos en el portal*

* Abra [Buscar][diagnostic] para ver si han llegado los eventos sin procesar. A veces tardan más en aparecer en el Explorador de métricas.
* Es posible que deba [establecer excepciones del firewall para los datos salientes](app-insights-ip-addresses.md)
* Habilite el seguimiento en el complemento Application Insights. Agregue esta línea en `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Abra un terminal e inicie collectd en modo detallado para ver cualquier problema que esté notificando:
  * `sudo collectd -f`

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md





<!--HONumber=Jan17_HO4-->


