---
title: "Actualización de API de REST del servicio Azure Search versión 2016-09-01 | Microsoft Docs"
description: "Actualización de API de REST del Servicio Azure Search versión 2016-09-01"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 6183fa6c-48bb-4af7-adae-4be3bc43c3ed
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: brjohnst
ms.openlocfilehash: f6a189c2e314b91c490583a86d8bacca8ec78a0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Actualización de API de REST del Servicio Azure Search versión 2016-09-01
Si usa la versión 2015-02-28 o 2015-02-28-Preview de [API de REST del servicio Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx), este artículo le ayudará a actualizar la aplicación para que use la siguiente versión de API disponible con carácter general, 2016-09-01.

La versión 2016-09-01 de la API de REST contiene algunos cambios con respecto a versiones anteriores. Se trata principalmente de cambios compatibles con versiones anteriores, por lo que cambiar el código apenas debe exigir esfuerzo, según la versión que utilizaba antes. Vea [Pasos para actualizar](#UpgradeSteps), a fin de obtener instrucciones sobre cómo cambiar el código para usar la nueva versión de API.

> [!NOTE]
> La instancia del servicio Azure Search es compatible con varias versiones de API de REST, incluida la más reciente. Puede seguir usando una versión aunque no sea la más reciente, pero es recomendable que migre el código para usar la versión más actualizada.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>Novedades de la versión 2016-09-01
La versión 2016-09-01 es la segunda versión disponible con carácter general de API de REST del servicio Azure Search. Entre las nuevas características de esta versión de API se incluyen:

* [Analizadores personalizados](https://aka.ms/customanalyzers), que le permiten tomar el control sobre el proceso de conversión de texto en tokens que se pueden indizar y buscar.
* Indizadores de [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) y [Azure Table Storage](search-howto-indexing-azure-tables.md), que permiten importar fácilmente datos del almacenamiento de Azure en Azure Search según una programación o a petición.
* [Asignaciones de campo](search-indexer-field-mappings.md), que permiten personalizar cómo los indizadores importan datos en Azure Search.
* Etiquetas ETag, que permiten actualizar las definiciones de índices, los indizadores y los orígenes de datos con seguridad de simultaneidad. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Pasos para actualizar
Si va a actualizar desde la versión 2015-02-28, probablemente no tenga que realizar cambios en el código, excepto para cambiar el número de versión. Las únicas situaciones en las que puede que tenga que modificar el código ocurren si:

* Se produce un error en el código cuando se devuelven propiedades no reconocidas en una respuesta de la API. De forma predeterminada, la aplicación debe omitir propiedades que no comprende.
* El código conserva las solicitudes de API e intenta volver a enviarlas a la nueva versión de API. Por ejemplo, esto podría suceder si la aplicación conserva tokens de continuación devueltos por la API de búsqueda (para más información, busque `@search.nextPageParameters` en la [referencia de la API de búsqueda](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)).

Si alguna de estas situaciones se le presenta, debe cambiar el código en consecuencia. De lo contrario, no debe ser necesario efectuar ningún cambio a menos que quiera empezar a usar las [nuevas características](#WhatsNew) de la versión 2016-09-01.

Si va a actualizar desde la versión 2015-02-28-Preview, lo anterior también se aplica, pero además debe tener en cuenta que algunas características de vista previa no están disponibles en la versión 2016-09-01:

* El indizador de Azure Blob Storage admite archivos CSV y blobs que contengan matrices JSON.
* Sinónimos
* Consultas de tipo "Más resultados similares"

Si el código usa estas características, no podrá actualizar a 2016-09-01 sin quitar el uso de las mismas.

> [!IMPORTANT]
> Por favor, recuerde que las versiones preliminares de las API están pensadas para realizar pruebas y evaluar, y no deben usarse en entornos de producción.
> 
> 

## <a name="conclusion"></a>Conclusión
Si necesita más información sobre el uso de API de REST del servicio Azure Search, vea la [Referencia de API](https://msdn.microsoft.com/library/azure/dn798935.aspx) recién actualizada en MSDN.

Agradecemos sus comentarios sobre Azure Search. Si tiene algún problema, no dude en pedirnos ayuda en el [foro de MSDN sobre Azure Search](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) o [StackOverflow](http://stackoverflow.com/). Si va a hacer una pregunta sobre Azure Search en StackOverflow, asegúrese de etiquetarla con `azure-search`.

Gracias por usar Búsqueda de Azure.

