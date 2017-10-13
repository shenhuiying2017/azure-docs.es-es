---
title: Formato del archivo de manifiesto de Azure Import/Export | Microsoft Docs
description: "Obtenga información acerca del formato del archivo de manifiesto de la unidad que describe la asignación entre los blobs de Azure Blob Storage y un archivo de la unidad en un trabajo de importación o exportación del servicio Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f3119e1c-2c25-48ad-8752-a6ed4adadbb0
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: c1857eb94fba13c30e7f07669616f5d0ab9953f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-importexport-service-manifest-file-format"></a>Formato del archivo de manifiesto de servicio de Azure Import/Export
El archivo de manifiesto de la unidad describe la asignación entre los blobs de Azure Blob Storage y los archivos de la unidad que componen un trabajo de importación o exportación. En el caso de una operación de importación, el archivo de manifiesto se crea como parte del proceso de preparación de la unidad y se almacena en la unidad antes de que esta se envíe al centro de datos de Azure. Durante una operación de exportación, el servicio Azure Import/Export crea y almacena el manifiesto en la unidad.  
  
Tanto en los trabajos de importación como en los de exportación, el archivo de manifiesto de la unidad se almacena en la unidad de importación o exportación; no se transmite al servicio mediante ninguna operación de API.  
  
A continuación se describir el formato general de un archivo de manifiesta de la unidad:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>Elementos y atributos XML del manifiesto

Los elementos de datos y atributos del formato XML del manifiesto de la unidad se especifican en la tabla siguiente.  
  
