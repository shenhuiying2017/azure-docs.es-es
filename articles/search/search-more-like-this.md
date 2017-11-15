---
title: "moreLikeThis en Azure Search (versión preliminar) | Microsoft Docs"
description: "Documentación preliminar de la característica moreLikeThis (versión preliminar), expuesta en la API de REST de Azure Search."
services: search
documentationCenter: na
authors: mhko
manager: jlembicz
editor: na
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: nateko
ms.openlocfilehash: 20f6a1166d47799f15275cb5b6a9ea4934ae57e2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis en Azure Search (versión preliminar)

`moreLikeThis=[key]` es un parámetro de consulta de [API Search](https://docs.microsoft.com/rest/api/searchservice/search-documents). Al especificar el parámetro  `moreLikeThis` en una consulta de búsqueda, puede buscar documentos que sean similares al especificado por la clave de documento. Cuando se realiza una solicitud de búsqueda con `moreLikeThis`, se genera una consulta con los términos de búsqueda extraídos del documento especificado que describen mejor ese documento. La consulta generada se usa luego para realizar la solicitud de búsqueda. De forma predeterminada, se tiene en cuenta el contenido de todos los campos `searchable` a menos que se use el parámetro `searchFields` para restringir los campos. El parámetro `moreLikeThis` no se puede usar con el parámetro de búsqueda, `search=[string]`.

## <a name="examples"></a>Ejemplos 

A continuación se muestra un ejemplo de una consulta moreLikeThis. La consulta busca los documentos cuyos campos de descripción son lo más parecido al campo del documento de origen según se especifica con el parámetro `moreLikeThis`.

```  
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```  

```  
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {  
      "moreLikeThis": "1002",  
      "searchFields": "description"  
    }  
```  

## <a name="feature-availability"></a>Disponibilidad de características

La característica moreLikeThis se encuentra actualmente en versión preliminar y solo se admite en las versiones de API de versión preliminar `2015-02-28-Preview` y `2016-09-01-Preview`. Puesto que la versión de la API se especifica en la solicitud, es posible combinar API de versión preliminar y disponibles en general en la misma aplicación. Sin embargo, la versión preliminar de las API no se someten a las condiciones del Acuerdo de Nivel de Servicio y sus características pueden cambiar, por lo que no se recomienda su uso en aplicaciones de producción.