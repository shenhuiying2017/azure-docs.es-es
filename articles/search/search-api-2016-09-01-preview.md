---
title: "API de REST de Azure Search Service versión 2016-09-01-Preview | Microsoft Docs"
description: "La API de REST de Azure Search Service, versión 2016-09-01-Preview incluye características experimentales como búsquedas de sinónimos y moreLikeThis."
services: search
documentationcenter: na
author: mhko
manager: jlembicz
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: 082c207f892fcc277d30d66c6165dd9920d3ab27
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>API de REST de Azure Search Service: versión 2016-09-01-Preview
Este artículo es la documentación de referencia de `api-version=2016-09-01-Preview`. Esta vista previa amplía la versión disponible actualmente con carácter general, [api-version=2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx), al proporcionar las siguientes características experimentales:

* [API de sinónimos](search-synonyms.md) para cargar mapas de sinónimos y ampliar la búsqueda.
* [`moreLikeThis`Parámetro de consulta](search-more-like-this.md) para buscar documentos que sean pertinentes para un documento específico.

Asegúrese de que el destino sea la versión de API preliminar `api-version=2016-09-01-Preview` para probar estas características experimentales. En el ejemplo siguiente se ilustra cómo se especifica la versión de la API de versión preliminar a la hora de realizar una consulta more-like-this.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Las características en versión preliminar están disponibles para prueba y experimentación con el fin de recabar comentarios y están sujetas a cambios. **Se recomienda encarecidamente no usar una versión preliminar de API en aplicaciones de producción.**

El servicio Azure Search está disponible en varias versiones. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.