|Elemento XML|Tipo|Descripción|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Elemento raíz|Elemento raíz del archivo de manifiesto. Los restantes elementos del archivo están debajo de este elemento.|  
|`Version`|Attribute, String|Versión del archivo de manifiesto.|  
|`Drive`|Elemento XML anidado|Contiene el manifiesto de cada unidad.|  
|`DriveId`|string|Identificador único de la unidad. Para encontrar el identificador de la unidad es preciso consultar su número de serie. El número de serie de la unidad normalmente también está impreso en la parte exterior de la unidad. El elemento `DriveID` debe aparecer antes de todos los elementos `BlobList` en el archivo de manifiesto.|  
|`StorageAccountKey`|string|Se requiere para trabajos de importación si, y solo si, no se ha especificado `ContainerSas`. La clave de la cuenta de Azure Storage asociada al job.<br /><br /> Este elemento se omite del manifiesto en las operaciones de exportación.|  
|`ContainerSas`|string|Se requiere para trabajos de importación si, y solo si, no se ha especificado `StorageAccountKey`. El SAS del contenedor para acceder a los blobs asociados al trabajo. Consulte [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) para conocer su formato. Este elemento se omite del manifiesto en las operaciones de exportación.|  
|`ClientCreator`|String|Especifica que el cliente que creó el archivo XML. El servicio Import/Export no interpreta este valor.|  
|`BlobList`|Elemento XML anidado|Contiene una lista de los blobs que forman parte del trabajo de importación o exportación. Todos los blobs de una lista de blobs comparten los mismos metadatos y propiedades.|  
|`BlobList/MetadataPath`|string|Opcional. Especifica la ruta de acceso relativa de un archivo en el disco que contiene los metadatos predeterminados que se establecerán los blobs de la lista en una operación de importación. Opcionalmente, estos metadatos se pueden reemplazar en función del blob.<br /><br /> Este elemento se omite del manifiesto en las operaciones de exportación.|  
|`BlobList/MetadataPath/@Hash`|Attribute, String|Especifica el valor de hash MD5 con codificación Base16 del archivo de metadatos.|  
|`BlobList/PropertiesPath`|string|Opcional. Especifica la ruta de acceso relativa de un archivo en el disco que contiene las propiedades predeterminadas que se establecerán los blobs de la lista en una operación de importación. Opcionalmente, estas propiedades se pueden reemplazar en función del blob.<br /><br /> Este elemento se omite del manifiesto en las operaciones de exportación.|  
|`BlobList/PropertiesPath/@Hash`|Attribute, String|Especifica el valor de hash MD5 con codificación Base16 del archivo de propiedades.|  
|`Blob`|Elemento XML anidado|Contiene información sobre todos los blobs de todas las listas de blobs.|  
|`Blob/BlobPath`|string|Identificador URI relativo para el blob, comienza por el nombre del contenedor. Si el blob está en un contenedor raíz, debe comenzar por `$root`.|  
|`Blob/FilePath`|string|Especifica la ruta de acceso relativa al archivo en la unidad. En el caso de los trabajos de exportación, la ruta de acceso del blob se usará para la ruta de acceso de archivo, en caso de que sea posible; *p. ej.*, `pictures/bob/wild/desert.jpg` se exportará a `\pictures\bob\wild\desert.jpg`. Sin embargo, das las limitaciones de los nombres NTFS, un blob puede exportarse a un archivo con una ruta de acceso que no se parezca a la ruta de acceso de dicho blob.|  
|`Blob/ClientData`|string|Opcional. Contiene comentarios del cliente. El servicio Import/Export no interpreta este valor.|  
|`Blob/Snapshot`|DateTime|Opcional para trabajos de exportación. Especifica el identificador de instantáneas de la instantánea de un blob exportado.|  
|`Blob/Length`|Entero|Especifica la longitud total del blob, en bytes. El valor puede llegar a ser de hasta 200 GB en el caso de un blob en bloques y de hasta 1 TB en un blob en páginas. En el caso de un blob en páginas, este valor debe ser múltiplo de 512.|  
|`Blob/ImportDisposition`|string|Opcional en los trabajos de importación, se omite en los trabajos de exportación. Especifica la forma en que el servicio Import/Export debe controlar el caso en un trabajo de importación en el que ya existe un blob con el mismo nombre. Si no se especifica el valor en el manifiesto de importación, el valor predeterminado es `rename`.<br /><br /> Los valores de este elemento incluyen:<br /><br /> -   `no-overwrite`: si ya hay un blob de destino con el mismo nombre, en la operación de importación se omitirá la importación de este archivo.<br />-   `overwrite`: el archivo recién importado sobrescribe completamente los blobs de destino existentes.<br />-   `rename`: el nuevo blob se cargará con un nombre modificado.<br /><br /> Esta es la regla de cambio de nombre:<br /><br /> - Si el nombre del blob no contiene un punto, se genera un nuevo nombre anexando `(2)` al nombre original del blob; si este nuevo nombre también entra en conflicto con un nombre de blob existente, se anexa `(3)` en lugar de `(2)`, y así sucesivamente.<br />- Si el nombre del blob contiene un punto, la parte que sigue al último punto se considera el nombre de la extensión. Igual que en el procedimiento anterior, `(2)` se inserta antes del último punto para generar un nuevo nombre; si este nombre entra en conflicto con otro, el servicio prueba con `(3)`, `(4)`, etc. hasta encontrar uno que no entre en conflicto.<br /><br /> Estos son algunos ejemplos:<br /><br /> El nombre del blob `BlobNameWithoutDot` se cambiará a:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> El nombre del blob `Seattle.jpg` se cambiará a:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Elemento XML anidado|Se requiere para un blob en páginas.<br /><br /> En el caso de una operación de importación, especifica una lista de los intervalos de bytes de un archivo que se van a importar. Cada intervalo de páginas se describe mediante un desplazamiento y una longitud en el archivo de origen que describe el intervalo de páginas, junto con un hash MD5 de la región. Se requiere el atributo `Hash` de un intervalo de páginas. El servicio validará que el hash de los datos del blob coincide con el hash MD5 calculado a partir del intervalo de páginas. Se puede usar cualquier número de intervalos de páginas para describir un archivo para una importación y su tamaño total no puede superar 1 TB. Todos los intervalos de página deben ordenarse por desplazamiento y no se permiten superposiciones.<br /><br /> En el caso de una operación de exportación, especifica el conjunto de intervalos de bytes de un blob que se han exportado a la unidad.<br /><br /> En conjunto, los intervalos de páginas solo pueden cubrir los subintervalos de un archivo o blob.  La parte restante del archivo, la que no está cubierta por ningún intervalo de páginas, queda en espera y su contenido puede ser indefinido.|  
|`PageRange`|Elemento XML|Representa un intervalo de páginas.|  
|`PageRange/@Offset`|Attribute, Integer|Especifica el desplazamiento en el archivo de transferencia y el blob en el que comienza el intervalo de páginas especificado. Este valor debe ser múltiplo de 512.|  
|`PageRange/@Length`|Attribute, Integer|Especifica la longitud del intervalo de páginas. Este valor debe ser múltiplo de 512 y no puede supera los 4 MB.|  
|`PageRange/@Hash`|Attribute, String|Especifica el valor de hash MD5 con codificación Base16 del intervalo de páginas.|  
|`BlockList`|Elemento XML anidado|Se requiere para un blob en bloques con bloques con nombre.<br /><br /> En el caso de una operación de importación, la lista de bloques especifica un conjunto de bloques que se importarán en Azure Storage. En el caso de una operación de exportación, la lista de bloques especifica en qué lugar del archivo del disco de exportación se ha almacenado cada bloque. Cada bloque se describe mediante un desplazamiento en el archivo y una longitud de bloque; a cada bloque se le asigna un nombre en función de un atributo de identificador de bloque y contiene un hash MD5 para el bloque. Para describir un blob se pueden usar hasta 50 000 bloques.  Todos los bloques se deben solicitar por desplazamiento y, en conjunto, deben cubrir el intervalo completo del archivo, *, es decir,*, no debe haber espacio entre los bloques. Si el blob no tiene más de 64 MB, los identificadores de cada bloque deben estar todos ausentes o todos presentes. Los identificadores de bloque deben ser cadenas con codificación Base64. Para ver otros requisitos de los identificadores de bloque, consulte [Put Block](/rest/api/storageservices/put-block).|  
|`Block`|Elemento XML|Representa un bloque.|  
|`Block/@Offset`|Attribute, Integer|Especifica el desplazamiento en que comienza el bloque especificado.|  
|`Block/@Length`|Attribute, Integer|Especifica el número de bytes del bloque; este valor no debe tener más de 4 MB.|  
|`Block/@Id`|Attribute, String|Especifica una cadena que representa el identificador del bloque.|  
|`Block/@Hash`|Attribute, String|Especifica el hash MD5 con codificación Base16 del bloque.|  
|`Blob/MetadataPath`|String|Opcional. Especifica la ruta de acceso relativa de un archivo de metadatos. Durante una importación, los metadatos se establecen en el blob de destino. Durante una operación de exportación, los metadatos del blob se almacenan en el archivo de metadatos de la unidad.|  
|`Blob/MetadataPath/@Hash`|Attribute, String|Especifica el hash MD5 con codificación Base16 del archivo de metadatos del blob.|  
|`Blob/PropertiesPath`|String|Opcional. Especifica la ruta de acceso relativa de un archivo de propiedades. Durante una importación, las propiedades se establecen en el blob de destino. Durante una operación de exportación, las propiedades del blob se almacenan en el archivo de propiedades de la unidad.|  
|`Blob/PropertiesPath/@Hash`|Attribute, String|Especifica el hash MD5 con codificación Base16 del archivo de propiedades del blob.|  
  
## <a name="next-steps"></a>Pasos siguientes
 
* [API de REST de Storage Import/Export](/rest/api/storageimportexport/)
