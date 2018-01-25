---
title: Versiones de API de Azure Search | Microsoft Docs
description: "Directiva de versión para las API de REST de Azure Search y la biblioteca de cliente en el SDK de .NET."
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/15/2018
ms.author: brjohnst
ms.openlocfilehash: 1a7f5b9c53d3258e5f8eda40401c61a85971d8c7
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="api-versions-in-azure-search"></a>Versiones de API en Azure Search
Azure Search implementa las actualizaciones de características de forma regular. A veces, aunque no siempre, estas actualizaciones nos obligan a publicar una nueva versión de la API para mantener la compatibilidad con versiones anteriores. La publicación de una nueva versión le permite controlar cuándo y cómo integrar en su código las actualizaciones del servicio de búsqueda.

Por lo general, intentamos publicar versiones nuevas solo cuando sea necesario, ya que el proceso de actualizar el código para que use una nueva versión de API puede suponer cierto esfuerzo. Solo publicaremos una versión nueva si es necesario cambiar algún aspecto de la API de tal manera que se interrumpe la compatibilidad con versiones anteriores. Esto puede deberse a correcciones en las características existentes o a características nuevas que cambian el área expuesta de la API.

La misma regla se aplica a las actualizaciones del SDK. El SDK de Azure Search sigue las reglas de [Versionamiento semántico](http://semver.org/) , lo que significa que la versión tiene tres partes: principal, secundaria y número de compilación (por ejemplo, 1.1.0). Solo se lanzará una versión principal nueva del SDK en caso de que haya cambios que impidan la compatibilidad con versiones anteriores. En caso de actualizaciones de características que no producen interrupciones, incrementaremos la versión secundaria y, en caso de correcciones de errores, solo aumentaremos la versión de compilación.

> [!NOTE]
> La instancia del servicio Azure Search es compatible con varias versiones de API de REST, incluida la más reciente. Puede seguir usando una versión aunque no sea la más reciente, pero es recomendable que migre el código para usar la versión más actualizada. Cuando se usa la API de REST, debe especificar la versión de API en cada solicitud realizada a través del parámetro api-version. Al usar el SDK. NET, la versión del SDK que usa determina la versión correspondiente de la API de REST. Si usa un SDK anterior, puede seguir ejecutando ese código sin realizar ningún cambio, incluso si el servicio se ha actualizado para admitir una versión más reciente de la API.

## <a name="snapshot-of-current-versions"></a>Instantánea de las versiones actuales
A continuación se incluye una instantánea de las versiones actuales de todas las interfaces de programación para Azure Search.

| Interfaces | Versión principal más reciente | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |3.0 |Disponibilidad general, lanzado en noviembre de 2016 |
| [Versión preliminar del SDK de .NET](https://aka.ms/search-sdk-preview) |4.0.1-preview |Versión preliminar, publicada en mayo de 2017 |
| [API de REST de servicio](https://docs.microsoft.com/rest/api/searchservice/) |2016-09-01 |Disponibilidad general |
| [Versión preliminar de la API de REST de servicio](search-api-2016-09-01-preview.md) |2016-09-01-Preview |Vista previa |
| [SDK de administración de .NET](https://aka.ms/search-mgmt-sdk) |2.0 |Disponibilidad general |
| [API de REST de administración](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Disponibilidad general |

Para las API de REST, es necesario incluir `api-version` en cada llamada. Esto permite establecer fácilmente una versión específica de destino, como una versión preliminar de API. En el ejemplo siguiente se muestra cómo se especifica el parámetro `api-version` :

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2016-09-01

> [!NOTE]
> Aunque cada solicitud tiene una `api-version`, se recomienda usar la misma versión para todas las solicitudes de API. Esto ocurre especialmente cuando las nuevas versiones de API incorporan atributos u operaciones que las versiones anteriores no reconocen. Debe evitarse la mezcla de versiones de API, ya que puede tener consecuencias no deseadas.
>
> Las versiones de la API de REST de servicio y de la API de REST de administración son independientes entre sí. Cualquier semejanza entre los números de versión es mera coincidencia.

Las API de disponibilidad general (o GA) puede usarse en entornos de producción y están sujetas a contratos de nivel de servicio de Azure. Las versiones preliminares tienen características experimentales que no siempre se migran a una versión de disponibilidad general. **Se recomienda encarecidamente no usar una versión preliminar de API en aplicaciones de producción.**

## <a name="about-preview-and-generally-available-versions"></a>Acerca de las versiones preliminar y de disponibilidad general
Azure Search siempre publica previamente características experimentales primero a través de la API de REST y, después, a través de las versiones preliminares del SDK de .NET.

No se garantiza la migración de las características en versión preliminar a una versión de disponibilidad general. Mientras que las características de una versión de disponibilidad general se consideran estables y es poco probable que cambien, a excepción de pequeñas mejoras y correcciones compatibles con versiones anteriores, las características en versión preliminar están disponibles para la experimentación y la realización de pruebas, con el fin de recabar comentarios sobre el diseño y la implementación de la característica.

Sin embargo, dado que las características en versión preliminar están sujetas a cambios, no se recomienda escribir código de producción que dependa de versiones preliminares. Si usa una versión preliminar antigua, se recomienda que migre a la versión de disponibilidad general (GA).

Para el SDK de .NET: encontrará indicaciones para la migración de código en [Actualización a la versión 1.1 del SDK de .NET para Búsqueda de Azure](search-dotnet-sdk-migration.md).

La disponibilidad general significa que Azure Search está ahora sujeta al contrato de nivel de servicio. Encontrará dicho contrato en el [Acuerdo de Nivel de Servicio de Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
