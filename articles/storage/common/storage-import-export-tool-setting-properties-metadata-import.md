---
title: "Configuración de propiedades y metadatos mediante Azure Import/Export | Microsoft Docs"
description: "Obtenga información sobre cómo especificar las propiedades y los metadatos que se van a establecer en los blobs de destino cuando se ejecuta la herramienta Azure Import/Export para preparar las unidades."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1ba6d157402fae0c7d7bf841d2b4e4f6b1ee1084
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Establecimiento de las propiedades y los metadatos durante el proceso de importación

Al ejecutar la herramienta Microsoft Azure Import/Export para preparar las unidades, puede especificar propiedades y metadatos que se establecerán en los blobs de destino. Siga estos pasos:

1.  Para establecer propiedades de blob, cree un archivo de texto en el equipo local que especifique los valores y nombres de propiedad.
2.  Para establecer metadatos de blob, cree un archivo de texto en el equipo local que especifique los valores y nombres de metadatos.
3.  Pase la ruta de acceso completa a uno de estos archivos o a ambos a la herramienta Azure Import/Export como parte de la operación `PrepImport`.

> [!NOTE]
>  Cuando se especifica un archivo de propiedades o metadatos como parte de una sesión de copia, esas propiedades o metadatos se establecen para cada blob que se importa como parte de esa sesión de copia. Si desea especificar otro conjunto de propiedades o metadatos para algunos de los blobs que se importan, tendrá que crear una sesión de copia independiente con archivos de propiedades o metadatos diferentes.

## <a name="specify-blob-properties-in-a-text-file"></a>Especificación de propiedades de blob en un archivo de texto

Para especificar propiedades de blob, cree un archivo de texto local e incluya código XML que especifique los nombres de propiedad como elementos y los valores de propiedad como valores. Este es un ejemplo que especifica algunos valores de propiedad:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

Guarde el archivo en una ubicación local como `C:\WAImportExport\ImportProperties.txt`.

## <a name="specify-blob-metadata-in-a-text-file"></a>Especificación de metadatos de blob en un archivo de texto

De forma similar, para especificar los metadatos de blob, cree un archivo de texto local que especifique los nombres de metadatos como elementos y los valores de metadatos como valores. Este es un ejemplo que especifica algunos valores de metadatos:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Guarde el archivo en una ubicación local como `C:\WAImportExport\ImportMetadata.txt`.

## <a name="add-the-path-to-properties-and-metadata-files-in-datasetcsv"></a>Agregar la ruta de acceso a archivos de metadatos y propiedades en dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,https://mystorageaccount.blob.core.windows.net/video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,https://mystorageaccount.blob.core.windows.net/photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,https://mystorageaccount.blob.core.windows.net/music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

## <a name="next-steps"></a>Pasos siguientes

* [Formato de archivo de propiedades y metadatos del servicio Import-Export](../storage-import-export-file-format-metadata-and-properties.md)
