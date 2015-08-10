<properties 
	pageTitle="Notas de la versión de Application Insights" 
	description="Las actualizaciones más recientes." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sergkanz"/>
 
# Notas de la versión del SDK de Application Insights para .NET

El [SDK de Application Insights para .NET](app-insights-start-monitoring-app-health-usage.md) envía telemetría acerca de la aplicación activa a [Application Insights](http://azure.microsoft.com/services/application-insights/), donde puede analizar su uso y el rendimiento.


#### Para instalar el SDK en su aplicación

Consulte [Introducción a Application Insights para .NET](app-insights-start-monitoring-app-health-usage.md).

#### Para actualizar al SDK más reciente 

* Después de actualizar, necesitará volver combinar todas las personalizaciones realizadas en ApplicationInsights.config. Si no está seguro de si lo ha personalizado, cree un nuevo proyecto, agregue Application Insights y compare el archivo .config con el que aparece en el nuevo proyecto. Tome nota de las diferencias.
* En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes de NuGet**.
* Establezca el filtro para mostrar los paquetes instalados. 
* Seleccione **Microsoft.ApplicationInsights.Web** y elija **Actualizar**. (Esto actualizará todos los paquetes dependientes).
* Compare ApplicationInsights.config con la copia anterior. La mayoría de los cambios que verá se deben a que hemos quitado algunos módulos y hemos hecho que otros se puedan parametrizar. Restablezca las personalizaciones realizadas en el archivo anterior.
* Vuelva a generar la solución.

## Versión 1.1

- Se agregó un nuevo tipo de telemetría `DependencyTelemetry` que se puede usar para enviar información sobre las llamadas de dependencia desde una aplicación (como llamadas SQL, HTTP, etc.).
- Se agregó un nuevo método de sobrecarga `TelemetryClient.TrackDependency` que permite enviar información sobre las llamadas de dependencia.
- Se corrigió la NullReferenceException que producía el módulo de diagnósticos cuando se usaba TelemetryConfiguration.CreateDefault.

## Versión 1.0

- Se movieron los inicializadores y módulos de telemetría y módulos de telemetría de subespacios de nombres independientes al espacio de nombres `Microsoft.ApplicationInsights.Extensibility.Web` raíz.
- Se quitó el prefijo "Web" de los nombres de los inicializadores y módulos de telemetría porque ya está incluido en el espacio de nombres `Microsoft.ApplicationInsights.Extensibility.Web`.
- Se movió `DeviceContextInitializer` desde el ensamblado `Microsoft.ApplicationInsights` al ensamblado `Microsoft.ApplicationInsights.Extensibility.Web` y se convirtió en un `ITelemetryInitializer`.
- Cambie el espacio de nombres y los nombres de ensamblado de `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` a `Microsoft.ApplicationInsights.Extensibility.DependencyCollector` para mantener la coherencia con el nombre del paquete de NuGet.
- Cambie el nombre `RemoteDependencyModule` a `DependencyTrackingTelemetryModule`.
- Cambie el nombre `CustomPerformanceCounterCollectionRequest` a `PerformanceCounterCollectionRequest`.

## Versión 0.17
- Quitada la dependencia a EventSource NuGet para las aplicaciones de Framework 4.5.
- Las cookies de sesión y usuario anónimo no se generarán en el lado del servidor. Para implementar el seguimiento de usuarios y sesiones para las aplicaciones web, es necesaria la instrumentación con el SDK de JS: las cookies del SDK de JavaScript se siguen respetando. Los módulos de telemetría ```WebSessionTrackingTelemetryModule``` y ```WebUserTrackingTelemetryModule``` ya no se admiten y se han quitado del archivo ApplicationInsights.config. Tenga en cuenta que este cambio puede provocar una rectificación significativa de los recuentos de usuario y sesión, ya que ahora solo se cuentan las sesiones originadas por el usuario.
- El SDK ya no rellena OSVersion de manera predeterminada. Cuando está vacío, la canalización de Application Insights calcula SO y OSVersion según el agente del usuario. 
- Se usa un canal de persistencia optimizado para los escenarios de carga alta para el SDK de web. Problema "Espiral de la muerte" solucionado. Espiral de muerte es una condición especial en el recuento de elementos de telemetría que supera la limitación en el extremo que conducirá a reintentar después de determinados tiempo y se limitarán durante el reintento de nuevo.
- El modo de programador está optimizado para la producción. Si se deja por error no provocará una sobrecarga tan grande como antes de intentar enviar información adicional.
- El modo de programador solo se habilitará de forma predeterminada cuando la aplicación está en el depurador. Puede invalidarlo mediante la propiedad ```DeveloperMode``` de la interfaz ```ITelemetryChannel```.

## Versión 0.16 

Vista previa del 28-04-2015

- El SDK es ahora compatible con la plataforma de destino DNX para permitir la supervisión de aplicaciones del [marco de trabajo principal .NET](http://www.dotnetfoundation.org/NETCore5).
- Las instancias de ```TelemetryClient``` ya no almacenan en caché la clave de instrumentación. Ahora, si no estableció la clave de instrumentación explícitamente en ```TelemetryClient```, ```InstrumentationKey``` devolverá null. Corrige un problema en el que cuando establece ```TelemetryConfiguration.Active.InstrumentationKey``` después de que ya se recopilaran algunos datos de telemetría, los módulos de telemetría, como el recopilador de dependencias, la recopilación de datos de solicitudes web y el recolector de contadores de rendimiento usarán la nueva clave de instrumentación.

## Version 0.15

- Nueva propiedad ```Operation.SyntheticSource``` ahora disponible en ```TelemetryContext```. Ahora puede marcar los elementos de telemetría como "no es un tráfico de usuario real" y especificar cómo se generó este tráfico. Por ejemplo, si define esta propiedad, puede distinguir el tráfico de la automatización de pruebas del tráfico de prueba de carga.
- La lógica de canal se movió al NuGet independiente denominado Microsoft.ApplicationInsights.PersistenceChannel. El canal predeterminado se denomina ahora InMemoryChannel.
- El nuevo método ```TelemetryClient.Flush``` permite vaciar elementos de telemetría del búfer de forma sincrónica.

## Versión 0.13

No existen notas de la versión para versiones anteriores.

 

<!---HONumber=July15_HO5-->