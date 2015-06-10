<properties 
	pageTitle="Notas de la versión de Application Insights" 
	description="Las actualizaciones más recientes." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="sergkanz"/>
 
# Notas de la versión del SDK de Application Insights para .NET

[Uso del SDK para .NET](app-insights-start-monitoring-app-health-usage.md)

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

<!---HONumber=58-->