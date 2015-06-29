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
	ms.author="awills"/>
 
# Notas de la versión del SDK de Application Insights para Java

El [SDK de Application Insights para Java](app-insights-java-get-started.md) envía telemetría acerca de la aplicación activa a [Application Insights](http://azure.microsoft.com/services/application-insights/), donde puede analizar su uso y el rendimiento.

#### Para instalar el SDK en su aplicación

Consulte [Introducción al SDK para Java](app-insights-java-get-started.md).

#### Para actualizar al SDK más reciente 

Después de actualizar, necesitará volver combinar todas las personalizaciones realizadas en ApplicationInsights.xml. Realice una copia del mismo para compararlo con el nuevo archivo.

*Si utiliza Maven o Gradle*

1. Si especifica un número de versión concreto en pom.xml o build.gradle, actualícelo.
2. Actualice las dependencias del proyecto.

*De lo contrario*

* Descargue las versiones de las [bibliotecas de Azure para Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html) más recientes y reemplace las antiguas. 
 
Compare los archivos ApplicationInsights.xml antiguo y nuevo. Muchos de los cambios que verá son porque hemos agregado y quitado módulos. Restablezca las personalizaciones que había realizado.

## Versión 0.9.6
- Haga que el SDK de Java sea compatible con servlet v2.5 y HttpClient pre-v4.3
- Adición de compatibilidad para los interceptores de Java EE
- Eliminación de dependencias redundantes del appender Logback

## Versión 0.9.5  

- Corrección de un problema donde los eventos personalizados no están correlacionados con sesiones o usuarios debido a errores de análisis de cookies.  
- Lógica mejorada para resolver la ubicación del archivo de configuración ApplicationInsights.xml.
- Quitado el seguimiento de sesiones y usuarios (esto solo lo hará el SDK del cliente).

## Versión 0.9.4

- Compatible con la recopilación de contadores de rendimiento de equipos Windows de 32 bits.
- Compatible con el seguimiento manual de dependencias mediante una nueva API de método ```trackDependency```.
- Posibilidad de etiquetar un elemento de telemetría como sintético agregando una propiedad ```SyntheticSource``` al elemento de informe.
 

<!---HONumber=58_postMigration-->