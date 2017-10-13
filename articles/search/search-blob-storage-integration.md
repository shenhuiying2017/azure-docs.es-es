---
title: Agregar Azure Search a Blob Storage | Microsoft Docs
description: "Creación de un índice en código con Búsqueda de Azure y la API de REST de HTTP."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
ms.service: search
ms.topic: article
ms.date: 05/04/2017
ms.author: ashmaka
ms.openlocfilehash: 15469e8a2d28bdf00d6e8d8c9f823c51975ee90e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="searching-blob-storage-with-azure-search"></a>Búsqueda en Blob Storage con Azure Search

La búsqueda en los diversos tipos de contenido almacenado en Azure Blob Storage puede ser un problema difícil de resolver. Pero gracias a Azure Search, es posible indexar y buscar en el contenido de los blobs con tan solo unos clics. Para buscar en Blob Storage, es necesario aprovisionar un servicio de Azure Search. Encontrará los diversos límites de los servicios y los planes de tarifa de Azure Search en la [página de precios](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>¿Qué es Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) es un servicio de búsqueda que permite que los programadores agreguen fácilmente experiencias eficaces de búsqueda de texto completo para aplicaciones web y móviles. Como servicio, Azure Search elimina la necesidad de administrar las infraestructuras de búsqueda al mismo tiempo que ofrece un [tiempo de actividad del 99,9 % a través del contrato de nivel de servicio](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Indexación y búsqueda de formatos de documentos empresariales
Con compatibilidad para la [extracción de documentos](https://aka.ms/azsblobindexer) en Azure Blob Storage, puede indexar el contenido siguiente:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Mediante la extracción de texto y metadatos de estos tipos de archivos, puede realizar búsquedas en varios formatos de archivo con una sola búsqueda. 

## <a name="search-through-your-blob-metadata"></a>Buscar en los metadatos del blob
Una práctica común que permite ordenar fácilmente blobs con cualquier tipo de contenido consiste en indexar los metadatos personalizados, así como las propiedades del sistema para cada uno de los blobs. De esta manera, la información de todos los blobs se indexa independientemente del tipo de documento. A continuación, puede continuar con la ordenación, filtrado y búsqueda de contenido mediante facetas en todo el contenido de Blob Storage.

[Obtenga más información sobre la indexación de metadatos del blob.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Búsqueda de imágenes
Ahora, la búsqueda de texto completo, la navegación por facetas y las funcionalidades de ordenación de Azure Search se pueden aplicar a los metadatos de las imágenes almacenadas en blobs.

Si estas imágenes se han procesado previamente mediante [Computer Vision API](https://www.microsoft.com/cognitive-services/computer-vision-api) de Microsoft Cognitive Services, es posible indexar el contenido visual de cada imagen, incluido el reconocimiento de escritura a mano y OCR. Estamos trabajando para agregar OCR y otras funcionalidades de procesamiento de imágenes directamente en Azure Search. Si estas funcionalidades le interesan, envíe una solicitud a través de [UserVoice](https://aka.ms/azsuv) o [envíenos un correo electrónico](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Indexar y buscar en blobs JSON
Es posible configurar Azure Search para que extraiga el contenido estructurado que se encuentra en los blobs que contienen JSON. Azure Search puede leer blobs JSON y analizar el contenido estructurado en los campos correspondientes de un documento de Azure Search. Azure Search también puede tomar los blobs que contienen una matriz de objetos JSON y asignar cada elemento a un documento independiente de Azure Search.

Actualmente, el análisis de JSON no se puede configurar en el portal. [Obtenga más información sobre el análisis de JSON en Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Inicio rápido
Es posible agregar Azure Search a blobs directamente desde la página del portal de Blob Storage.

![](./media/search-blob-storage-integration/blob-blade.png)

Haga clic en **Agregar Azure Search** para iniciar un flujo en el que puede seleccionar un servicio existente de Azure Search o crear uno nuevo. Si crea un servicio, saldrá de la experiencia del portal de su cuenta de almacenamiento. Puede regresar a la página del portal de almacenamiento y volver a seleccionar la opción **Agregar Azure Search**, donde puede seleccionar el servicio existente.

### <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre el indexador de blobs de Azure Search en la [documentación](https://aka.ms/azsblobindexer) completa.
