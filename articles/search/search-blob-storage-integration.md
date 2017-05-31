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
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 0cd0cbb05c465d32a9ef02f9350ebdf9ccea36c5
ms.contentlocale: es-es
ms.lasthandoff: 05/08/2017

---

# <a name="searching-blob-storage-with-azure-search"></a>Búsqueda en Blob Storage con Azure Search

La búsqueda en los diversos tipos de contenido almacenado en Azure Blob Storage puede ser un problema difícil de resolver. Pero gracias a Azure Search, es posible indexar y buscar en el contenido de los blobs con tan solo unos clics. Para buscar en Blob Storage, es necesario aprovisionar un servicio de Azure Search. Encontrará los diversos límites de los servicios y los planes de tarifa de Azure Search en la [página de precios](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>¿Qué es Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) es una solución de búsqueda que permite que los programadores agreguen fácilmente experiencias eficaces de búsqueda de texto completo para aplicaciones web y móviles. Como servicio, Azure Search elimina la necesidad de administrar las infraestructuras de búsqueda al mismo tiempo que ofrece un [tiempo de actividad del 99,9 % a través del contrato de nivel de servicio](https://aka.ms/azuresearchsla).

Con una compatibilidad avanzada con 56 idiomas, Azure Search puede analizar el contenido y controlar de forma inteligente las construcciones específicas del idioma. Azure Search también proporciona las herramientas necesarias para crear una experiencia de usuario de búsqueda completa. Es fácil agregar características como la navegación por facetas, las sugerencias de búsqueda con escritura anticipada y el resaltado de referencias para las interfaces de usuario que emplean Azure Search. Para obtener información sobre las características de Azure Search, puede leer la [documentación](https://aka.ms/azsearchdocs) del servicio.

## <a name="crack-open-and-search-through-the-content-of-enterprise-document-formats"></a>Extraer y buscar contenido de formatos de documentos empresariales
Gracias a la compatibilidad con la [extracción de documentos](https://aka.ms/azsblobindexer) en Azure Blob Storage, Azure Search puede indexar el contenido de diversos tipos de archivo almacenados en blobs:
- PDF
- Microsoft Office: DOC/DOCX, XLSX/XLS, PPTX/PPT y MSG (correos electrónicos de Outlook)
- HTML
- Texto sin formato

Mediante la extracción de texto y metadatos de estos tipos de archivo, es fácil buscar en varios formatos de archivo con una sola consulta para encontrar los documentos más relevantes, independientemente del tipo. Al indexar el contenido y los metadatos de documentos de Microsoft Office, archivos PDF y mensajes de correo electrónico, es posible compilar una sólida solución de administración de contenido empresarial mediante el uso de Blob Storage y Azure Search.

## <a name="search-through-your-blob-metadata"></a>Buscar en los metadatos del blob
Una práctica común que permite ordenar fácilmente blobs con cualquier tipo de contenido consiste en indexar los metadatos de blob personalizados definidos por el usuario, así como las propiedades del sistema para cada uno de los blobs. De esta manera, la información de cada uno de los blobs está indexada, independientemente del tipo de documento del blob, por lo que es fácil establecer un orden y facetas en todo el contenido de Blob Storage.

[Obtenga más información sobre la indexación de metadatos del blob.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Búsqueda de imágenes
Ahora, la búsqueda de texto completo, la navegación por facetas y las funcionalidades de ordenación de Azure Search se pueden aplicar a los metadatos de las imágenes almacenadas en blobs.

Si estas imágenes se han procesado previamente mediante [Computer Vision API](https://www.microsoft.com/cognitive-services/computer-vision-api) de Microsoft Cognitive Services, es posible indexar el contenido visual de cada imagen, incluido el reconocimiento de escritura a mano y OCR. Estamos trabajando para agregar OCR y otras funcionalidades de procesamiento de imágenes directamente en Azure Search. Si estas funcionalidades le interesan, envíe una solicitud a través de [UserVoice](https://aka.ms/azsuv) o [envíenos un correo electrónico](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Indexar y buscar en blobs JSON
Es posible configurar Azure Search para que extraiga el contenido estructurado que se encuentra en los blobs que contienen JSON. Azure Search puede leer blobs JSON y analizar el contenido estructurado en los campos correspondientes de un documento de Azure Search. Azure Search también puede tomar los blobs que contienen una matriz de objetos JSON y asignar cada elemento a un documento independiente de Azure Search.

Tenga en cuenta que actualmente el análisis de JSON no se puede configurar en el portal. [Obtenga más información sobre el análisis de JSON en Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Inicio rápido
Es posible agregar Azure Search a blobs directamente desde la hoja del portal de Blob Storage.

![](./media/search-blob-storage-integration/blob-blade.png)

Al hacer clic en la opción "Agregar Azure Search", se inicia un flujo en el que puede seleccionar un servicio existente de Azure Search o crear un servicio. Si crea un servicio, saldrá de la experiencia del portal de su cuenta de almacenamiento. Deberá regresar a la hoja del portal de almacenamiento y volver a seleccionar la opción "Agregar Azure Search", donde puede seleccionar el servicio existente.

### <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre el indexador de blobs de Azure Search en la [documentación](https://aka.ms/azsblobindexer) completa.

