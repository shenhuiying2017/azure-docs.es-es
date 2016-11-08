---
title: Recursos de Application Insights independientes para desarrollo, pruebas y producción
description: Supervisar el rendimiento y el uso de la aplicación en diferentes fases de desarrollo
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2016
ms.author: awills

---
# Separación de recursos de Application Insights
¿Se debería enviar la telemetría procedente de diferentes componentes y versiones de la aplicación a distintos recursos de Application Insights o se debería combinar todo en uno? En este artículo se examinan los procedimientos recomendados y las técnicas necesarias.

En primer lugar, hay que comprender la pregunta. Application Insights almacena y procesa los datos recibidos de la aplicación en un *recurso* de Azure. Cada recurso se identifica con una *clave de instrumentación* (iKey). En la aplicación, la clave se proporciona al SDK de Application Insights para que este pueda enviar los datos que recopila al recurso adecuado. La clave se puede proporcionar en el código o en ApplicationInsights.config. Al cambiar la clave en el SDK, puede dirigir los datos a distintos recursos.

En un caso sencillo, cuando se crea el código para una nueva aplicación, también se crea un recurso en Application Insights. En Visual Studio, esto se hace automáticamente en el cuadro de diálogo *Nuevo proyecto*.

Si se trata de un sitio web de gran volumen, es posible que se implemente en más de una instancia del servidor.

En escenarios más complejos, posee un sistema que consta de varios componentes: por ejemplo, un sitio web y un procesador de back-end.

## Cuándo usar claves iKey independientes
Estas son algunas instrucciones generales:

* Cuando tenga una unidad de aplicación de implementación independiente que se ejecute en un conjunto de instancias de servidor que se puedan escalar vertical u horizontalmente con independencia de los demás componentes, normalmente la asignará a un único recurso. Es decir, tendrá una única clave de instrumentación (iKey).
* En cambio, estas son algunas razones para usar claves iKey independientes:
  * Lea fácilmente métricas independientes procedentes de componentes independientes.
  * Mantenga la telemetría de bajo volumen separada de la de gran volumen, para que la limitación, las cuotas y el muestreo de una transmisión no afecten a la otra.
  * Separe las alertas, la exportación y las configuraciones de elemento de trabajo.
  * Propague [límites](app-insights-pricing.md#limits-summary) como la cuota de telemetría, la limitación y el número de pruebas web.
  * El código en desarrollo y pruebas debería enviar datos a una clave iKey independiente del sello de producción.

Muchas de las experiencias de portal de Application Insights están diseñadas con estas instrucciones en mente. Por ejemplo, los servidores ven segmentos en la instancia del servidor, que da por supuesto que la telemetría sobre un componente lógico puede proceder de varias instancias de servidor.

## IKey única
Cuando se envía telemetría procedente de varios componentes a una clave iKey única:

* Agregue una propiedad a toda la telemetría que le permita segmentar y filtrar por la identidad del componente. El identificador del rol se agrega automáticamente a la telemetría desde instancias de rol de servidor, pero en otros casos puede usar un [inicializador de telemetría](app-insights-api-filtering-sampling.md#add-properties) para agregar la propiedad.
* Actualice los SDK de Application Insights en los distintos componentes al mismo tiempo. La telemetría de una clave iKey debería originarse en la misma versión del SDK.

## Claves IKey independientes
Cuando existen varias claves iKey para diferentes componentes de la aplicación:

* Cree un [panel](app-insights-dashboards.md) para obtener una vista de la telemetría clave desde la aplicación lógica, combinada desde los diferentes componentes de la aplicación. Los paneles se pueden compartir, para que diferentes equipos puedan trabajar con una sola vista de sistema lógico.
* Organice los [grupos de recursos](app-insights-resources-roles-access-control.md) en el nivel de equipo. Los permisos de acceso se asignan por grupo de recursos, e incluyen permisos para configurar las alertas.
* Use [plantillas de Azure Resource Manager y PowerShell](app-insights-powershell.md) para ayudar a administrar artefactos, como reglas de alerta y pruebas web.

## Claves IKey independientes para desarrollo y pruebas, y producción
Para facilitar el cambio automático de clave cuando se lanza la aplicación, establezca la clave iKey en el código, en lugar de en ApplicationInsights.config.

### <a name="dynamic-ikey"></a> Copia de la clave de instrumentación
Establezca la clave en un método de inicialización, como global.aspx.cs en un servicio de ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

En este ejemplo, las ikeys para los distintos recursos se colocan en diferentes versiones del archivo de configuración web. Si cambia el archivo de configuración web (algo que se puede hacer como parte del script de lanzamiento), se intercambiará el recurso de destino.

### Páginas web
La iKey también se usa en las páginas web de su aplicación, en el [script que obtuvo desde la hoja Inicio rápido](app-insights-javascript.md). En vez de codificarla literalmente en el script, genérela desde el estado del servidor. Por ejemplo, en una aplicación ASP.NET:

*JavaScript en Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## Creación de un recurso de Application Insights adicional
Si decide separar la telemetría para los diferentes componentes de la aplicación, o para diferentes sellos (desarrollo, prueba y producción) del mismo componente, tendrá que crear un recurso de Application Insights.

En el [portal.azure.com](https://portal.azure.com), agregue un recurso de Application Insights:

![Haga clic en Nuevo, Application Insights.](./media/app-insights-separate-resources/01-new.png)

* El **tipo de aplicación** afecta a lo que ve en la hoja de información general y las propiedades disponibles en el [explorador de métricas](app-insights-metrics-explorer.md). Si no ve el tipo de aplicación, elija uno de los tipos web para páginas web.
* El **grupo de recursos** resulta práctico para administrar propiedades como el como el [control de acceso](app-insights-resources-roles-access-control.md). Puede usar grupos de recursos independientes para desarrollo, prueba y producción.
* La **suscripción** es su cuenta de pago de Azure.
* La **ubicación** es donde se guardan los datos. Actualmente no se puede cambiar.
* **Agregar al panel** coloca un icono de acceso rápido al recurso en la página principal de Azure.

El recurso tarda unos segundos en crearse. Verá una alerta cuando esté listo.

(Puede escribir un [script de PowerShell](app-insights-powershell-script-create-resource.md) para crear un recurso automáticamente.)

## Obtención de la clave de instrumentación
La clave de instrumentación identifica al recurso que ha creado.

![Haga clic en Essentials y elija la clave de instrumentación, CTRL + C](./media/app-insights-separate-resources/02-props.png)

Necesita las claves de instrumentación de todos los recursos a los que la aplicación enviará datos.

<!---HONumber=AcomDC_0831_2016-->