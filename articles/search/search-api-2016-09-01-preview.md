---
title: API de REST de Azure Search Service versión 2016-09-01-Preview | Microsoft Docs
description: La API de REST de Azure Search Service, versión 2016-09-01-Preview incluye características experimentales como búsquedas de sinónimos y moreLikeThis.
author: mhko
manager: jlembicz
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: bbfdb2641d68f1262cc6293462c7022701357374
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
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
