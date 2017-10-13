---
title: "Flujo de trabajo de ejemplo para preparar unidades de disco duro para un trabajo de importación de Azure Import/Export | Microsoft Docs"
description: "Vea un tutorial para conocer el proceso completo de preparación de las unidades para un trabajo de importación en el servicio Azure Import/Export."
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
ms.date: 04/07/2017
ms.author: muralikk
ms.openlocfilehash: 60139ff36b66432620591ceaf201e046ad30217f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación

Este artículo le guiará por el proceso completo de preparar las unidades para un trabajo de importación.

## <a name="sample-data"></a>Datos de ejemplo

Este ejemplo importa los datos siguientes en una cuenta de Azure Storage denominada `mystorageaccount`:

|Ubicación|Descripción|Tamaño de los datos|
|--------------|-----------------|-----|
|H:\Video\ |Una colección de vídeos|12 TB|
|H:\Photo\ |Una colección de fotos|30 GB|
|K:\Temp\FavoriteMovie.ISO|Una imagen de disco Blu-ray™|25 GB|
|\\\bigshare\john\music\ |Una colección de archivos de música en un recurso compartido de red|10 GB|

## <a name="storage-account-destinations"></a>Destinos de la cuenta de almacenamiento

El trabajo de importación importará los datos en los siguientes destinos en la cuenta de almacenamiento:

|Origen|Directorio virtual o blob de destino|
|------------|-------------------------------------------|
|H:\Video\ |video/|
|H:\Photo\ |photo/|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |music|

Con esta asignación, el archivo `H:\Video\Drama\GreatMovie.mov` se importará al blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Determinación de los requisitos de la unidad de disco duro

A continuación, para determinar cuántas unidades de disco duro se necesitan, calcule el tamaño de los datos:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

En este ejemplo, dos unidades de disco duro de 8 TB deberían ser suficientes. Sin embargo, dado que el directorio de origen `H:\Video` tiene 12 TB de datos y la capacidad de la unidad de disco duro es de solo 8 TB, podrá especificarlo de la siguiente manera en el archivo **driveset.csv**:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
La herramienta distribuirá datos en las dos unidades de disco duro de forma optimizada.

## <a name="attach-drives-and-configure-the-job"></a>Conexión de unidades y configuración del trabajo
Conectará ambos discos a la máquina y creará volúmenes. Luego, cree el archivo **dataset.csv**:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Además, puede establecer los metadatos siguientes para todos los archivos:

* **UploadMethod:** servicio Microsoft Azure Import/Export
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

Para establecer los metadatos de los archivos importados, cree un archivo de texto, `c:\WAImportExport\SampleMetadata.txt`, con el siguiente contenido:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

También puede establecer algunas propiedades para el blob `FavoriteMovie.ISO`:

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control:** no-cache

Para establecer estas propiedades, cree un archivo de texto, `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Ejecución de la herramienta Azure Import/Export (WAImportExport.exe)

Ahora está listo para ejecutar la herramienta Azure Import/Export para preparar las dos unidades de disco duro.

**Para la primera sesión:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Si es necesario agregar más datos, cree otro archivo de conjunto de datos (con el mismo formato que Initialdataset).

**Para la segunda sesión:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Cuando se hayan completado las sesiones de copia, podrá desconectar las dos unidades del equipo de copia y enviarlas al centro de datos de Azure adecuado. Cargará los dos archivos de diario, `<FirstDriveSerialNumber>.xml` y `<SecondDriveSerialNumber>.xml`, cuando cree el trabajo de importación en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

* [Preparación de unidades de disco duro para un trabajo de importación](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Quick reference for frequently used commands for import jobs](../storage-import-export-tool-quick-reference.md) (Referencia rápida para comandos usados con frecuencia)
