<properties
    pageTitle="Application Insights para aplicaciones Android | Microsoft Azure"
    description="Analice el uso y el rendimiento de la aplicación Android con Application Insights."
    services="application-insights"
    documentationCenter="android"
    authors="alancameronwills"
    manager="ronmart"/>

<tags
    ms.service="application-insights"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="04/28/2015"
    ms.author="awills"/>

# Application Insights para aplicaciones Android

Application Insights de Visual Studio le permite supervisar su aplicación móvil para obtener información relativa al uso, los eventos y los bloqueos.

## Requisitos

Necesitará:

* Una suscripción a [Microsoft Azure](http://azure.com). Inicie sesión con una cuenta de Microsoft, que puede que tenga para Windows, XBox Live u otros servicios en la nube de Microsoft.
* Android Studio
* Versión 9 o posterior del SDK de Android.

## Creación de recursos en Application Insights

En el [portal de Azure][portal], cree un nuevo recurso de Application Insights. Seleccione la opción Android.

![Haga clic en Nuevo, Servicios para desarrolladores, Application Insights.](./media/app-insights-android/11-new.png)

En la hoja que se abre podrá ver los datos de uso y rendimiento sobre la aplicación. Para volver a ella la próxima vez que inicie sesión en Azure, encontrará un icono correspondiente en la pantalla de inicio. También puede hacer clic en Examinar para buscarla.

## Instale el complemento Application Insights en Android Studio

si aún no lo ha hecho.

1.  Inicie Studio Android y configure los complementos.

    ![Elija Configurar](./media/app-insights-android/01-configure.png)

2.  Seleccione e instale el complemento Application Insights Android Studio.

    ![Seleccione el complemento](./media/app-insights-android/03-select-plugin.png)

## <a name="sdk"></a>Instalación del SDK en la aplicación


1.  Seleccione **Herramientas** -> **Integrar SDK de Application Insights**.

    ![Integre Application Insights](./media/app-insights-android/04-tools-integrate.png)

3.  Creación de un componente en la suscripción

    ![Crear un componente](./media/app-insights-android/07-create-component.png)

    Use la clave de instrumentación que obtuvo del recurso de Application Insights.

4.  Sincronización de Gradle para descargar el SDK e integrarlo con el proyecto

    ![Sincronice archivos Gradle para descargar el SDK](./media/app-insights-android/08-successful-integration.png)

    (En la [página de uso](http://go.microsoft.com/fwlink/?LinkID=533220) hay disponible información adicional.)

En este punto se agregó la siguiente referencia a los módulos build.gradle, permisos para `INTERNET` y `ACCESS_NETWORK_STATE`, y una etiqueta de metadatos que contiene la clave de instrumentación del componente se agregaron a los `AndroidManifest.xml` de los módulos.

```java

    dependencies {
    compile 'com.microsoft.azure:applicationinsights-android:+'
    }
```

```xml

    <manifest>
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application>
        <meta-data
            android:name="com.microsoft.applicationinsights.instrumentationKey"
            android:value="${AI_INSTRUMENTATION_KEY}" />
    </application>
    </manifest>
```

#### Opcional: establezca la clave de instrumentación en el código

También es posible establecer la clave de instrumentación en el código. Esto anulará la establecida en `AndroidManifest.xml`.

```java

    ApplicationInsights.setup(this, "<YOUR-IKEY-GOES-HERE>");
    ApplicationInsights.start();
```


## Uso del SDK

Inicialice el SDK y empiece a realizar un seguimiento de la telemetría.

Agregue el elemento importado siguiente a la actividad raíz de la aplicación:

```java

     import com.microsoft.applicationinsights.library.ApplicationInsights;
```

Y agregue lo siguiente a la devolución de llamada de `onCreate` de la actividad.

```java

    ApplicationInsights.setup(this.getApplicationContext(), this.getApplication());
    ApplicationInsights.start();
```

Una vez que se llame a `ApplicationInsights.start()`, el SDK comienza a realizar el seguimiento de la actividad del ciclo de vida de Android y de cualquier excepción no controlada.

> [AZURE.NOTE]Los eventos del ciclo de vida de la aplicación solo se recopilan en la versión 15 y posteriores del SDK de Android (Ice Cream Sandwich+).

Además, se pueden recopilar eventos personalizados, seguimientos, métricas y excepciones controladas. Utilice cualquiera de las [API de Application Insights][api] para enviar telemetría.

* TrackEvent(eventName) para las demás acciones de usuario
* TrackTrace(logEvent) para el [registro de diagnóstico][diagnostic]
* TrackException(exception) en cláusulas Catch
* TrackMetric(name, value) en una tarea en segundo plano para enviar informes periódicos de métricas no asociados a eventos específicos

El código siguiente es un ejemplo de inicialización y recopilación manual de telemetría.

```java

    public class MyActivity extends Activity {

      @Override
      protected void onCreate(Bundle savedInstanceState) {

        ApplicationInsights.setup(this);
        //... other initialization code ...//
        ApplicationInsights.start();

        // track telemetry data
        TelemetryClient client = TelemetryClient.getInstance();
        HashMap<String, String> properties = new HashMap<String, String>();
        properties.put("property1", "my custom property");
        client.trackEvent("sample event", properties);
        client.trackTrace("sample trace");
        client.trackMetric("sample metric", 3);
        client.trackHandledException(new Exception("sample exception"));
      }
    }
```

## <a name="run"></a> Ejecución del proyecto

Ejecute la aplicación (MAYÚS+ 10 en Windows, CTRL+R en OS X) para generar telemetría.

## Visualización de los datos en Application Insights

Vuelva a http://portal.azure.com y busque el recurso de Application Insights.

Haga clic en **Buscar** para abrir [Búsqueda de diagnóstico][diagnostic], que es donde aparecerán los primeros eventos. Si no ve nada, espere un minuto o dos y haga clic en **Actualizar**.

![Haga clic en Búsqueda de diagnóstico](./media/app-insights-android/21-search.png)

A medida que use la aplicación, los datos aparecerán en la hoja de información general.

![Hoja de información general](./media/app-insights-android/22-oview.png)

Haga clic en cualquier gráfico para obtener más detalles. Por ejemplo, bloqueos:

![Haga clic en el gráfico de bloqueos](./media/app-insights-android/23-crashes.png)


## <a name="usage"></a>Pasos siguientes

[Seguimiento del uso de la aplicación][track]

[Búsqueda de diagnóstico][diagnostic]

[Explorador de métricas][metrics]

[Solución de problemas][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Oct15_HO3-->