---
title: "Exploración de los registros de seguimiento de Java en Azure Application Insights | Microsoft Docs"
description: "Búsqueda de seguimiento Log4J o Logback en Application Insights"
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: mbullwin
ms.openlocfilehash: 6e441c9cbd15bb1528ea8e8a781f90900af90cf2
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Exploración de los registros de seguimiento de Java en Application Insights
Si está usando Logback o Log4J (v1.2 o v2.0) para el seguimiento, los registros de seguimiento se pueden enviar automáticamente a Application Insights, donde puede explorarlos y buscar en ellos.

## <a name="install-the-java-sdk"></a>Instalación del SDK de Java

Instale el [SDK de Application Insights para Java][java], si no ha hecho ya.

(Si no desea realizar un seguimiento de las solicitudes HTTP, puede omitir la mayor parte del archivo de configuración .xml, pero debe incluir al menos el elemento `InstrumentationKey`. También debe llamar a `new TelemetryClient()` para inicializar el SDK).


## <a name="add-logging-libraries-to-your-project"></a>Incorporación de bibliotecas de registro al proyecto
*Elija la forma adecuada para su proyecto.*

#### <a name="if-youre-using-maven"></a>Si está usando Maven...
Si su proyecto ya se ha configurado para usar Maven para la compilación, combine uno de los siguientes fragmentos de código en el archivo pom.xml.

A continuación, actualice las dependencias del proyecto, para obtener los archivos binarios descargados.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Si está usando Gradle...
Si el proyecto ya se ha configurado para usar Gradle para la compilación, agregue una de las líneas siguientes al grupo `dependencies` en el archivo build.gradle.

A continuación, actualice las dependencias del proyecto, para obtener los archivos binarios descargados.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '1.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '1.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '1.0.+'
```

#### <a name="otherwise-"></a>De lo contrario...
Descargue, extraiga el appender adecuado y agregue después la biblioteca adecuada a su proyecto:

| Registrador | Descargar | Biblioteca |
| --- | --- | --- |
| Logback |[SDK con el appender de Logback](https://aka.ms/xt62a4) |applicationinsights-logging-logback |
| Log4J v2.0 |[SDK con el appender de Log4J v2](https://aka.ms/qypznq) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[SDK con el appender de Log4J v1.2](https://aka.ms/ky9cbo) |applicationinsights-logging-log4j1_2 |

## <a name="add-the-appender-to-your-logging-framework"></a>Incorporación del appender el marco de registro
Para empezar a recibir los seguimientos, combine el fragmento de código relevante al archivo de configuración Log4J o Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.Log4j">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Los appenders de Application Insights pueden hacer referencia a cualquier registrador configurado y no necesariamente por el registrador de raíz (como se muestra en los ejemplos de código anteriores).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>En el portal de Application Insights, explore los seguimientos.
Ahora que ha configurado el proyecto para enviar el seguimiento a Application Insights, puede ver y buscar estos seguimientos en el portal de Application Insights, en la hoja [Búsqueda][diagnostic].

![En el portal de Application Insights, abra Búsqueda.](./media/app-insights-java-trace-logs/10-diagnostics.png)

## <a name="next-steps"></a>Pasos siguientes
[Búsqueda de diagnóstico][diagnostic]

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md


