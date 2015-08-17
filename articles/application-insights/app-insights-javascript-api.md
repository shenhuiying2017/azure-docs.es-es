<properties 
	pageTitle="API de JavaScript del SDK de Application Insights" 
	description="Documento de referencia" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="awills"/>
 

# API de JavaScript del SDK de Application Insights

El SDK de JavaScript se carga en la página web al configurar el [seguimiento de página web](app-insights-javascript.md) en [Application Insights](https://azure.microsoft.com/services/application-insights/).

[Información general de la API y ejemplos](app-insights-api-custom-events-metrics.md)

## clase AppInsights

La parte delantera de la tienda del SDK que envía la telemetría a Application Insights.

En una página web en la que tiene que [configurar el seguimiento de página web](app-insights-javascript.md), puede utilizar la instancia `appInsights`. Por ejemplo:
    
    appInsights.trackPageView("page1");



### trackPageView

    trackPageView(name?: string, url?: string, properties?:{[string]:string}, measurements?: {[string]:number})

Registros que mostraron una página o un contenedor similar al usuario.

 | | 
---|---|---
`name` | `? string` | El nombre utilizado para identificar la página en el portal. El valor predeterminado es el título del documento.
`url` | `? string` | Una dirección URL relativa o absoluta que identifica la página o el elemento similar. El valor predeterminado es la ubicación de la ventana.
`properties` | `? {[string]:string}` | Datos adicionales que se usan para filtrar las páginas y las métricas en el portal. El valor predeterminado está vacío.
`measurements` | `? {[string]:number}` | Métricas asociadas a esta página, que se muestran en el Explorador de métricas en el portal. El valor predeterminado está vacío.


### trackEvent

    trackEvent(name: string, properties?: {[string]:string}, measurements?: {[string]:string})

Inicie una acción del usuario u otra repetición.

En el portal, puede seleccionar los eventos por nombre, y [mostrar gráficos que los cuenten o muestren medidas asociadas](app-insights-metrics-explorer.md).

También puede buscar y [mostrar eventos individuales](app-insights-diagnostic-search.md).

 | | 
---|---|---
 `name` | `string` | Identifica el evento. Se cuentan los eventos con el mismo nombre y pueden trazarse en [Explorador de métricas](app-insights-metrics-explorer.md).
`properties` | `? {[string]:string}` | Datos adicionales que se usan para filtrar las páginas y las métricas en el portal. El valor predeterminado está vacío.
`measurements` | `? {[string]:number}` | Métricas asociadas a esta página, que se muestran en el Explorador de métricas en el portal. El valor predeterminado está vacío.


### trackMetric

    trackMetric(name: string, average: number, sampleCount?: number, min?: number, max?: number)


Registre un valor numérico que no esté asociado a un evento específico. Normalmente se utiliza para enviar los informes periódicos de indicadores de rendimiento.

En el portal, puede seleccionar las métricas por nombre para [trazar los valores con el tiempo](app-insights-metrics-explorer.md). No puede buscar o ver llamadas de trackMetric individuales.

Para enviar una sola medida, utilice solo los dos primeros parámetros. Si toma medidas muy a menudo, puede reducir el ancho de banda de telemetría agregando varias medidas y enviando la media resultante en intervalos.

 | | 
---|---|---
`name` | `string` | Una cadena que identifica la métrica. En el portal, puede seleccionar las métricas para su visualización por nombre.
`average` | ` number` | Una sola medida o la media de varias medidas.
`sampleCount` | `? number` | Recuento de medidas representado por la media. El valor predeterminado es 1.
`min` | `? number` | La medida más pequeña en el ejemplo. El valor predeterminado es la media.
`max` | `? number` | La medida más grande en el ejemplo. El valor predeterminado es la media.

### trackException

    trackException(exception: Error, handledAt?: string, properties?: Object, measurements?: Object)

Registre una excepción que haya detectado. También se registran las excepciones detectadas por el explorador.

En el portal, puede [buscar el tipo de excepción y ver](app-insights-diagnostic-search.md) el tipo, el mensaje y el seguimiento de la pila de instancias individuales.

 | | 
---|---|---
`exception` | `Error` | Un error de una cláusula catch.  
`handledAt` | `? string` | El valor predeterminado es "no controlado".
`properties` | `? {[string]:string}` | Datos adicionales que se usan para filtrar las páginas y las métricas en el portal. El valor predeterminado está vacío.
`measurements` | `? {[string]:number}` | Métricas asociadas a esta página, que se muestran en el Explorador de métricas en el portal. El valor predeterminado está vacío.

### trackTrace

    trackTrace(message: string, properties?: Object, measurements?: Object)

Registre un evento de diagnóstico como la especificación o el abandono de un método.

En el portal puede buscar contenido del mensaje y [mostrar eventos individuales de trackTrace](app-insights-diagnostic-search.md). A diferencia de `trackEvent`, no puede filtrar el contenido del mensaje en el portal.

 | | 
---|---|---
`message` | `string` | Datos de diagnóstico. Pueden ser mucho más grandes que un nombre.

### vaciar

    flush()

Enviar inmediatamente toda la telemetría en cola.

Utilícela en el cierre de la ventana.


### config

    config: IConfig

Los valores que controlan cómo se envían los datos de telemetría.

    interface IConfig {
        instrumentationKey: string;
        endpointUrl: string;
        accountId: string;
        appUserId: string;
        sessionRenewalMs: number; // 30 mins. 
        sessionExpirationMs: number; // 24h. 
        maxPayloadSizeInBytes: number; // 200k
        maxBatchSizeInBytes: number; // 100k
        maxBatchInterval: number; // 15000
        enableDebug: boolean;
        autoCollectErrors: boolean; // true
        disableTelemetry: boolean; // false
        verboseLogging: boolean;
        diagnosticLogInterval: number; // 10 000
    }

Configure estos valores en [el fragmento](app-insights-javascript-api.md) que inserta en las páginas web. Busque esta línea y agregue más elementos:

    })({
      instrumentationKey: "000...000"
    });

### contexto

    context: TelemetryContext

Información que el SDK intenta extraer del entorno acerca del dispositivo, la ubicación y el usuario


## clase TelemetryContext




        /**
         * Details of the app you're monitoring.
         */
        public application: Context.Application;

        /**
         * The device the app is running on.
         */
        public device: Context.Device;

        /**
         * The user currently signed in.
         */
        public user: Context.User;

        /**
         * The user session. A session represents a series
         * of user actions. A session starts with a user action.
         * It ends when there is no user activity for 
         * sessionRenewalMs, 
         * or if it lasts longer than sessionExpirationMs.
         */
        public session: Context.Session;

        /**
         * The geographical location of the user's device,
         * if available.
         */
        public location: Context.Location;

        /**
         * Represents the user request. Operation id is used
         * to tie together related events in diagnostic search.
         */
        public operation: Context.Operation;

        /**
         * Default measurements to be attached by default to
         * all events.
         */
        public measurements: any;

        /**
         * Default properties to be attached by default to
         * all events. 
         */
        public properties: any;

        /**
         * Send telemetry object to the endpoint.
         * Returns telemetryObject.
         */
        public track(envelope: Telemetry.Common.Envelope) ;


## Código fuente abierto

Lea o contribuya al [código para el SDK](https://github.com/Microsoft/ApplicationInsights-js).

<!---HONumber=August15_HO6-->