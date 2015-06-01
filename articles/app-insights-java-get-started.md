<properties 
	pageTitle="Introducción a Application Insights en un proyecto web de Java" 
	description="Supervisión del rendimiento y del uso del sitio web de Java con Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/26/2015" 
	ms.author="awills"/>
 
# Introducción a Application Insights en un proyecto web de Java

*Application Insights se encuentra en su versión de vista previa.*

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

Mediante la adición de Application Insights de Visual Studio al proyecto, puede detectar y diagnosticar problemas de rendimiento y excepciones.


![datos de ejemplo](./media/app-insights-java-track-http-requests/5-results.png)

Además, puede configurar [pruebas web][availability] para supervisar la disponibilidad de la aplicación e insertar el [código en las páginas web][track] para comprender los patrones de uso.

Necesitará:

* Oracle JRE 1.6 o posterior, o Zulu JRE 1.6 o posterior
* Una suscripción a [Microsoft Azure](http://azure.microsoft.com/). (Puede empezar con la [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/)).


## 1 Obtención de una clave de instrumentación de Application Insights

1. Inicio de sesión en el [Portal de Microsoft Azure](https://portal.azure.com)
2. Creación de un recurso de Application Insights

    ![Haga clic en + y elija Application Insights](./media/app-insights-java-get-started/01-create.png)
3. Establezca el tipo de aplicación a una aplicación web de Java.

    ![Rellene un nombre, elija la aplicación web de Java y haga clic en Crear.](./media/app-insights-java-get-started/02-create.png)
4. Busque la clave de instrumentación del nuevo recurso. En breve necesitará pegarlo en el proyecto de código.

    ![En la información general de nuevos recursos, haga clic en Propiedades y copie la clave de instrumentación.](./media/app-insights-java-get-started/03-key.png)

## 2 Incorporación del SDK de Application Insights para Java al proyecto

*Elija la forma adecuada para su proyecto.*

#### Si está creando un proyecto web dinámico en Eclipse...

Utilice el [complemento de SDK de Application Insights para Java][eclipse].

#### Si está usando Maven...

Si su proyecto ya se ha configurado para usar Maven para la compilación, combine el siguiente fragmento de código al archivo pom.xml.

A continuación, actualice las dependencias del proyecto, para obtener los archivos binarios descargados.

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[0.9,)</version>
      </dependency>
    </dependencies>


* *¿Errores de validación de suma de comprobación o de compilación? Utilice en su lugar una versión específica:* `<version>0.9.3</version>`

#### Si está usando Gradle...

Si su proyecto ya se ha configurado para usar Grade para la compilación, combine el siguiente fragmento de código al archivo build.gradle.

A continuación, actualice las dependencias del proyecto, para obtener los archivos binarios descargados.

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '0.9.+'
      // or applicationinsights-core for bare API
    }

* *¿Errores de validación de suma de comprobación o de compilación? Utilice en su lugar una versión específica:* `version:'0.9.3'`

#### De lo contrario...

Agregue manualmente el SDK:

1. Documentación de las [bibliotecas de Azure para Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html)
2. Extraiga los siguientes archivos binarios en el archivo zip y agregarlos al proyecto:
 * applicationinsights-core
 * applicationinsights-web
 * commons-codec
 * commons-io
 * commons-lang
 * commons-logging
 * guava
 * httpclient
 * httpcore
 * jsr305


*¿Cuál es la relación entre los componentes `-core` y `-web`?*

`applicationinsights-core` proporciona la API básica sin telemetría automática. `applicationinsights-web` proporciona tiempos de respuesta y recuentos de solicitud HTTP del seguimiento de métricas.


## 3 Adición de un archivo xml de Application Insights

Actualice ApplicationInsights.xml en la carpeta de recursos del proyecto. Copie en ella el código XML siguiente.

Sustituya la clave de instrumentación que obtuvo en el portal de Azure.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* La clave de instrumentación se envía junto con todos los elementos de telemetría e indica a Application Insights que se muestre en el recurso.
* El componente de la solicitud HTTP es opcional. Envía automáticamente telemetría sobre las solicitudes y tiempos de respuesta en el portal.
* La correlación de eventos es un complemento del componente de la solicitud HTTP. Asigna un identificador a cada solicitud recibida por el servidor y lo agrega como una propiedad a cada elemento de telemetría como la propiedad 'Operation.Id'. Le permite correlacionar la telemetría asociada a cada solicitud estableciendo un filtro en la [búsqueda de diagnóstico][diagnostic].

## 4 Adición de un filtro HTTP

El último paso de la configuración permite que el componente de la solicitud HTTP registre las solicitudes web. (No es necesario si solo desea la API básica).

Busque y abra el archivo web.xml en el proyecto y combine el siguiente fragmento de código bajo el nodo web-app, donde se han configurado los filtros de aplicación.

Para obtener los resultados más precisos, el filtro debe asignarse antes de todos los demás filtros.

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

#### Si utiliza MVC 3.1 o posterior

Edite estos elementos para incluir en el paquete de Application Insights:

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### Si está usando Struts 2

Agregue este elemento al archivo de configuración Struts (denominado normalmente struts.xml o struts-default.xml):

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

(Si tiene interceptores definidos en una pila predeterminada, el interceptor puede agregarse simplemente a dicha pila).

## 5 Visualización de la telemetría en Application Insights

Ejecute la aplicación.

Vuelva al recurso Application Insights en Microsoft Azure.

Los datos de las solicitudes HTTP aparecerán en la hoja de información general. (Si todavía no está ahí, espere unos segundos y, a continuación, haga clic en Actualizar).

![datos de ejemplo](./media/app-insights-java-track-http-requests/5-results.png)
 

Haga clic en cualquier gráfico para ver métricas más detalladas.

![](./media/app-insights-java-track-http-requests/6-barchart.png)

 

Y cuando vea las propiedades de una solicitud, podrá ver los eventos de telemetría asociados, como solicitudes y excepciones.
 
![](./media/app-insights-java-track-http-requests/7-instance.png)



[Más información acerca de las métricas][metrics]

#### Cálculo de nombre de dirección inteligente

Application Insights asume que el formato de las solicitudes HTTP para las aplicaciones de MVC es: `VERB controller/action`


Por ejemplo, `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` y `GET Home/Product/sdf96vws` se agruparán en `GET Home/Product`.

Esto permite agregaciones significativas de solicitudes, como el número de solicitudes y el tiempo de ejecución promedio para las solicitudes.

## 5 Contadores de rendimiento

Haga clic en el icono de los servidores y verá una variedad de contadores de rendimiento.


![](./media/app-insights-java-get-started/11-perf-counters.png)

### Personalización de la recopilación de contadores de rendimiento

Para deshabilitar la recopilación del conjunto estándar de contadores de rendimiento, agregue el siguiente fragmento de código bajo el nodo raíz del archivo ApplicationInsights.xml:

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### Recopilación de contadores de rendimiento adicionales

Puede especificar contadores de rendimiento adicionales que se van a recopilar.

#### Contadores JMX (expuestos por la máquina virtual de Java)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*	`displayName`: el nombre mostrado en el portal de Application Insights.
*	`objectName`: el nombre del objeto JMX.
*	`attribute`: el atributo del nombre del objeto JMX que se va a capturar
*	`type` (opcional): el tipo de atributo del objeto JMX:
 *	Valor predeterminado: un tipo simple como int o long.
 *	`composite`: los datos del contador de rendimiento tienen el formato 'Attribute.Data'
 *	`tabular`: los datos del contador de rendimiento tienen el formato de una fila de tabla



#### Contadores de rendimiento de Windows (64 bits) 

Cada [contador de rendimiento de Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) es un miembro de una categoría (de la misma manera que un campo es un miembro de una clase). Las categorías puede ser globales, o pueden tener instancias con nombre o numeradas.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*	displayName: el nombre mostrado en el portal de Application Insights.
*	categoryName: la categoría de contador de rendimiento (objeto de rendimiento) con la que está asociada este contador de rendimiento
*	counterName: el nombre del contador de rendimiento
*	instanceName: el nombre de la instancia de categoría del contador de rendimiento o una cadena vacía (""), si la categoría contiene una sola instancia. Si categoryName es Proceso, y el contador de rendimiento que desea recopilar está en el proceso de JVM actual en que se ejecuta la aplicación, especifique `"__SELF__"`.

Los contadores de rendimiento están visibles como métricas personalizadas en el [Explorador de métricas][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)


## 6 Captura de seguimiento de registros

Puede utilizar Application Insights para segmentar los registros desde Log4J, Logback u otros marcos de registro. Puede correlacionar los registros con solicitudes HTTP y otra telemetría. [Vea cómo][javalogs].

## 7 Envío de su propia telemetría

Ahora que ha instalado el SDK, puede utilizar la API para enviar su propia telemetría.

* [Realice el seguimiento de eventos y métricas personalizados][track] para saber qué hacen los usuarios con su aplicación.
* [Busque eventos y registros][diagnostic] para ayudar a diagnosticar problemas.


Además, puede aportar más características de Application Insights a su aplicación:

* [Agregue telemetría de cliente web][usage] para supervisar las vistas de páginas y las métricas básicas de usuario.
* [Configure las pruebas web][availability] para comprobar que la aplicación efectivamente está activa y responde adecuadamente.


## ¿Tiene preguntas? ¿Tiene problemas?

[Solución de problemas de Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md


<!--HONumber=54-->