---
title: "Application Insights para aplicaciones web de Java que ya están activas"
description: "Inicio de la supervisión de una aplicación web que se ya se está ejecutando en el servidor"
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: bwren
ms.openlocfilehash: a2731e3e44f8f3d104d8abc7dbe71fe3a4c3a690
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Application Insights para aplicaciones web de Java que ya están activas


Si tiene una aplicación web que se está ejecutando en el servidor de J2EE, puede iniciar la supervisión con [Application Insights](app-insights-overview.md) sin necesidad de realizar cambios de código ni volver a compilar el proyecto. Con esta opción, obtendrá información acerca de las solicitudes HTTP enviadas al servidor, las excepciones no controladas y contadores de rendimiento.

Necesitará una suscripción a [Microsoft Azure](https://azure.com).

> [!NOTE]
> El procedimiento en esta página agrega el SDK a la aplicación web en tiempo de ejecución. Esta instrumentación en tiempo de ejecución es útil si no desea actualizar o volver a generar el código fuente. Pero si es posible, se recomienda [agregar el SDK al código fuente](app-insights-java-get-started.md) en su lugar. Que proporciona más opciones, como la escritura de código para realizar el seguimiento de actividad del usuario.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Obtención de una clave de instrumentación de Application Insights
1. Inicie sesión en el [Portal de Microsoft Azure](https://portal.azure.com)
2. Cree un nuevo recurso de Application Insights y establezca el tipo de aplicación en aplicación web de Java.
   
    ![Rellene un nombre, elija la aplicación web de Java y haga clic en Crear.](./media/app-insights-java-live/02-create.png)

    El recurso se crea en unos segundos.

4. Abra el nuevo recurso y obtenga su clave de instrumentación. Pronto tendrá que pegarla en el proyecto de código.
   
    ![En la información general de nuevos recursos, haga clic en Propiedades y copie la clave de instrumentación.](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Descarga del SDK
1. Descargue el [SDK de Application Insights para Java](https://aka.ms/aijavasdk). 
2. En el servidor, extraiga el contenido del SDK en el directorio desde el que se cargan los archivos binarios de proyecto. Si usa Tomcat, este directorio se encontrará normalmente en `webapps/<your_app_name>/WEB-INF/lib`.

Tenga en cuenta que necesitará repetir este procedimiento en cada instancia de servidor y en cada aplicación.

## <a name="3-add-an-application-insights-xml-file"></a>3. Adición de un archivo xml de Application Insights
Cree ApplicationInsights.xml en la carpeta en la que se ha agregado el SDK. Ponga el archivo en el siguiente XML.

Sustituya la clave de instrumentación que obtuvo en el portal de Azure.

```XML

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
```

* La clave de instrumentación se envía junto con todos los elementos de telemetría e indica a Application Insights que se muestre en el recurso.
* El componente de la solicitud HTTP es opcional. Envía automáticamente telemetría sobre las solicitudes y tiempos de respuesta en el portal.
* La correlación de eventos es un complemento del componente de la solicitud HTTP. Asigna un identificador a cada solicitud recibida por el servidor y agrega este identificador como propiedad a todos los elementos de telemetría como la propiedad 'Operation.Id'. Le permite correlacionar la telemetría asociada a cada solicitud estableciendo un filtro en la [búsqueda de diagnóstico](app-insights-diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. Adición de un filtro HTTP
Busque y abra el archivo web.xml en el proyecto y combine el siguiente fragmento de código bajo el nodo web-app, donde se han configurado los filtros de aplicación.

Para obtener los resultados más precisos, el filtro debe asignarse antes de todos los demás filtros.

```XML

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
```

## <a name="5-check-firewall-exceptions"></a>5. Comprobación de las excepciones del firewall
Es posible que deba [establecer excepciones para enviar los datos salientes](app-insights-ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Reinicio de la aplicación web
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Visualización de la telemetría en Application Insights
Vuelva al recurso de Application Insights en el [Portal de Microsoft Azure](https://portal.azure.com).

Se mostrará telemetría sobre las solicitudes HTTP en la hoja de información general. (Si todavía no está ahí, espere unos segundos y, a continuación, haga clic en Actualizar).

![datos de ejemplo](./media/app-insights-java-live/5-results.png)

Haga clic en cualquier gráfico para ver métricas más detalladas. 

![](./media/app-insights-java-live/6-barchart.png)

Y cuando vea las propiedades de una solicitud, podrá ver los eventos de telemetría asociados, como solicitudes y excepciones.

![](./media/app-insights-java-live/7-instance.png)

[Más información acerca de las métricas.](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>Pasos siguientes
* [Agregue telemetría a las páginas web](app-insights-javascript.md) para supervisar las vistas de páginas y las métricas de usuario.
* [Configure las pruebas web](app-insights-monitor-web-app-availability.md) para comprobar que la aplicación efectivamente está activa y responde adecuadamente.
* [Captura de seguimiento de registros](app-insights-java-trace-logs.md)
* [Busque eventos y registros](app-insights-diagnostic-search.md) para ayudar a diagnosticar problemas.

