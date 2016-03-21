<properties
   pageTitle="Versiones de API de Búsqueda de Azure | Microsoft Azure | API de búsqueda"
   description="Directiva de versión para las API de REST de Búsqueda de Azure y la biblioteca de cliente en el SDK de .NET."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="03/08/2016"
   ms.author="brjohnst"/>

# Versiones de API en Búsqueda de Azure

Búsqueda de Azure implementa las actualizaciones de características de forma regular. A veces, aunque no siempre, estas actualizaciones nos obligan a publicar una nueva versión de la API para mantener la compatibilidad con versiones anteriores. La publicación de una nueva versión le permite controlar cuándo y cómo integrar en su código las actualizaciones del servicio de búsqueda.

Por lo general, intentamos publicar versiones nuevas solo cuando sea necesario, ya que el proceso de actualizar el código para que use una nueva versión de API puede suponer cierto esfuerzo. Solo publicaremos una versión nueva si es necesario cambiar algún aspecto de la API de tal manera que se interrumpe la compatibilidad con versiones anteriores. Esto puede deberse a correcciones en las características existentes o a características nuevas que cambian el área expuesta de la API.

La misma regla se aplica a las actualizaciones del SDK. El SDK de Búsqueda de Azure sigue las reglas de [Versionamiento semántico](http://semver.org/), lo que significa que la versión tiene tres partes: principal, secundaria y número de compilación (por ejemplo, 1.1.0). Solo se lanzará una versión principal nueva del SDK en caso de que haya cambios que impidan la compatibilidad con versiones anteriores. En caso de actualizaciones de características que no producen interrupciones, incrementaremos la versión secundaria y, en caso de correcciones de errores, solo aumentaremos la versión de compilación.

##Instantánea de las versiones actuales 

A continuación se incluye una instantánea de las versiones actuales de todas las interfaces de programación para Búsqueda de Azure. Encontrará guías y otros detalles en secciones posteriores de este documento.

Interfaces|Versión principal más reciente|Estado
----------|-------------------------|------
[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1\.1|Disponibilidad general, lanzado en febrero de 2016
[API de REST de servicio](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02-28|Disponibilidad general
[Versión preliminar de la API de REST de servicio](search-api-2015-02-28-preview.md)|2015-02-28-Preview|Vista previa
[API de REST de administración](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08-19|Disponibilidad general

Para las API de REST, es necesario incluir `api-version` en cada llamada. Esto permite establecer fácilmente una versión específica de destino, como una versión preliminar de API. En el ejemplo siguiente se muestra cómo se especifica el parámetro `api-version`:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] Aunque cada solicitud tiene una `api-version`, se recomienda usar la misma versión para todas las solicitudes de API. Esto ocurre especialmente cuando las nuevas versiones de API incorporan atributos u operaciones que las versiones anteriores no reconocen. Debe evitarse la mezcla de versiones de API, ya que puede tener consecuencias no deseadas.
> 
Las versiones de la API de REST de servicio y de la API de REST de administración son independientes entre sí. Cualquier semejanza entre los números de versión es mera coincidencia.

Las API de disponibilidad general (o GA) puede usarse en entornos de producción y están sujetas a contratos de nivel de servicio de Azure. Las versiones preliminares tienen características experimentales que no siempre se migran a una versión de disponibilidad general. **Se recomienda encarecidamente no usar una versión preliminar de API en aplicaciones de producción.**

##Guía de la versión del SDK

Cada versión del SDK de .NET tiene como destino una versión específica de la API de REST de servicio. Las características se implementan primero en la API de REST y, después, en el SDK.

El SDK de .NET está ahora disponible con carácter general y ya se está trabajando en la próxima versión. En la tabla siguiente incluye información sobre las próximas versiones del SDK para que pueda hacerse una idea de las novedades futuras.

Versión de SDK de .NET|Versión de API de REST|Características|Fecha estimada
----------------|----------------|--------|---
1\.1|2015-02-28|Sintaxis de consulta de Lucene|Febrero de 2016
2\.x-preview|2015-02-28-Preview|Analizadores personalizados, indexador de blobs de Azure, asignaciones de campos, etiquetas ETag|Las características empezarán a incluirse en el primer trimestre de 2016
2\.x|Nueva versión de API de disponibilidad general|Igual que 2.x-preview|Poco después de que se complete 2.x-preview

##Acerca de las versiones preliminar y de disponibilidad general

Búsqueda de Azure siempre publica previamente características experimentales primero a través de la API de REST y, después, a través de las versiones preliminares del SDK de .NET. Encontrará una lista de las características en versión preliminar en [Novedades de Búsqueda de Azure](search-latest-updates.md).

No se garantiza la migración de las características en versión preliminar a una versión de disponibilidad general. Mientras que las características de una versión de disponibilidad general se consideran estables y es poco probable que cambien, a excepción de pequeñas mejoras y correcciones compatibles con versiones anteriores, las características en versión preliminar están disponibles para la experimentación y la realización de pruebas, con el fin de recabar comentarios sobre el diseño y la implementación de la característica.

Sin embargo, dado que las características en versión preliminar están sujetas a cambios, no se recomienda escribir código de producción que dependa de versiones preliminares. Si usa una versión preliminar antigua, se recomienda que migre a la versión de disponibilidad general (GA).

- Para el SDK de .NET: encontrará indicaciones para la migración de código en [Actualizar el SDK de .NET](search-dotnet-sdk-migration.md).
- Para la API de REST: encontrará indicaciones para la migración de código en [Transición de la versión preliminar a la versión de disponibilidad general](search-transition-from-preview.md).

La disponibilidad general significa que Búsqueda de Azure está ahora sujeta al contrato de nivel de servicio. Encontrará dicho contrato en [Contratos de nivel de servicio de Búsqueda de Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

<!---HONumber=AcomDC_0309_2016-->