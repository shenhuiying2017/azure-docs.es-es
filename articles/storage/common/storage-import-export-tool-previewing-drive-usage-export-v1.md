---
title: "Vista previa de uso de disco para un trabajo de exportación de Azure Import/Export (versión 1) | Microsoft Docs"
description: "Obtenga información sobre cómo conseguir una vista previa de la lista de blobs que ha seleccionado para un trabajo de exportación en el servicio Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 7707d744-7ec7-4de8-ac9b-93a18608dc9a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 6ec74ae0b0931f3fed99a43f4f7e58f9d425b138
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Vista previa de uso de disco para un trabajo de exportación
Antes de crear un trabajo de exportación, debe elegir un conjunto de blobs que se van a exportar. El servicio Microsoft Azure Import/Export permite usar una lista de rutas de acceso de blob o prefijos de blob para representar los blobs que ha seleccionado.  
  
A continuación, debe determinar cuántas unidades necesita enviar. La herramienta Import/Export proporciona el comando `PreviewExport` para obtener una vista previa del uso de la unidad para los blobs que ha seleccionado, en función del tamaño de las unidades que se van a usar.

## <a name="command-line-parameters"></a>Parámetros de línea de comandos

Puede usar los parámetros siguientes al usar el comando `PreviewExport` de la herramienta Import/Export.

|Parámetro de línea de comandos|Descripción|  
|--------------------------|-----------------|  
|**/logdir:**&lt;DirectorioRegistro\>|Opcional. El directorio de registro. Los archivos de registro detallados se escribirán en este directorio. Si no se especifica un directorio de registro, se usará el directorio actual como directorio de registro.|  
|**/sn:**&lt;NombreCuentaAlmacenamiento\>|Obligatorio. El nombre de la cuenta de almacenamiento para el trabajo de exportación.|  
|**/sk:**&lt;ClaveCuentaAlmacenamiento\>|Necesario únicamente si no se especifica un contenedor SAS. La clave de cuenta para la cuenta de almacenamiento correspondiente al trabajo de exportación.|  
|**/csas:**&lt;SasContenedor\>|Requerido únicamente si no se especifica una clave de cuenta de almacenamiento. El contenedor SAS para enumerar los blobs que se van a exportar en el trabajo de exportación.|  
|**/ExportBlobListFile:**&lt;ArchivoListaBlobExportación\>|Obligatorio. Ruta de acceso al archivo XML que contiene una lista de rutas de acceso de blob o prefijos de ruta de acceso para los blobs que se van a exportar. El formato de archivo usado en el elemento `BlobListBlobPath` de la operación [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) de la API de REST del servicio Import/Export.|  
|**/DriveSize:**&lt;TamañoUnidad\>|Obligatorio. El tamaño de las unidades que se van a usar para un trabajo de exportación, *por ejemplo*, 500 GB o 1,5 TB.|  

## <a name="command-line-example"></a>Ejemplo de línea de comandos

En el siguiente ejemplo se muestra el comando `PreviewExport`:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
El archivo de lista de blobs de exportación puede contener nombres de blob y prefijos de blob, como se muestra aquí:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

La herramienta Azure Import/Export enumera todos los blobs que se van a exportar y calcula cómo empaquetarlos en unidades del tamaño especificado, teniendo en cuenta cualquier sobrecarga necesaria; después calcula el número de unidades necesarias para albergar los blobs y la información de uso de la unidad.  
  
Este es un ejemplo de la salida, con los registros informativos omitidos:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="next-steps"></a>Pasos siguientes

* [Referencia de la herramienta Azure Import/Export](../storage-import-export-tool-how-to-v1.md)
