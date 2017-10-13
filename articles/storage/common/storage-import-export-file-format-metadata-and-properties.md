---
title: Formato del archivo de propiedades y metadatos de Azure Import/Export | Microsoft Docs
description: "Obtenga información sobre cómo especificar metadatos y propiedades para uno o más blobs que forman parte de un trabajo de importación o exportación."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3f728ad94cdcbd32092b677f11a737ae91376720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Formato del archivo de propiedades y metadatos de Azure Import/Export
Puede especificar metadatos y propiedades para uno o más blobs como parte de un trabajo de importación o exportación. Para establecer los metadatos o las propiedades para blobs que se crean como parte de un trabajo de importación, proporcione un archivo de metadatos o propiedades en la unidad de disco duro que contiene los datos que se van a importar. Para un trabajo de exportación, los metadatos y las propiedades se escriben en un archivo de metadatos o propiedades que se incluye en la unidad de disco duro que se le devuelve.  
  
## <a name="metadata-file-format"></a>Formato del archivo de metadatos  
El formato de un archivo de metadatos es el siguiente:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|Elemento XML|Tipo|Descripción|  
|-----------------|----------|-----------------|  
|`Metadata`|Elemento raíz|Elemento raíz del archivo de metadatos.|  
|`metadata-name`|string|Opcional. El elemento XML especifica el nombre de los metadatos para el blob y su valor especifica el valor de la configuración de metadatos.|  
  
## <a name="properties-file-format"></a>Formato de archivo de propiedades  
El formato de un archivo de propiedades es el siguiente:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|Elemento XML|Tipo|Descripción|  
|-----------------|----------|-----------------|  
|`Properties`|Elemento raíz|Elemento raíz del archivo de propiedades.|  
|`Last-Modified`|string|Opcional. Hora de la última modificación del blob. Solo para trabajos de exportación.|  
|`Etag`|string|Opcional. Valor ETag del blob. Solo para trabajos de exportación.|  
|`Content-Length`|string|Opcional. Tamaño del blob en bytes. Solo para trabajos de exportación.|  
|`Content-Type`|string|Opcional. Tipo de contenido del blob.|  
|`Content-MD5`|string|Opcional. Hash MD5 del blob.|  
|`Content-Encoding`|String|Opcional. Codificación del contenido del blob.|  
|`Content-Language`|string|Opcional. Idioma del contenido del blob.|  
|`Cache-Control`|String|Opcional. Cadena de control de caché para el blob.|  

## <a name="next-steps"></a>Pasos siguientes

Vea [Set Blob Properties](/rest/api/storageservices/set-blob-properties) (Establecer propiedades del blob), [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) (Establecer metadatos del blob) y [Setting and Retrieving Properties and Metadata for Blob Resources](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) (Configuración y recuperación de propiedades y metadatos para los recursos del blob) para consultar reglas detalladas sobre cómo establecer propiedades y metadatos del blob.
