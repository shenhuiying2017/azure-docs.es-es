---
title: Actualización a la versión más reciente de la API REST del servicio Azure Search | Microsoft Docs
description: Actualización a la versión más reciente de la API REST del servicio Azure Search
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 3848f317fd6d760961756f132edf9cbcb5f431ee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181977"
---
# <a name="upgrading-to-the-latest-azure-search-service-rest-api-version"></a>Actualización a la versión más reciente de la API REST del servicio Azure Search
Si usa la versión anterior de la [API REST del servicio Azure Search](https://docs.microsoft.com/rest/api/searchservice/), este artículo le ayudará a actualizar la aplicación para que use la versión más reciente de la API disponible con carácter general, 2017-11-11.

La versión 2017-11-11 de la API REST contiene algunos cambios con respecto a versiones anteriores. Se trata principalmente de cambios compatibles con versiones anteriores, por lo que cambiar el código apenas debe exigir esfuerzo, según la versión que utilizaba antes. Vea [Pasos para actualizar](#UpgradeSteps), a fin de obtener instrucciones sobre cómo cambiar el código para usar la nueva versión de API.

> [!NOTE]
> La instancia del servicio Azure Search es compatible con varias versiones de API de REST, incluida la más reciente. Puede seguir usando una versión aunque no sea la más reciente, pero es recomendable que migre el código para usar la versión más actualizada.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>Novedades de la versión 2017-11-11
La versión 2017-11-11 es la versión disponible con carácter general más reciente de la API REST del servicio Azure Search. Entre las nuevas características de esta versión de API se incluyen:

* [Sinónimos](search-synonyms.md). La nueva característica de sinónimos permite definir términos equivalentes y ampliar el ámbito de la consulta.
* [Compatibilidad para la indexación eficaz de blobs de texto](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). El nuevo modo de análisis `text` para los indexadores de blobs de Azure mejora significativamente el rendimiento de la indexación.
* [API de estadísticas del servicio](https://aka.ms/azure-search-stats) Vea el uso y los límites de los recursos actuales en Azure Search con esta nueva API.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Pasos para actualizar
Si va a actualizar desde la versión de disponibilidad general 2015-02-28 o 2016-09-01, probablemente no tenga que realizar cambios en el código, excepto para cambiar el número de versión. Las únicas situaciones en las que puede que tenga que modificar el código ocurren si:

* Se produce un error en el código cuando se devuelven propiedades no reconocidas en una respuesta de la API. De forma predeterminada, la aplicación debe omitir propiedades que no comprende.
* El código conserva las solicitudes de API e intenta volver a enviarlas a la nueva versión de API. Por ejemplo, esto podría suceder si la aplicación conserva tokens de continuación devueltos por la API de búsqueda (para más información, busque `@search.nextPageParameters` en la [referencia de la API de búsqueda](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Si alguna de estas situaciones se le presenta, debe cambiar el código en consecuencia. De lo contrario, no debe ser necesario efectuar ningún cambio a menos que quiera empezar a usar las [nuevas características](#WhatsNew) de la versión 2017-11-11.

Si va a actualizar desde la versión de la API de versión preliminar, lo anterior también se aplica, pero además debe tener en cuenta que algunas características de vista previa no están disponibles en la versión 2017-11-11:

* El indizador de Azure Blob Storage admite archivos CSV y blobs que contengan matrices JSON.
* Consultas de tipo "Más resultados similares"

Si el código usa estas características, no podrá actualizar a 2017-11-11 sin quitar el uso de estas.

> [!IMPORTANT]
> Por favor, recuerde que las versiones preliminares de las API están pensadas para realizar pruebas y evaluar, y no deben usarse en entornos de producción.
> 
> 

## <a name="conclusion"></a>Conclusión
Si necesita más información sobre el uso de API de REST del servicio Azure Search, vea la [Referencia de API](https://docs.microsoft.com/rest/api/searchservice/) recién actualizada en MSDN.

Agradecemos sus comentarios sobre Azure Search. Si tiene algún problema, no dude en pedirnos ayuda en el [foro de MSDN sobre Azure Search](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) o [StackOverflow](http://stackoverflow.com/). Si va a hacer una pregunta sobre Azure Search en StackOverflow, asegúrese de etiquetarla con `azure-search`.

Gracias por usar Azure Search.

