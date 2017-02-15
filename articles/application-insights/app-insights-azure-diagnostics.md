---
title: "Envío de registros de Diagnósticos de Azure a Application Insights | Microsoft Docs"
description: "Configure los detalles de los registros de diagnóstico de Servicios en la nube de Azure que se envían al portal de Application Insights."
services: application-insights
documentationcenter: .net
author: sbtron
manager: douge
ms.assetid: a67dd20a-fc5d-4391-ba63-bfe164fb62f7
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2843ff25c118afe5d5421b322b529e9a9f2d5036


---
# <a name="configure-azure-diagnostic-logging-to-application-insights"></a>Configuración del registro de diagnóstico de Azure en Application Insights
Cuando se configura un proyecto de Servicios en la nube o una máquina Virtual en Microsoft Azure, [Azure puede generar un registro de diagnóstico](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Puede hacer que dicho registro se envíe a Application Insights para poder analizarlo junto con los datos de telemetría de diagnóstico y uso que el SDK de Application Insights envía desde la aplicación. El registro de Azure incluye eventos de la administración de la aplicación, como inicio, detención, bloqueos, así como contadores de rendimiento. El registro también incluye llamadas de la aplicación a System.Diagnostics.Trace.

En este artículo se describe la configuración de la captura de diagnóstico en detalle.

Necesitará tener instalado Azure SDK 2.8 en Visual Studio.

## <a name="get-an-application-insights-resource"></a>Obtención de un recurso de Application Insights
Para lograr la mejor experiencia, [agregue el SDK de Application Insights a cada rol de la aplicación de Cloud Services](app-insights-cloudservices.md), o [a cualquier aplicación que vaya a ejecutar en la máquina virtual](app-insights-overview.md). Luego, puede enviar los datos de diagnóstico para analizarlos y mostrarlos en el mismo recurso de Application Insights.

Como alternativa, si no desea usar el SDK, por ejemplo, porque la aplicación ya esté funcionando, puede [crear un nuevo recurso de Application Insights](app-insights-create-new-resource.md) en el Portal de Azure. Elija **Diagnósticos de Azure** como el tipo de aplicación.

## <a name="send-azure-diagnostics-to-application-insights"></a>Envío de diagnósticos de Azure a Application Insights
Si puede actualizar el proyecto de aplicación, en Visual Studio, seleccione cada rol, elija sus propiedades y, en la pestaña Configuración, seleccione **Enviar diagnósticos a Application Insights**.

De forma alternativa, cuando usa el comando de publicación para cargar la aplicación, puede elegir la opción de Application Insights en la página Diagnósticos.

Si la aplicación ya está funcionando, use el Explorador de servidores o el explorador de Servicios en la nube de Visual Studio para abrir las propiedades de la aplicación. Seleccione **Enviar diagnósticos a Application Insights**.

En cada caso se le preguntará por los detalles del recurso de Application Insights que ha creado.

Se recomienda enviar los datos de roles distintos a recursos distintos. Puede mostrar sus gráficos de métricas, uno junto a otro, en el portal mediante la creación de un [panel](app-insights-dashboards.md).

[Más información sobre la configuración de Application Insights para una aplicación de Servicios en la nube](app-insights-cloudservices.md).

## <a name="configuring-the-azure-diagnostics-adapter"></a>Configuración del adaptador de Diagnósticos de Azure
Siga leyendo solo si quiere seleccionar las partes del registro que envía a Application Insights. De forma predeterminada, se envía todo: eventos de Microsoft Azure, contadores de rendimiento y llamadas de seguimiento de la aplicación a System.Diagnostics.Trace.

Al cambiar las opciones de diagnóstico en el editor de propiedades de roles o el asistente para publicación, lo que hace en realidad es cambiar el contenido de dos conjuntos de archivos:

* [Archivos de configuración de diagnóstico](https://msdn.microsoft.com/library/azure/dn782207.aspx) de roles. Los encontrará en el Explorador de soluciones en `<Your Service>/Roles/*/diagnostics.wadcfgx`
* Archivos de configuración de servicio, `ServiceConfiguration.*.cscfg`.

Modifique estos archivos directamente para seleccionar opciones más específicas que las que permiten los asistentes. Siga leyendo para más información. 

## <a name="separate-development-and-production-resources"></a>Recursos de desarrollo y producción diferentes
Puede enviar los datos de telemetría de las marcas de desarrollo y producción de su aplicación a diferentes recursos de Application Insights. Esto evita que la telemetría de desarrollo se vea inundada por telemetría activa. 

1. [Cree nuevos recursos de Application Insights](app-insights-create-new-resource.md) para cada marca. Obtenga la clave de instrumentación de la pestaña Información básica de cada recurso.
2. Edite los dos archivos .cscfg e inserte diferentes claves de instrumentación.

## <a name="choose-the-priority-levels-to-send"></a>Selección de los niveles de prioridad de envío
En el archivo de configuración de diagnósticos `diagnostics.wadcfgx` de cada rol, puede filtrar los mensajes de registro por nivel.

### <a name="define-a-sink"></a>Definición de un receptor
`<SinksConfig>` define el receptor adicional donde se pueden enviar los datos de diagnóstico de Azure.  Un ejemplo de `SinksConfig` sería parecido a este:

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights/>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig>

```

`Channels` nombra un flujo de datos que se enviará al receptor. El canal actúa como filtro. El atributo `loglevel` le permite especificar el nivel de registro que enviará el canal. Los valores disponibles son: `{Verbose, Information, Warning, Error, Critical}`.

### <a name="send-data-to-the-sink"></a>Envío de datos al receptor
Para enviar datos al receptor de Application Insights, agregue los atributos de receptor en el nodo DiagnosticMonitorConfiguration o en cualquier nodo que se encuentre debajo de este. Al agregar el elemento sinks a cada nodo, se especifica que desea que los datos recopilados de ese nodo y de cualquier nodo situado debajo de éste se envíen al receptor especificado.

Por ejemplo, el valor predeterminado creado por el SDK de Azure es enviar todos los datos de diagnóstico de Azure:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

Pero si desea enviar solo los registros de error, califique el nombre del receptor con un nombre de canal:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

Observe que estamos usando el nombre del receptor que hemos definido, junto con el nombre de un canal que definimos anteriormente.

Si solo quisiera enviar registros de aplicación detallados a Application Insights, entonces agregaría el atributo sinks al nodo `Logs` .

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

También puede incluir varios receptores en la configuración en distintos niveles de la jerarquía. En ese caso, el receptor especificado en el nivel superior de la jerarquía actúa como una configuración global y el especificado en los elementos individuales como una invalidación de esa configuración global.

Este es un ejemplo completo del archivo de configuración pública que envía todos los errores a Application Insights (especificados en el nodo `DiagnosticMonitorConfiguration`) y además los registros de nivel detallado de los registros de aplicación (especificados en el nodo `Logs`).

```xml

    <WadCfg>
     <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData">
       <!-- All info below sent to this channel -->
      <DiagnosticInfrastructureLogs />
      <PerformanceCounters>
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
        <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
      </PerformanceCounters>
      <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Application!*" />
      </WindowsEventLog>
      <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/>
       <!-- This specific info sent to this channel -->
     </DiagnosticMonitorConfiguration>

     <SinksConfig>
      <Sink name="ApplicationInsights">
        <ApplicationInsights/>
        <Channels>
          <Channel logLevel="Error" name="MyTopDiagData"  />
          <Channel logLevel="Verbose" name="MyLogData"  />
        </Channels>
      </Sink>
     </SinksConfig>
    </WadCfg>
```


Existen algunas limitaciones que se deben tener en cuenta con esta funcionalidad:

* Los canales solo están diseñados para funcionar con tipos de registros y no con contadores de rendimiento. Si especifica un canal con un elemento de contador de rendimiento se pasará por alto.
* El nivel de registro de un canal no puede superar el nivel de registro de lo que va a recopilar con Diagnósticos de Azure. Por ejemplo, no puede recopilar errores de registro de aplicación del elemento Logs e intentar enviar registros detallados al receptor de Application Insights. El atributo scheduledTransferLogLevelFilter debe recopilar siempre un número de registros igual o superior a los registros que intenta enviar a un receptor.
* No puede enviar datos de blob recopilados mediante la extensión de Diagnósticos de Azure a Application Insights. Por ejemplo, nada de lo especificado en el nodo Directories. En el caso de volcados de memoria, el volcado real se sigue enviando al almacenamiento de blobs y solo se enviará a Application Insights una notificación de que el volcado de memoria se ha generado.

## <a name="next-steps"></a>Pasos siguientes
* [Supervisión de Servicios en la nube de Azure con Application Insights](app-insights-cloudservices.md)
* [Uso de PowerShell para enviar diagnósticos de Azure a Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Archivo de configuración de Diagnósticos de Azure](https://msdn.microsoft.com/library/azure/dn782207.aspx)




<!--HONumber=Nov16_HO3-->


