---
title: Supervisión de rendimiento de aplicaciones web de Java en Azure Application Insights | Microsoft Docs
description: Supervisión extendida del rendimiento y el uso de su sitio web de Java con Application Insights.
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: b327e7f062cdf3e6b1b34a9540461dcb18caf21c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2018
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Supervisión de dependencias, excepciones detectadas y tiempos de ejecución del método en aplicaciones web de Java


Si ha [instrumentado la aplicación web de Java con Application Insights][java], puede usar el agente de Java para obtener información más clara, sin tener que realizar cambios de código:

* **Dependencias:** datos sobre las llamadas realizadas por la aplicación a otros componentes, por ejemplo:
  * Las **llamadas REST** realizadas a través de HttpClient, OkHttp y RestTemplate (Spring) se capturan.
  * Las llamadas **Redis** realizadas a través del cliente de Jedis se capturan.
  * **[Llamadas JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**: los comandos de MySQL, SQL Server y Oracle DB se capturan automáticamente. En el caso de MySQL, si la llamada tarda más de 10 s, el agente notifica el plan de consulta.
* **Excepciones detectadas:** información sobre las excepciones que controla el código.
* **Tiempo de ejecución del método:** información sobre el tiempo necesario para ejecutar métodos específicos.

Para usar el agente de Java, debe instalarlo en el servidor. Las aplicaciones web deben instrumentarse con el [SDK de Application Insights para Java][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalación del agente de Application Insights para Java
1. [Descargue el agente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)en la máquina que ejecuta el servidor de Java. Asegúrese de descargar la misma versión del agente de Java que los paquetes web y principales del SDK de Application Insights para Java.
2. Edite el script de inicio del servidor de aplicaciones y agregue la siguiente JVM:
   
    `javaagent:`*ruta de acceso completa al archivo JAR del agente*
   
    Por ejemplo, en Tomcat en un equipo Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Reinicie el servidor de aplicaciones.

## <a name="configure-the-agent"></a>Configuración del agente
Cree un archivo denominado `AI-Agent.xml` y colóquelo en la misma carpeta que el archivo JAR del agente.

Establezca el contenido del archivo XML. Edite el ejemplo siguiente para incluir u omitir las características que desee.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Debe habilitar la excepción de los informes y los intervalos de método para métodos individuales.

De forma predeterminada, `reportExecutionTime` es true y `reportCaughtExceptions` es false.

## <a name="view-the-data"></a>Visualización de los datos
En el recurso de Application Insights, aparecen tiempos de ejecución agregados de métodos y dependencias remotos [en el icono Rendimiento][metrics].

Para buscar instancias individuales de informes de dependencia, excepción y método, abra [Buscar][diagnostic].

[Más información sobre diagnósticos de problemas de dependencia](app-insights-asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>¿Tiene preguntas? ¿Tiene problemas?
* ¿No hay datos? [Establezca excepciones del firewall](app-insights-ip-addresses.md)
* [Solución de problemas de Java](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
