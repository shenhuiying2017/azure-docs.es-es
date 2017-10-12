---
title: "Configuración de propiedades y metadatos mediante Azure Import/Export (versión 1) | Microsoft Docs"
description: "Obtenga información sobre cómo especificar las propiedades y los metadatos que se van a establecer en los blobs de destino cuando se ejecuta la herramienta Azure Import/Export para preparar las unidades. Se refiere a la versión 1 de la herramienta Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 77bdaa5559de86cd1de9f30e70656e47fd5719e2
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
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Creación de una sesión de copia que incluya los archivos de propiedades o metadatos  
Al ejecutar la herramienta Azure Import/Export para preparar el trabajo de importación, especifique el archivo de propiedades en la línea de comandos mediante el parámetro `PropertyFile`. Especifique el archivo de metadatos en la línea de comandos mediante el parámetro `/MetadataFile`. Este es un ejemplo que especifica ambos archivos:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>Pasos siguientes

* [Formato de archivo de propiedades y metadatos del servicio Import-Export](../storage-import-export-file-format-metadata-and-properties.md)
