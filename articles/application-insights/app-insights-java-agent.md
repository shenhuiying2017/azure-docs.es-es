<properties 
	pageTitle="Supervisión de dependencias, excepciones y tiempos de ejecución en aplicaciones web de Java" 
	description="Supervisión extendida de sitios web de Java con Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="awills"/>
 
# Supervisión de dependencias, excepciones y tiempos de ejecución en aplicaciones web de Java

*Application Insights se encuentra en su versión de vista previa.*

Si ha [instrumentado la aplicación web de Java con Application Insights][java], puede usar el agente de Java para obtener información más clara, sin tener que realizar cambios de código:

* **Dependencias remotas:** datos sobre las llamadas que la aplicación realiza a través de un controlador [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/), como MySQL, SQL Server, PostgreSQL y SQLite.
* **Excepciones detectadas:** datos sobre las excepciones que controla el código.
* **Tiempo de ejecución del método:** datos sobre el tiempo necesario para ejecutar métodos específicos.

Para usar el agente de Java, debe instalarlo en el servidor. Las aplicaciones web deben instrumentarse con el [SDK de Application Insights para Java][java].

## Instalación del agente de Application Insights para Java

1. [Descargue el agente](http://go.microsoft.com/fwlink/?LinkId=618633) en el equipo que ejecuta el servidor de Java.
2. Edite el script de inicio del servidor de aplicaciones y agregue la siguiente JVM:

    `javaagent:`*ruta de acceso completa al archivo JAR del agente*

    Por ejemplo, en Tomcat en un equipo Linux:

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Reinicie el servidor de aplicaciones.

## Configuración del agente

Cree un archivo denominado `AI-Agent.xml` y colóquelo en la misma carpeta que el archivo JAR del agente.

Establezca el contenido del archivo XML. Edite el ejemplo siguiente para incluir u omitir las características que desee.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true"/>

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
              signature="(Ljava/lang/String:I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Debe habilitar la excepción de los informes y los intervalos de método para métodos individuales.

De forma predeterminada, `reportExecutionTime` es true y `reportCaughtExceptions` es false.

## Visualización de los datos

En el recurso de Application Insights, aparecerán tiempos de ejecución de métodos y dependencias remota agregados [en el icono Rendimiento][metrics].

Para buscar instancias individuales de informes de dependencia, excepción y método, abra [Buscar][diagnostic].

[Más de diagnosticar problemas de dependencia -](app-insights-dependencies.md#diagnosis).

## ¿Tiene preguntas? ¿Tiene problemas?

[Solución de problemas de Java](app-insights-java-troubleshoot.md)



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

 

<!---HONumber=July15_HO4-->